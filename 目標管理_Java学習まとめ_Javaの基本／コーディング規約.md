# 目標管理

* 本資料は目標管理の自己啓発で学習した内容まとめた資料である。
* Stream-Rで使用されてる内容を精査する。

## 1.Java言語の基本
### 1-1. 基本（備忘録）
* final変数<br>
    * finalがついた変数は、一度定義したら二度と値を変更することができない。
    * 変数と定数を区別するために使用する。

* アクセス指定子
    * protected：同じパッケージか、そのサブクラスからしか呼び出せない。

### 1-2. 応用
* ガーベージコレクタ<br>
ガーベージコレクタの起動を促すには「System.gc();」と記述する。 <br>
※必ず起動されるわけではない。（ガーベージコレクタは、基本的に自分自身の判断で呼び出されるため、どのタイミングで呼び出されるかはわからない）
しかし、この処理を行うことにより「適切なタイミングで」呼びだされることが期待される。
⇒Stream-Rで使ってない気がする。

## 2.Javaコーディング規約 for Java8
### 2-1. 基本（備忘録）
コーディング規約を遵守することで以下が得られる。
* ソフトウェアメンテナンスにおける、可読性・保守性・拡張性の向上
* 問題を起こしやすい実装を未然に回避することによる、品質・生産性の向上
* 標準規約を通して得られる一般的な実装知識やノウハウ（＝学習効果）

コーディングの心得5か条<br>
1. 見やすさを重視せよ
1. ネーミングはわかりやすく
1. サンプルを鵜呑みにしない
1. 同じコードを二度書かない
1. 役割は一つに

### 2-2. ネーミング規約
* メソッド<br>
    * 型がbooleanの場合は「"is"+属性名」にする<br>
        > 例：public boolean isAsleep(){<br>
        > 　　}

* 変数全般
    * 定数は全てstatic finalとし、すべて大文字、区切りは"_"とする<br>
        > 例：private static final String SYSTEM_NAME = "販売管理システム"

    * 変数名に固有名詞が含まれる場合、先頭をのぞき、単語の区切り以外に大文字を使用してもよい<br>
        > 例：private String thisIsIPAddress;

### 2-2. コーディング規約
* 全般
    * 推奨されないAPI を使用しない<br>
    アノテーション@Deprecatedで指定されたメソッドは利用しないこと

    * 宣言は適切な権限で行うこと（public, protected, private）

* フォーマット
    * ifなどの条件式でbooleanの変数を比較しない<br>
        > 良い例：javaif (hasStock)<br>
        > 悪い例：javaif (hasStock == true)

    * 不等号の向きは左向き（ < 、 <= ）にする<br>
        > 良い例：javaif (from <= x && x <= to) {<br>
        > 悪い例：javaif (x >= from && x <= to) {

* 変数全般
    * リテラルは使用しない<br>
    リテラルとは、コード中に表現が定数として直接現れており、記号やリストで表現することができないものを指す（数値、文字列両方含む通称マジックナンバー）。<br>
    コードの可読性・保守性の低下を防ぐために、リテラル定数（static final フィールド）を使用すること<br>
    例外：-1,0,1 等をカウント値としてループ処理等で使用するような場合<br>
        > 良い例：private static final double ONE_MILE_METRE = 1609.344;<br>
        　　　    　public double mileToMetre(double mi) {<br>
        　　　　   　　return mi * ONE_MILE_METRE;<br>
        　　　　   }<br>
        > 悪い例：public double mileToMetre(double mi) {<br>
        　　　　   　　return mi * ONE_MILE_METRE;<br>
        　　　　   }

* 継承
    * スーパークラスのメソッドをオーバーライドするときは@Overrideアノテーションを指定する<br>
        > 良い例：public class Abs {<br>
        > 　　　　　protected void process() {<br>
        > 　　　　}<br>
        > <br>
        > 　　　　public class Sub extends Abs {<br>
        > 　　　　    @Override<br>
        > 　　　　    protected void process() <br>
        > 　　　　}

        > 悪い例：@Overrideアノテーションの指定がない<br>

* メンバー順序
    * 以下の順で記述する
    1. staticフィールド
    1. staticイニシャライザー
    1. staticメソッド
    1. フィールド
    1. イニシャライザー
    1. コンストラクター
    1. メソッド

    * 同一カテゴリー内では以下の可視性の順で記述する
    1. public
    1. protected
    1. パッケージprivate
    1. private

* インスタンス
    * オブジェクト同士はequals()メソッドで比較する(==演算子で比較しない)<br>
    ※ただしEnumの場合は==演算子を利用して比較する


### 2-3. パフォーマンス
パフォーマンスを考慮したJavaのコーディングについて記載する。
#### 2-3-1. Stream API
Java8で追加されたStream APIでの記述は、可読性も高く、簡潔に書けるが、パフォーマンス・性能面で注意が必要な場合がある。

Listの処理を行う際、拡張for文で処理する場合はIteratorインスタンスが1つだけ生成されるが、Stream APIで処理する場合、最初のStreamインスタンスに加え、各中間処理ごとにもStreamインスタンスが生成され、その分の性能劣化が懸念される。
以下に処理例と計測結果を記載する。

* 拡張for文
    >List<String> list = //数値文字列のList<br>
    >List<String> resultList = new ArrayList<>();<br>
    >for (String string : list) {<br>
    >    if (string.endsWith("0")) {<br>
    >        resultList.add(string);<br>
    >    }<br>
    >}<br>
    >
    >return resultList;<br>

* Stream API
    >List<String> list = //数値文字列のList<br>
    >List<String> resultList = list.stream()<br>
    >    .filter(s -> s.endsWith("0"))<br>
    >    .collect(Collectors.toList());<br>
    >return resultList;<br>

* 計測結果

|  処理するListの件数  |  拡張for文 (ms)  |  StreamAPI (ms)  |
| :---: | :---: | :---: |
|  100万件  |  7  |  9  |
|  1,000万件  |  88  |  114  |
|  1億件  |  949  |  1026  |
|  2億件  |  1822  |  2081  |

#### 2-3-2. ラムダ式
Java8で追加されたラムダ式・メソッド参照・コンストラクタ参照は、匿名クラスを利用するよりも効率的。
以下にComparatorを生成した場合の計測結果を記載する。

* 匿名クラス
    >Comparator<String> c = new Comparator<String>() {<br>
    >@Override<br>
    >public int compare(String o1, String o2) {<br>
    >   return o1.compareToIgnoreCase(o2);<br>
    >}<br>
    >};<br>

* ラムダ式
    >Comparator<String> c = (o1, o2) -> o1.compareToIgnoreCase(o2);

* メソッド参照
    >Comparator<String> c = String::compareToIgnoreCase;

* 計測結果

| 処理するListの件数 | 匿名クラス (ms) | ラムダ式 (ms) | メソッド参照 (ms) |
| :---: | :---: | :---: | :---: |
| 10億回 | 380 | 0(計測不能) | 0(計測不能) |
| 100億回 | 6,374 | 0(計測不能) | 0(計測不能) |
| 1京回 | (30秒以上) | 14 | 10 |