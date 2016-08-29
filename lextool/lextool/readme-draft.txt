■動機と目的

lex & yaccの構文ルール記述は簡潔で分かり易い。そのルール記述をアプリ作成に導入したい。
lex & yaccは汎用向けで作成されたため内部構造が複雑で分かり辛い。
lex & yaccの特徴にソースの動的生成がある。市販アプリでの実装はできない。

内部構造を単純化してホワイトボックスとして運用できるようにする。
ただし、単純化のための制限が実用レベルを落とさないように努める。
構文ルールをオリジナル同様に記述できる。
他環境への移植も考慮する。


■概要

終末記号分解部分 (lex)は、.lexに当たるものはなく、ソースコードによる直実装。 y/lex.cs にて行う。
構文ルールは、y/syntax/ydef.csに定義。
解釈の流れはProgram.csを参照すうこと。
解釈後の実行用クラスは runtimeフォルダに集約。
エラーとログ出力はsys.csに集約。

■ソース

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

■実行確認

例） data/test05.txtを第一引数にして実行。

以下、実行結果

*lex_output
[STR>`SCREEN`][QSTR>`"hoge"`][EOL>]
[STR>`LAYER`][QSTR>`"main"`][STR>`MC`][EOL>]
[PRE>`#IF`][STR>`DATE`][SYM>`(`][QSTR>`"2016/8/13"`][SYM>`)`][EOL>]
[PRE>`#SET`][VAR>`@A`][QSTR>`"AAA"`][EOL>]
[PRE>`#ELIF`][STR>`DATE`][SYM>`(`][QSTR>`"2016/8/14"`][SYM>`)`][EOL>]
[PRE>`#SET`][VAR>`@A`][QSTR>`"BBB"`][EOL>]
[PRE>`#ELIF`][STR>`DATEBETWEEN`][SYM>`(`][QSTR>`"2016/8/15"`][SYM>`,`][QSTR>`"2016/8/16"`][SYM>`)`][EOL>]
[PRE>`#SET`][VAR>`@A`][QSTR>`"CCC"`][EOL>]
[PRE>`#ELIF`][STR>`DATEAFTER`][SYM>`(`][QSTR>`"2016/8/17"`][SYM>`)`][EOL>]
[PRE>`#SET`][VAR>`@A`][QSTR>`"DDD"`][EOL>]
[PRE>`#ENDIF`][EOL>]
[STR>`DISPLAY`][VAR>`@A`][SYM>`(`][NUM>`0`][SYM>`,`][NUM>`0`][SYM>`)`][SYM>`{`][STR>`DELAY_ON`][SYM>`(`][NUM>`5`][SYM>`)`][SYM>`;`][SYM>`}`][EOL>]
[STR>`DISPLAY`][QSTR>`"EFFECT_1"`][SYM>`(`][QSTR>`"OKBUTTON"`][SYM>`,`][NUM>`0`][SYM>`,`][NUM>`0`][SYM>`)`][SYM>`{`][STR>`HIDE`][SYM>`(`][SYM>`)`][SYM>`;`][STR>`ON_BUTTON`][SYM>`(`][SYM>`)`][SYM>`;`][SYM>`}`][EOL>]


*first_interpreted
[sx_sentence>0[sx_screen_sentence>0[STR>`SCREEN`]1[sx_param>0[QSTR>`"hoge"`]]]]
[sx_sentence>0[sx_layer_sentence>0[STR>`LAYER`]1[sx_param>0[QSTR>`"main"`]]2[STR>`MC`]]]
[sx_prepro_setence>0[sx_pif_sentence>0[PRE>`#IF`]1[sx_function>0[STR>`DATE`]1[sx_param>0[QSTR>`"2016/8/13"`]]]]]
[sx_prepro_setence>0[sx_pset_sentence>0[PRE>`#SET`]1[VAR>`@A`]2[REST>0[sx_param>0[QSTR>`"AAA"`]]]]]
[sx_prepro_setence>0[sx_pelif_sentence>0[PRE>`#ELIF`]1[sx_function>0[STR>`DATE`]1[sx_param>0[QSTR>`"2016/8/14"`]]]]]
[sx_prepro_setence>0[sx_pset_sentence>0[PRE>`#SET`]1[VAR>`@A`]2[REST>0[sx_param>0[QSTR>`"BBB"`]]]]]
[sx_prepro_setence>0[sx_pelif_sentence>0[PRE>`#ELIF`]1[sx_function>0[STR>`DATEBETWEEN`]1[sx_param_list>0[sx_param>0[QSTR>`"2016/8/15"`]]1[sx_param>0[QSTR>`"2016/8/16"`]]]]]]
[sx_prepro_setence>0[sx_pset_sentence>0[PRE>`#SET`]1[VAR>`@A`]2[REST>0[sx_param>0[QSTR>`"CCC"`]]]]]
[sx_prepro_setence>0[sx_pelif_sentence>0[PRE>`#ELIF`]1[sx_function>0[STR>`DATEAFTER`]1[sx_param>0[QSTR>`"2016/8/17"`]]]]]
[sx_prepro_setence>0[sx_pset_sentence>0[PRE>`#SET`]1[VAR>`@A`]2[REST>0[sx_param>0[QSTR>`"DDD"`]]]]]
[sx_prepro_setence>0[sx_pendif_sentence>0[PRE>`#ENDIF`]]]
[STR>`DISPLAY`][VAR>`@A`][sx_pos>0[NUM>`0`]1[NUM>`0`]][sx_option_phrase>0[sx_function>0[STR>`DELAY_ON`]1[sx_param>0[NUM>`5`]]]][EOL>]
[sx_sentence>0[sx_display_sentence>0[STR>`DISPLAY`]1[sx_param>0[QSTR>`"EFFECT_1"`]]2[sx_pos>0[QSTR>`"OKBUTTON"`]1[NUM>`0`]2[NUM>`0`]]3[sx_option_phrase>0[sx_function_list>0[sx_function>0[STR>`HIDE`]]1[sx_function>0[STR>`ON_BUTTON`]]]]]]

skip

*preprocessed
[sx_sentence>0[sx_screen_sentence>0[STR>`SCREEN`]1[sx_param>0[QSTR>`"hoge"`]]]]
[sx_sentence>0[sx_layer_sentence>0[STR>`LAYER`]1[sx_param>0[QSTR>`"main"`]]2[STR>`MC`]]]
[STR>`DISPLAY`][sx_param>0[QSTR>`"CCC"`]][sx_pos>0[NUM>`0`]1[NUM>`0`]][sx_option_phrase>0[sx_function>0[STR>`DELAY_ON`]1[sx_param>0[NUM>`5`]]]][EOL>]
[sx_sentence>0[sx_display_sentence>0[STR>`DISPLAY`]1[sx_param>0[QSTR>`"EFFECT_1"`]]2[sx_pos>0[QSTR>`"OKBUTTON"`]1[NUM>`0`]2[NUM>`0`]]3[sx_option_phrase>0[sx_function_list>0[sx_function>0[STR>`HIDE`]]1[sx_function>0[STR>`ON_BUTTON`]]]]]]

*executable_value_list
[sx_sentence>0[sx_screen_sentence>0[STR>`SCREEN`]1[sx_param>0[QSTR>`"hoge"`]]]]
[sx_sentence>0[sx_layer_sentence>0[STR>`LAYER`]1[sx_param>0[QSTR>`"main"`]]2[STR>`MC`]]]
[sx_sentence>0[sx_display_sentence>0[STR>`DISPLAY`]1[sx_param>0[QSTR>`"CCC"`]]2[sx_pos>0[NUM>`0`]1[NUM>`0`]]3[sx_option_phrase>0[sx_function>0[STR>`DELAY_ON`]1[sx_param>0[NUM>`5`]]]]]]
[sx_sentence>0[sx_display_sentence>0[STR>`DISPLAY`]1[sx_param>0[QSTR>`"EFFECT_1"`]]2[sx_pos>0[QSTR>`"OKBUTTON"`]1[NUM>`0`]2[NUM>`0`]]3[sx_option_phrase>0[sx_function_list>0[sx_function>0[STR>`HIDE`]]1[sx_function>0[STR>`ON_BUTTON`]]]]]]


*Execute!

Exec Screen Sentence
Exec Layer Sentence
Exec Display Sentence
Exec Display Sentence

■ライセンス

商用も含め、自由にお使いください。


■補足

1. スクリプトについて

データスクリプトを想定。
行単位での解釈を行う。
プリプロセスを持つ。
ダブルクォーテーションで囲まれた文字列以外は別として、他の文字は大文字として扱われる。
数値はdouble値のみ。

[命令]

Screen  --- 表示スクリーン指定   Format: SCREEN  "スクリーン名" [{オプション}]
Layer   --- 表示レイヤ指定       Format: LAYER   "レイヤ名" アンカー 
Display --- 表示物指定           Format: DISPLAY "表示物名" 座標

#if|#elif|#else|#endif|#set --- プリプロス用。実際の実行より先に解釈される。変数 @hogeが置き換わる。

オプション : { hoge(); [hohe();...] } 
座標       : 2文字 TL(top left) MC(Middle Center) ...

注：サンプルのための仕様が曖昧なことに留意ください。


２．構文ルールについて

y/syntax/ydef.csの構文ルールは以下の通りです。

public object[] sx_hoge = new object[]{ 

                                 "sx_hoge",   <--- 格納するルールデータと同じものを指定。
								 NNNN,        <--- 優先順位を指定します。1000以上。数値が大きい方が優先度が高。
								 "HOGE", "(", QSTR ,")"    <--- ルールを指定。(*1)
								 __MAKE__,                 <--- 作成ＡＰＩが次に来ることを示すマーク。
								 YCODE.DO_NEW,             <--- ＡＰＩ指定  YCODE.DO_NEW か YCODE.DO_ADDを指定
								                                YCODE.DO_NEW --- 新規のVALUEを作成。引数データをリスト化。
																YCODE.DO_ADD --- 既存のVALUEに引数データを追加。
								 0,2,                      <--- ＡＰＩの引数を指定します。(*1)での実際値を引数化。
								                                例の場合、"HOGE"と"QSTR"が入る

							     __OR__,                   <--- 別ルールが次に来ることを示すマーク

								 "HOGE", "(", QSTR, "," , QSTR, ")" <--- ルール２
								 __MAKE__,
								 YCODE.DO_NEW,
								 0,2,4

                          };

３．構文解釈について

    スクリプトの冒頭から構文ルールを総当たりで解析。一方向のみ単純な解析のみ。

	構文ルールに合致すると指定の生成API(YCODE.DO_NEWまたはYCODE.DO_ADD)を実行。
	
	詳細は y/yanalizer.csを参照。
	

４．VALUEについて
	
	VALUEは解析結果の１要素。
　　
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

	解析結果は、VALUEの集合体となります。


５．ルール変数で非終端記号の値（type）や名前を取得する

非終端記号の値（type）や名前の利便を計るため、ルール変数(YDEF.sx_xxxx)にて取得できるようにした。
ルール変数はインテリジェンスの恩恵があるので、その利便を優先した。


■課題

スクリプト実行時の便利関数の充実。

計算式、関数の導入


以上