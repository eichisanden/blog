+++
categories = ["技術メモ"]
date = "2019-06-02T23:30:00+09:00"
description = "PostgreSQL JDBC DriverのServer Side Prepared Statementの挙動を調べた"
tags = ["Postgresql", "Java"]
thumbnail = "images/eye-catch/default.png"
title = "PostgreSQL JDBC DriverのServer Side Prepared Statementの挙動を調べた"
+++

昔から当たり前のようにJDBCでPreparedStatemnetを使っていますが実は全然分かっていなかったので調べてみました。

## 前提

動作確認に使用したのは以下のバージョン。

- PostgreSQL 11.3
- PostgreSQL JDBC Driver 42.2.5
- openjdk 1.8.0_192

今回、テストのためテーブルを作成しました。

```sql
postgres=# create table test(id integer primary key, name text);
CREATE TABLE

postgres=# insert into test values(1, 'yamada'),(2,'tanaka');
INSERT 0 2
```

## PREPAREとEXECUTE

https://www.postgresql.jp/document/11/html/sql-prepare.html

まずはPostgreSQLのPREPAREから。実際に使ったことがなくて色々初めて知りました。

```sql
postgres=# PREPARE select_test(int) AS
postgres-#   SELECT * FROM test WHERE id = $1;
PREPARE
```

- PREPAREを実行すると指定したSQLが実行されるのではなく、構文解析が行われる。
- パラメータは$1のように指定する。
- 好きな名前をつけられる。今回はselect_testという名前で登録しました。

登録したPREPAREは`pg_prepared_statements`を検索して確認できます。ちなみに同一セッション内でのみ有効なのでセッションが終わると消滅します。

```sql
postgres=# select * from pg_prepared_statements;
    name     |              statement              |         prepare_time          | parameter_types | from_sql
-------------+-------------------------------------+-------------------------------+-----------------+----------
 select_test | PREPARE select_test(int) AS        +| 2019-05-31 16:29:47.580902+00 | {integer}       | t
             |   SELECT * FROM test WHERE id = $1; |                               |                 |
(1 row)
```

登録したPREPAREはEXECUTEで実行できます。$1と定義したパラメータは引数として渡します。

```
postgres=# EXECUTE select_test(1);
 id |  name
----+--------
  1 | yamada
(1 row)

postgres=# EXECUTE select_test(2);
 id |  name
----+--------
  2 | tanaka
(1 row)
```

試しに型が一致しないパラメータを渡してみます。
当たり前ですが期待通りエラーになりました。

```
postgres=# EXECUTE select_test('a');
ERROR:  invalid input syntax for integer: "a"
LINE 1: EXECUTE select_test('a');
```

少し話は逸れますが、PREPARE文を登録した後、ALTER文で列の定義を変更するとPREPAREで定義した内容とずれるのでEXECUTE実行時に`cached plan must not change result type`でエラーになります。こうなった場合はセッションを切ってクリアするか、PREPAREを再作成する必要があります。実際に出くわしたが一瞬なんだか分からなかったのでメモしておきます。

```
postgres=# alter table test add column mail_address text;
ALTER TABLE
postgres=#
postgres=# EXECUTE select_test(1);
ERROR:  cached plan must not change result type
postgres=#
``` 

## PostgreSQL JDBC DriverのPrepared Statement

次はJDBCです。PreparedStatementを実行したらPREPARE文が登録されているだろうと思って、PostgreSQL側を確認してみてもPREPARE文は作成されていなくて少々混乱しました。

https://github.com/pgjdbc/pgjdbc/blob/master/docs/documentation/94/server-prepare.md
https://jdbc.postgresql.org/documentation/head/server-prepare.html

GitHubと公式サイトを読んでやっと分かりましたが、デフォルトでは同じSQLを5回実行してはじめてPREPAREを登録するようです。  
考えてみれば、単発のSQLをいちいちPREPAREに登録するのは逆に効率が悪いので、そりゃそうだよなと思いました。

```java
  // org.postgresql.PGProperty.javaから抜粋
  /**
   * Sets the default threshold for enabling server-side prepare. A value of {@code -1} stands for
   * forceBinary
   */
  PREPARE_THRESHOLD("prepareThreshold", "5",
      "Statement prepare threshold. A value of {@code -1} stands for forceBinary"),
```

実際どうなのか検証してみます。

```java
public static void main(String[] args) throws Exception {

    final String URL = "jdbc:postgresql://localhost:5432/postgres";

    try (Connection conn = DriverManager.getConnection(URL, "postgres", "");
            PreparedStatement ps = conn.prepareStatement("select id, name from test where id = ?")) {

        PGStatement pgstmt = ps.unwrap(PGStatement.class);
        System.out.println("PrepareThresholdは" + pgstmt.getPrepareThreshold());

        for (int i = 1; i <= 5; i++) {
            ps.setInt(1, i);

            System.out.println(i + "回目: isUseServerPrepare = " + pgstmt.isUseServerPrepare());

            try (ResultSet psrs = ps.executeQuery()) {
                while (psrs.next()) {
                    //System.out.println(psrs.getInt("id"));
                }
            }

        }
    }
}
```

予想どおり5回目でServerPrepareを使うように切り替わりました。

```
PrepareThresholdは5
1回目: isUseServerPrepare = false
2回目: isUseServerPrepare = false
3回目: isUseServerPrepare = false
4回目: isUseServerPrepare = false
5回目: isUseServerPrepare = true
```

5回目のタイミングで`pg_prepared_statements`テーブルを調べると、`S_1`という名前でPREPAREが登録されていました。

|name|statement                              |prepare_time              |parameter_types|from_sql|
|:---|:--------------------------------------|:-------------------------|:--------------|:------:|
|S_1 |select id, name from test where id = $1|2019-06-03 00:48:40.508182|{integer}      |false   |

今度は先ほどのコードに`pgstmt.setPrepareThreshold(1)`を追加して最初からPREPAREが使われるようにしてみます。

```
PGStatement pgstmt = ps.unwrap(PGStatement.class);
pgstmt.setPrepareThreshold(1);
System.out.println("PrepareThresholdは" + pgstmt.getPrepareThreshold());
```

はい、1回目からPREPAREに登録されました。性能的に誤差なので最初から大量に同じSQLを実行すると分かっている時は設定しても良いかな程度でしょうか。

```
PrepareThresholdは1
1回目: isUseServerPrepare = true
2回目: isUseServerPrepare = true
3回目: isUseServerPrepare = true
4回目: isUseServerPrepare = true
5回目: isUseServerPrepare = true
```

## おわりに

PostgreSQLのPREPAREとJDBCの世界のPreparedStatementが一致していなくて混乱しましたが調べてみてスッキリしました。
