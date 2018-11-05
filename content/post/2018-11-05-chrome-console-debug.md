+++
draft = true
thumbnail = ""
tags = []
categories = []
date = "2018-11-05T13:17:11+09:00"
title = "2018 11 05 Chrome Console Debug"
description = ""
+++

https://developers.google.com/web/tools/chrome-devtools/console/?hl=ja

- $(selector)  
指定された CSS セレクターに一致する最初の要素を返します。
document.querySelector() のショートカットです。
ただし、jQueryを使っているサイトではそちらが使われるので使えないケースが多いと思います。
その場合は $$()[0]で代用すれば良いでしょう。

- $$(selector)  
指定された CSS セレクターに一致するすべての要素の配列を返します。 document.querySelectorAll() のエイリアスです。
返される配列は

$x()	指定された XPath に一致する要素の配列を返します。

inspect() 関数では、パラメータとして DOM 要素または JavaScript 参照を使用します。DOM 要素を指定した場合は、DevTools で [Elements] パネルが開き、その要素が表示されます。JavaScript 参照を指定した場合は、[Profile] パネルが開きます。


最後に使用した 5 つの要素とオブジェクトは、簡単にアクセスできるように変数に格納されます。コンソール内からこれらの要素にアクセスするには、$0～4 を使用します。コンピュータでは、カウントが 0 から開始されます。つまり、最新のアイテムは $0、最も古いアイテムは $4 です。


コマンドライン API リファレンス

$_ は、直前に評価された式の値を返します。


コマンドライン API リファレンス
Andi Smith
By Andi Smith
Andi is a contributor to WebFundamentals
 
Meggin Kearney
By Meggin Kearney
Meggin is a Tech Writer
コマンドライン API には、一般的なタスクを実行するための便利な関数のコレクションが含まれています。これらのタスクには、DOM 要素の選択と調査、読み取り可能な形式でのデータの表示、プロファイラの起動と停止、DOM イベントの監視などがあります。

注: この API は、コンソール内でのみ利用できます。ページ上のスクリプトからコマンドライン API にアクセスすることはできません。

$_
$_ は、直前に評価された式の値を返します。

次の例では、単純な式（2 + 2）が評価されています。次に $_ プロパティが評価され、同じ値が含まれています。

$_ は直前に評価された式

次の例では、評価された式に最初は名前の配列が含まれています。配列の長さを調べるために $_.length を評価すると、$_ に保存されている値が変わり、最後に評価された式の 4 になります。

新しいコマンドが評価されると、$_ が変わる

$0～$4
$0、$1、$2、$3、$4 コマンドは、[Elements] パネル内で調査された最後の 5 つの DOM 要素または [Profiles] パネルで選択された最後の 5 つの JavaScript ヒープ オブジェクトの参照履歴として動作します。$0 は直前に選択された要素または JavaScript オブジェクトを返し、$1 はその前に選択されたものを返す、のようになります。




copy(object)


debug(function)
指定した関数が呼び出されると、[Sources] パネル上でデバッガーが起動されてその関数内で中断するため、コードをステップ実行してデバッグできます。



undebug(fn)


dir(object)
dir(object) は、指定されたオブジェクトのすべてのプロパティをオブジェクト スタイルのリストで表示します。このメソッドは、コンソール API の console.dir() メソッドのエイリアスです。

次の例は、同じ要素を表示するためにコマンドラインで直接 document.body を評価した場合と dir() を使用した場合の違いを示しています。




inspect(object/function)
inspect(object/function) は、指定された要素またはオブジェクトを適切なパネルで開いて選択します。DOM 要素の場合は [Elements] パネル、JavaScript ヒープ オブジェクトの場合は [Profiles] パネルが使用されます。

次の例では、document.body を [Elements] パネルで開きます。

    inspect(document.body);


getEventListeners(object)
getEventListeners(object) は、指定されたオブジェクトに登録されているイベント リスナーを返します。戻り値は、各登録済みタイプ（"click"、"keydown" など）の配列が含まれているオブジェクトです。各配列のメンバーは、各タイプに登録されているリスナーを記述するオブジェクトです。たとえば、次のメソッドは、ドキュメント オブジェクトに登録されているすべてのイベント リスナーのリストを表示します。

    getEventListeners(document);


keys(object)
keys(object) は、指定されたオブジェクトに属しているプロパティの名前を含む配列を返します。同じプロパティに関連付けられている値を取得するには、values() を使用します。

たとえば、アプリケーションで次のオブジェクトが定義されているとします。

    var player1 = { "name": "Ted", "level": 42 }
player1 がグローバルな名前空間に定義されていると仮定して（簡潔にするため）、コンソールで keys(player1) と values(player1) を入力すると、結果は次のようになります。




monitorEvents(object[, events])
指定されたイベントのいずれかが指定されたオブジェクトで発生すると、イベント オブジェクトがコンソールに出力されます。監視する 1 つのイベント、イベントの配列、またはイベントの定義済みコレクションにマッピングされた一般的なイベントの「タイプ」のいずれかを指定できます。次の例を参照してください。

次のメソッドは、window オブジェクトに対するすべての resize イベントを監視します。

    monitorEvents(window, "resize");

    
console.group()
console.groupEnd()

console.groupCollapsed()

console.time("Array initialize");
var array= new Array(1000000);
for (var i = array.length - 1; i >= 0; i--) {
    array[i] = new Object();
};
console.timeEnd("Array initialize");
