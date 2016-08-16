# ConciseYaccLex
It's a simple design and it's easy to understand. It's for embedding simple lexical analyzer to your application.

## 動機と目的

lex & yaccの構文ルール記述は簡潔で分かり易い。そのルール記述をアプリ作成に導入したい。<br>
lex & yaccは汎用向けで作成されたため内部構造が複雑で分かり辛い。<br>
lex & yaccの特徴にソースの動的生成がある。製品アプリでの実装はできない。<br>
<br>
内部構造を単純化してホワイトボックスとして運用できるようにする。<br>
ただし、単純化のための制限が実用レベルを落とさないように努める。<br>
構文ルールをオリジナル同様に記述できる。<br>
他環境への移植も考慮する。<br>
<br>

## 概要
<br>
終末記号分解部分 (lex)は、.lexに当たるものはなく、ソースコードによる直実装。 y/lex.cs にて行う。<br>
構文ルールは、y/syntax/ydef.csに定義。<br>
解釈の流れはProgram.csを参照すうこと。<br>
解釈後の実行用クラスは runtimeフォルダに集約。<br>
エラーとログ出力はsys.csに集約。<br>
<br>

## ソース

<pre>
Program.cs       -- 第一引数のスクリプトファイルを解釈実行
sys.cs           -- エラー、ログ出力提供（移植用）

y/yengine.cs     -- ユーザに機能を提供
y/lex.cs         -- 終末記号分解を提供
y/yanalyzer.cs   -- 構文ルール解析を提供
y/ycode.cs       -- 構文ルールで使用するAPI提供
y/ydef_api.cs    -- 解析と利用時のAPI提供
y/syntax/ydef.cs -- 構文ルール記述（.yファイル相当）

runtime/preprocessfunc.cs  -- スクリプトのプリプロセス時の関数実行用
runtime/mainprocessfunc.cs -- スクリプトのメインプロセス時の関数実行用
runtime/funcitl.cs         -- 関数実行時の汎用関数提供

data/*.txt       -- テストファイル
</pre>

## 実行確認

例） data/test05.txtを第一引数にして実行。<br>

<pre>
//
// TEST 05
//

Screen "hoge" 
Layer  "main" MC 

#if DATE("2016/8/13") 

    #set @A "AAA" 

#elif DATE("2016/8/14") 

    #set @A "BBB" 

#elif DATEBETWEEN("2016/8/15","2016/8/16")

    #set @A "CCC" 

#elif DATEAFTER("2016/8/17")  

    #set @A "DDD" 

#endif

display @A     (0,0)                { DELAY_ON(5); }
display "EFFECT_1" ("OKBUTTON",0,0) { HIDE();  On_button(); }


</pre>

<br>
以下、実行結果<br>
<br>
<pre>
*lex_output
[STR&gt;`SCREEN`][QSTR&gt;`"hoge"`][EOL&gt;]
[STR&gt;`LAYER`][QSTR&gt;`"main"`][STR&gt;`MC`][EOL&gt;]
[PRE&gt;`#IF`][STR&gt;`DATE`][SYM&gt;`(`][QSTR&gt;`"2016/8/13"`][SYM&gt;`)`][EOL&gt;]
[PRE&gt;`#SET`][VAR&gt;`@A`][QSTR&gt;`"AAA"`][EOL&gt;]
[PRE&gt;`#ELIF`][STR&gt;`DATE`][SYM&gt;`(`][QSTR&gt;`"2016/8/14"`][SYM&gt;`)`][EOL&gt;]
[PRE&gt;`#SET`][VAR&gt;`@A`][QSTR&gt;`"BBB"`][EOL&gt;]
[PRE&gt;`#ELIF`][STR&gt;`DATEBETWEEN`][SYM&gt;`(`][QSTR&gt;`"2016/8/15"`][SYM&gt;`,`][QSTR&gt;`"2016/8/16"`][SYM&gt;`)`][EOL&gt;]
[PRE&gt;`#SET`][VAR&gt;`@A`][QSTR&gt;`"CCC"`][EOL&gt;]
[PRE&gt;`#ELIF`][STR&gt;`DATEAFTER`][SYM&gt;`(`][QSTR&gt;`"2016/8/17"`][SYM&gt;`)`][EOL&gt;]
[PRE&gt;`#SET`][VAR&gt;`@A`][QSTR&gt;`"DDD"`][EOL&gt;]
[PRE&gt;`#ENDIF`][EOL&gt;]
[STR&gt;`DISPLAY`][VAR&gt;`@A`][SYM&gt;`(`][NUM&gt;`0`][SYM&gt;`,`][NUM&gt;`0`][SYM&gt;`)`][SYM&gt;`{`][STR&gt;`DELAY_ON`][SYM&gt;`(`][NUM&gt;`5`][SYM&gt;`)`][SYM&gt;`;`][SYM&gt;`}`][EOL&gt;]
[STR&gt;`DISPLAY`][QSTR&gt;`"EFFECT_1"`][SYM&gt;`(`][QSTR&gt;`"OKBUTTON"`][SYM&gt;`,`][NUM&gt;`0`][SYM&gt;`,`][NUM&gt;`0`][SYM&gt;`)`][SYM&gt;`{`][STR&gt;`HIDE`][SYM&gt;`(`][SYM&gt;`)`][SYM&gt;`;`][STR&gt;`ON_BUTTON`][SYM&gt;`(`][SYM&gt;`)`][SYM&gt;`;`][SYM&gt;`}`][EOL&gt;]


*first_interpreted
[sx_sentence&gt;0[sx_screen_sentence&gt;0[STR&gt;`SCREEN`]1[sx_param&gt;0[QSTR&gt;`"hoge"`]]]]
[sx_sentence&gt;0[sx_layer_sentence&gt;0[STR&gt;`LAYER`]1[sx_param&gt;0[QSTR&gt;`"main"`]]2[STR&gt;`MC`]]]
[sx_prepro_setence&gt;0[sx_pif_sentence&gt;0[PRE&gt;`#IF`]1[sx_function&gt;0[STR&gt;`DATE`]1[sx_param&gt;0[QSTR&gt;`"2016/8/13"`]]]]]
[sx_prepro_setence&gt;0[sx_pset_sentence&gt;0[PRE&gt;`#SET`]1[VAR&gt;`@A`]2[REST&gt;0[sx_param&gt;0[QSTR&gt;`"AAA"`]]]]]
[sx_prepro_setence&gt;0[sx_pelif_sentence&gt;0[PRE&gt;`#ELIF`]1[sx_function&gt;0[STR&gt;`DATE`]1[sx_param&gt;0[QSTR&gt;`"2016/8/14"`]]]]]
[sx_prepro_setence&gt;0[sx_pset_sentence&gt;0[PRE&gt;`#SET`]1[VAR&gt;`@A`]2[REST&gt;0[sx_param&gt;0[QSTR&gt;`"BBB"`]]]]]
[sx_prepro_setence&gt;0[sx_pelif_sentence&gt;0[PRE&gt;`#ELIF`]1[sx_function&gt;0[STR&gt;`DATEBETWEEN`]1[sx_param_list&gt;0[sx_param&gt;0[QSTR&gt;`"2016/8/15"`]]1[sx_param&gt;0[QSTR&gt;`"2016/8/16"`]]]]]]
[sx_prepro_setence&gt;0[sx_pset_sentence&gt;0[PRE&gt;`#SET`]1[VAR&gt;`@A`]2[REST&gt;0[sx_param&gt;0[QSTR&gt;`"CCC"`]]]]]
[sx_prepro_setence&gt;0[sx_pelif_sentence&gt;0[PRE&gt;`#ELIF`]1[sx_function&gt;0[STR&gt;`DATEAFTER`]1[sx_param&gt;0[QSTR&gt;`"2016/8/17"`]]]]]
[sx_prepro_setence&gt;0[sx_pset_sentence&gt;0[PRE&gt;`#SET`]1[VAR&gt;`@A`]2[REST&gt;0[sx_param&gt;0[QSTR&gt;`"DDD"`]]]]]
[sx_prepro_setence&gt;0[sx_pendif_sentence&gt;0[PRE&gt;`#ENDIF`]]]
[STR&gt;`DISPLAY`][VAR&gt;`@A`][sx_pos&gt;0[NUM&gt;`0`]1[NUM&gt;`0`]][sx_option_phrase&gt;0[sx_function&gt;0[STR&gt;`DELAY_ON`]1[sx_param&gt;0[NUM&gt;`5`]]]][EOL&gt;]
[sx_sentence&gt;0[sx_display_sentence&gt;0[STR&gt;`DISPLAY`]1[sx_param&gt;0[QSTR&gt;`"EFFECT_1"`]]2[sx_pos&gt;0[QSTR&gt;`"OKBUTTON"`]1[NUM&gt;`0`]2[NUM&gt;`0`]]3[sx_option_phrase&gt;0[sx_function_list&gt;0[sx_function&gt;0[STR&gt;`HIDE`]]1[sx_function&gt;0[STR&gt;`ON_BUTTON`]]]]]]

skip

*preprocessed
[sx_sentence&gt;0[sx_screen_sentence&gt;0[STR&gt;`SCREEN`]1[sx_param&gt;0[QSTR&gt;`"hoge"`]]]]
[sx_sentence&gt;0[sx_layer_sentence&gt;0[STR&gt;`LAYER`]1[sx_param&gt;0[QSTR&gt;`"main"`]]2[STR&gt;`MC`]]]
[STR&gt;`DISPLAY`][sx_param&gt;0[QSTR&gt;`"CCC"`]][sx_pos&gt;0[NUM&gt;`0`]1[NUM&gt;`0`]][sx_option_phrase&gt;0[sx_function&gt;0[STR&gt;`DELAY_ON`]1[sx_param&gt;0[NUM&gt;`5`]]]][EOL&gt;]
[sx_sentence&gt;0[sx_display_sentence&gt;0[STR&gt;`DISPLAY`]1[sx_param&gt;0[QSTR&gt;`"EFFECT_1"`]]2[sx_pos&gt;0[QSTR&gt;`"OKBUTTON"`]1[NUM&gt;`0`]2[NUM&gt;`0`]]3[sx_option_phrase&gt;0[sx_function_list&gt;0[sx_function&gt;0[STR&gt;`HIDE`]]1[sx_function&gt;0[STR&gt;`ON_BUTTON`]]]]]]

*executable_value_list
[sx_sentence&gt;0[sx_screen_sentence&gt;0[STR&gt;`SCREEN`]1[sx_param&gt;0[QSTR&gt;`"hoge"`]]]]
[sx_sentence&gt;0[sx_layer_sentence&gt;0[STR&gt;`LAYER`]1[sx_param&gt;0[QSTR&gt;`"main"`]]2[STR&gt;`MC`]]]
[sx_sentence&gt;0[sx_display_sentence&gt;0[STR&gt;`DISPLAY`]1[sx_param&gt;0[QSTR&gt;`"CCC"`]]2[sx_pos&gt;0[NUM&gt;`0`]1[NUM&gt;`0`]]3[sx_option_phrase&gt;0[sx_function&gt;0[STR&gt;`DELAY_ON`]1[sx_param&gt;0[NUM&gt;`5`]]]]]]
[sx_sentence&gt;0[sx_display_sentence&gt;0[STR&gt;`DISPLAY`]1[sx_param&gt;0[QSTR&gt;`"EFFECT_1"`]]2[sx_pos&gt;0[QSTR&gt;`"OKBUTTON"`]1[NUM&gt;`0`]2[NUM&gt;`0`]]3[sx_option_phrase&gt;0[sx_function_list&gt;0[sx_function&gt;0[STR&gt;`HIDE`]]1[sx_function&gt;0[STR&gt;`ON_BUTTON`]]]]]]


*Execute!

Exec Screen Sentence
Exec Layer Sentence
Exec Display Sentence
Exec Display Sentence

</pre>

## ライセンス

商用も含め、自由にお使いください。


## 補足

### 1. スクリプトについて
<br>
データスクリプトを想定。<br>
行単位での解釈を行う。<br>
プリプロセスを持つ。<br>
ダブルクォーテーションで囲まれた文字列以外は別として、他の文字は大文字として扱われる。<br>
数値はdouble値のみ。<br>

[命令]

<pre>
Screen  --- 表示スクリーン指定   Format: SCREEN  "スクリーン名" [{オプション}]
Layer   --- 表示レイヤ指定       Format: LAYER   "レイヤ名" アンカー 
Display --- 表示物指定           Format: DISPLAY "表示物名" 座標

#if|#elif|#else|#endif|#set --- プリプロス用。実際の実行より先に解釈される。変数 @hogeが置き換わる。

オプション : { hoge(); [hohe();...] } 
座標       : 2文字 TL(top left) MC(Middle Center) ...

</pre>

注：サンプルのため、仕様が曖昧なことに留意


### ２．構文ルールについて
<br>
y/syntax/ydef.csの構文ルールは以下の通りです。
<br>
<pre>
public object[] sx_hoge = new object[]{ 

                                 "sx_hoge",   --- 格納するルールデータと同じものを指定。
								 NNNN,        --- 優先順位を指定します。1000以上。数値が大きい方が優先度が高。
								 "HOGE", "(", QSTR ,")"    --- ルールを指定。(*1)
								 __MAKE__,                 --- 作成ＡＰＩが次に来ることを示すマーク。
								 YCODE.DO_NEW,             --- ＡＰＩ指定  YCODE.DO_NEW か YCODE.DO_ADDを指定
								                                YCODE.DO_NEW --- 新規のVALUEを作成。引数データをリスト化。
																YCODE.DO_ADD --- 既存のVALUEに引数データを追加。
								 0,2,                      --- ＡＰＩの引数を指定します。(*1)での実際値を引数化。
								                                例の場合、"HOGE"と"QSTR"が入る

							     __OR__,                   --- 別ルールが次に来ることを示すマーク

								 "HOGE", "(", QSTR, "," , QSTR, ")" --- ルール２
								 __MAKE__,
								 YCODE.DO_NEW,
								 0,2,4

                          };

</pre>
<br>

### ３．構文解釈について

スクリプトの冒頭から構文ルールを総当たりで解析。一方向のみ単純な解析のみ。<br>    

構文ルールに合致すると指定の生成API(YCODE.DO_NEWまたはYCODE.DO_ADD)を実行。<br>
	
詳細は y/yanalizer.csを参照。<br>
<br>
<br>
### ４．VALUEについて
<br>
VALUEは解析結果の１要素。<br>
<br>
<pre>
    public class VALUE
    {
        public double n;	//終末記号時に数値を格納
        public string s;	//終末記号時に文字列を格納
        public object o;	//終末記号時の要素（数値｜文字列）を格納

        public int type;	//終末記号・非終末記号のタイプを格納
        public List<VALUE> list; //非終末記号時に子要素を格納　YCODE.DO_NEW|DO_ADD時に引数を格納。
		  :
		  :
    }
	    ※解析結果は、VALUEの集合体となります。
</pre>

<br>
<br>
### ５．ルール変数で非終端記号の値（type）や名前を取得する
<br>
非終端記号の値（type）や名前の利便を計るため、ルール変数(YDEF.sx_xxxx)にて取得できるようにした。<br>
ルール変数はインテリジェンスの恩恵があるので、その利便を優先した。<br>
<br>

## 課題
<br>
スクリプト実行時の便利関数の充実。<br>
<br>
計算式、関数の導入<br>
<br>
<br>
以上
