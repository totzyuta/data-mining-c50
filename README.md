
# CS5.0を使って決定木解析する


Mac版はないのか？！と思ってググったところ以下の記事にあるように、普通にビルドすることができました。

http://d.hatena.ne.jp/ke_takahashi/20120807/1344331123


```
$ make all
```


## C5.0について概要

C4.5は、決定木を生成するためのアルゴリズムである。開発者はロス・キンラン。

C4.5の特徴は、3分岐以上の木も扱うことができるということ。

1997年に商用のSee5/C5.0にバージョンをあげた。See5/C5.0の前バージョン、C4.5

- ブースティングを組み込むことにより、精度が格段に上昇した。
・拡張性が向上し、マルチコアCPUでより効果的になった。


C5.0の特徴

- 目的変数: シンボル型のみ
- 説明変数の型: 数値型、シンボル型
- 分岐の数: 多分岐可能
- 評価基準: 情報量


- C4.5とSee5/C5.0

https://ja.wikipedia.org/wiki/C4.5#C4.5_.E3.81.A8_C5.0.2FSee5

http://rulequest.com/see5-comparison.html.



## サンプルを動かしてみる

以下のチュートリアルに従って、サンプルを動かしてみる。

http://www.rulequest.com/see5-examples.html#SPAMBASE

このサンプルは、UCIのMachine Learningリポジトリのデータを用いて、メールがスパムかどうかを判定するというものになっている。

http://archive.ics.uci.edu/ml/

データのURLは以下である。

https://archive.ics.uci.edu/ml/datasets/Spambase


データ数は4,601で、57の属性をもっている。この属性は、ある特定の単語の出現確率や、大文字の平均と最大値、合計値などの値となっている。

`spams.names`のコメントを除いた部分を抜き出すと、以下のように定義されている。

```
1, 0.    | spam, non-spam classes

word_freq_make:         continuous.
word_freq_address:      continuous.
word_freq_all:          continuous.
word_freq_3d:           continuous.
.
.
.
```

一番上の行は従属変数であり、ここでは1と0でスパム判定を行っている（1がスパムで、0スパムでないメール）。

3行目からが独立変数とその属性を表していて、3行目だと、`word_freq_make`は連続数であることを示している。

この例では連続数しか扱わないが、これが例えば天気だと

```
weather:         fine, cloudy, rain.
```

などと表され、これはfine, cloudy, rainの3属性があると定義していることになる。

また欠損データを扱うこともでき、その場合は「?」とすればよい。また、コメントは`|`の後に挿入することができる。



また、`spams.data`は以下のようなデータ列が続いている。

```
0,0.64,0.64,0,0.32,0,0,0,0,0,0,0.64,0,0,0,0.32,0,1.29,1.93,0,0.96,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0.778,0,0,3.756,61,278,1
0.21,0.28,0.5,0,0.14,0.28,0.21,0.07,0,0.94,0.21,0.79,0.65,0.21,0.14,0.14,0.07,0.28,3.47,0,1.59,0,0.43,0.43,0,0,0,0,0,0,0,0,0,0,0,0,0.07,0,0,0,0,0,0,0,0,0,0,0,0,0.132,0,0.372,0.18,0.048,5.114,101,1028,1
0.06,0,0.71,0,1.23,0.19,0.19,0.12,0.64,0.25,0.38,0.45,0.12,0,1.75,0.06,0.06,1.03,1.36,0.32,0.51,0,1.16,0.06,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0.06,0,0,0.12,0,0.06,0.06,0,0,0.01,0.143,0,0.276,0.184,0.01,9.821,485,2259,1
.
.
.
```

`spams.names`に対応して連続数が並んでいることが確認できる。

データに関しては、以下を参照した。

http://www.rulequest.com/see5-win.html#.data

それでは、ダウンロードしてきたデータをC5.0に読み込ませ、決定木を生成してみる。

C5.0の使い方、解説などは以下のサイトが非常に参考になった。

http://shower.human.waseda.ac.jp/~m-kouki/pukiwiki_public/110.html


出力は以下のようになった。

```
$ ./c5.0 -f ../spambase/spambase                                                                                                                                      

C5.0 [Release 2.07 GPL Edition]      Thu Jul 4 23:09:47 2015
-------------------------------

    Options:
    Application `../spambase/spambase'

Read 4601 cases (57 attributes) from ../spambase/spambase.data

Decision tree:

word_freq_remove > 0:
:...word_freq_hp > 0.19:
:   :...word_freq_our <= 0.3: 0 (16/1)
:   :   word_freq_our > 0.3: 1 (14/2)
:   word_freq_hp <= 0.19:
:   :...word_freq_edu > 0.08:
:       :...word_freq_money <= 0.04: 0 (7/1)
:       :   word_freq_money > 0.04: 1 (20)
:       word_freq_edu <= 0.08:
:       :...word_freq_1999 <= 0.25: 1 (716/17)
:           word_freq_1999 > 0.25:
:           :...word_freq_george <= 0.08: 1 (31)
:               word_freq_george > 0.08: 0 (3)
word_freq_remove <= 0:
:...char_freq_$ > 0.055:
    :...word_freq_hp > 0.39: 0 (64/1)
    :   word_freq_hp <= 0.39:
    :   :...word_freq_edu > 0.04:
    :       :...word_freq_order > 0.29: 1 (5)
    :       :   word_freq_order <= 0.29:
    :       :   :...char_freq_; <= 0.151: 0 (18)
    :       :       char_freq_; > 0.151: 1 (2)
    :       word_freq_edu <= 0.04:
    :       :...capital_run_length_longest > 9: 1 (534/21)
    :           capital_run_length_longest <= 9:
    :           :...word_freq_technology > 0.2: 1 (5)
    :               word_freq_technology <= 0.2:
    :               :...word_freq_address > 0.19: 1 (2)
    :                   word_freq_address <= 0.19:
    :                   :...word_freq_email > 3.03: 1 (2)
    :                       word_freq_email <= 3.03:
    :                       :...capital_run_length_average <= 2.176: 0 (22/1)
    :                           capital_run_length_average > 2.176: 1 (2)
    char_freq_$ <= 0.055:
    :...word_freq_000 > 0.25:
        :...word_freq_re <= 0.3: 1 (57/6)
        :   word_freq_re > 0.3: 0 (4/1)
        word_freq_000 <= 0.25:
        :...char_freq_! > 0.378:
            :...capital_run_length_total > 64:
            :   :...word_freq_pm > 0.04: 0 (11/1)
            :   :   word_freq_pm <= 0.04:
            :   :   :...word_freq_people <= 0.15: 1 (153/9)
            :   :       word_freq_people > 0.15:
            :   :       :...word_freq_business <= 0.21: 0 (13/5)
            :   :           word_freq_business > 0.21: 1 (12)
            :   capital_run_length_total <= 64:
            :   :...word_freq_free > 0.77: 1 (22/1)
            :       word_freq_free <= 0.77:
            :       :...capital_run_length_average > 2.653:
            :           :...word_freq_re > 0.6: 0 (4)
            :           :   word_freq_re <= 0.6:
            :           :   :...word_freq_85 > 1.31: 0 (2)
            :           :       word_freq_85 <= 1.31:
            :           :       :...word_freq_will <= 0.8: 1 (13/1)
            :           :           word_freq_will > 0.8: 0 (5/1)
            :           capital_run_length_average <= 2.653:
            :           :...char_freq_! <= 0.824: 0 (89/2)
            :               char_freq_! > 0.824:
            :               :...word_freq_business > 0.5: 1 (5)
            :                   word_freq_business <= 0.5:
            :                   :...word_freq_re > 0.34: 0 (13)
            :                       word_freq_re <= 0.34:
            :                       :...char_freq_! > 3.907: 1 (5)
            :                           char_freq_! <= 3.907:
            :                           :...word_freq_you <= 0.65: 0 (21/2)
            :                               word_freq_you > 0.65:
            :                               :...word_freq_george <= 1.25: 1 (10/2)
            :                                   word_freq_george > 1.25: 0 (2)
            char_freq_! <= 0.378:
            :...word_freq_money > 0.03:
                :...word_freq_hp > 0.08: 0 (13)
                :   word_freq_hp <= 0.08:
                :   :...word_freq_edu > 0.08: 0 (10)
                :       word_freq_edu <= 0.08:
                :       :...capital_run_length_longest <= 9: 0 (9/1)
                :           capital_run_length_longest > 9:
                :           :...word_freq_george <= 0.37: 1 (29)
                :               word_freq_george > 0.37: 0 (2)
                word_freq_money <= 0.03:
                :...word_freq_font > 0.12:
                    :...char_freq_; > 0.895: 0 (15)
                    :   char_freq_; <= 0.895:
                    :   :...word_freq_edu <= 0.09: 1 (19)
                    :       word_freq_edu > 0.09: 0 (5)
                    word_freq_font <= 0.12:
                    :...word_freq_free > 0.11:
                        :...word_freq_george > 0: 0 (33)
                        :   word_freq_george <= 0:
                        :   :...word_freq_our <= 1.13:
                        :       :...word_freq_project > 0.31: 0 (14)
                        :       :   word_freq_project <= 0.31:
                        :       :   :...word_freq_1999 > 0.11: 0 (41/3)
                        :       :       word_freq_1999 <= 0.11:
                        :       :       :...word_freq_technology <= 0.06: 0 (105/40)
                        :       :           word_freq_technology > 0.06: 1 (8)
                        :       word_freq_our > 1.13:
                        :       :...word_freq_meeting > 0.71: 0 (2)
                        :           word_freq_meeting <= 0.71:
                        :           :...word_freq_people > 0.62: 0 (2)
                        :               word_freq_people <= 0.62:
                        :               :...char_freq_! <= 0.279: 1 (28)
                        :                   char_freq_! > 0.279: 0 (2)
                        word_freq_free <= 0.11:
                        :...word_freq_credit > 0:
                            :...capital_run_length_longest > 295: 1 (3)
                            :   capital_run_length_longest <= 295:
                            :   :...capital_run_length_average <= 1.16: 1 (2)
                            :       capital_run_length_average > 1.16: 0 (20)
                            word_freq_credit <= 0:
                            :...word_freq_george > 0.01: 0 (667)
                                word_freq_george <= 0.01:
                                :...word_freq_our > 0.71:
                                    :...word_freq_internet > 0.33:
                                    :   :...word_freq_over <= 0.07: 1 (8)
                                    :   :   word_freq_over > 0.07: 0 (4/1)
                                    :   word_freq_internet <= 0.33:
                                    :   :...word_freq_email > 0.43:
                                    :       :...word_freq_will <= 1.82: 1 (5)
                                    :       :   word_freq_will > 1.82: 0 (2)
                                    :       word_freq_email <= 0.43: [S1]
                                    word_freq_our <= 0.71:
                                    :...word_freq_over > 0.76: [S2]
                                        word_freq_over <= 0.76:
                                        :...word_freq_hpl > 0: 0 (355/2)
                                            word_freq_hpl <= 0:
                                            :...word_freq_650 <= 0.26: 0 (1113/61)
                                                word_freq_650 > 0.26:
                                                :...word_freq_pm > 0.38: 1 (5)
                                                    word_freq_pm <= 0.38: [S3]

SubTree [S1]

capital_run_length_longest <= 10: 0 (74/2)
capital_run_length_longest > 10:
:...word_freq_hp > 0.11: 0 (22)
    word_freq_hp <= 0.11:
    :...word_freq_make <= 0.08: 1 (18/4)
        word_freq_make > 0.08: 0 (2)

SubTree [S2]

capital_run_length_longest <= 13: 0 (14)
capital_run_length_longest > 13:
:...word_freq_edu <= 0.39: 1 (8/1)
    word_freq_edu > 0.39: 0 (3)

SubTree [S3]

word_freq_report > 1.49: 1 (3)
word_freq_report <= 1.49:
:...word_freq_650 <= 5.88: 0 (30/3)
    word_freq_650 > 5.88: 1 (2)


Evaluation on training data (4601 cases):

        Decision Tree
      ----------------
      Size      Errors

        73  193( 4.2%)   <<


       (a)   (b)    <-classified as
      ----  ----
      1684   129    (a): class 1
        64  2724    (b): class 0


    Attribute usage:

        100%  word_freq_remove
         82%  char_freq_$
         68%  word_freq_000
         67%  char_freq_!
         61%  word_freq_free
         59%  word_freq_money
         58%  word_freq_george
         57%  word_freq_font
         51%  word_freq_credit
         41%  word_freq_our
         34%  word_freq_hp
         34%  word_freq_over
         33%  word_freq_hpl
         32%  word_freq_edu
         25%  word_freq_650
         20%  word_freq_1999
         17%  capital_run_length_longest
          8%  capital_run_length_total
          5%  word_freq_pm
          5%  capital_run_length_average
          5%  word_freq_people
          4%  word_freq_project
          3%  word_freq_email
          3%  word_freq_technology
          3%  word_freq_re
          3%  word_freq_internet
          2%  word_freq_business
          1%  char_freq_;


Time: 0.1 secs
```

このとき、以下の決定木の出力において、

```
word_freq_remove > 0:
:...word_freq_hp > 0.19:
:   :...word_freq_our <= 0.3: 0 (16/1)
:   :   word_freq_our > 0.3: 1 (14/2)
:   word_freq_hp <= 0.19:
:   :...word_freq_edu > 0.08:
:       :...word_freq_money <= 0.04: 0 (7/1)
:       :   word_freq_money > 0.04: 1 (20)
:       word_freq_edu <= 0.08:
:       :...word_freq_1999 <= 0.25: 1 (716/17)
:           word_freq_1999 > 0.25:
:           :...word_freq_george <= 0.08: 1 (31)
:               word_freq_george > 0.08: 0 (3)
word_freq_remove <= 0:
:...char_freq_$ > 0.055:
    :...word_freq_hp > 0.39: 0 (64/1)
    :   word_freq_hp <= 0.39:
    :   :...word_freq_edu > 0.04:
.
.
.
```

`word_freq_remove`、removeの出現確率が0より大きいかどうかが最初のノードに位置していることになる。0より大きければ次のノードである`word_freq_hp`へ、0以下であれば逆側のノードである`char_freq_$`を判定するために次に進む。（ただし、C5.0は必ず2分木であるわけでなく、多数の分かれているノードを持つこともできる。）




また、Ruleを出力すると、以下のようになった。

```
$ ./c5.0 -f ../spambase/spambase -r                                                                                                                                 

C5.0 [Release 2.07 GPL Edition]      Thu Jul 4 23:36:59 2015
-------------------------------

    Options:
    Application `../spambase/spambase'
    Rule-based classifiers

Read 4601 cases (57 attributes) from ../spambase/spambase.data

Rules:

Rule 1: (90, lift 2.5)
    word_freq_our > 1.13
    word_freq_people <= 0.62
    word_freq_free > 0.11
    word_freq_george <= 0
    word_freq_meeting <= 0.71
    char_freq_! <= 0.279
    ->  class 1  [0.989]

Rule 2: (92, lift 2.5)
    word_freq_font > 0.12
    word_freq_edu <= 0.09
    char_freq_; <= 0.895
    ->  class 1  [0.989]

Rule 3: (84, lift 2.5)
    word_freq_credit > 0
    capital_run_length_longest > 295
    ->  class 1  [0.988]

Rule 4: (73, lift 2.5)
    word_freq_our > 0.71
    word_freq_will <= 1.82
    word_freq_free <= 0.11
    word_freq_email > 0.43
    word_freq_george <= 0.01
    ->  class 1  [0.987]

.
.
.

Default class: 0


Evaluation on training data (4601 cases):

            Rules
      ----------------
        No      Errors

        29  190( 4.1%)   <<


       (a)   (b)    <-classified as
      ----  ----
      1682   131    (a): class 1
        59  2729    (b): class 0


    Attribute usage:

        100%  word_freq_remove
         49%  word_freq_hp
.
.
.
          2%  word_freq_credit


Time: 0.2 secs
```


See5.0/C5.0は0.1秒で22個のスパムであるメールのルールと、7個のスパムではないメールのルールを見つけたことを確認できる。

例えば、Rule 2は1813のスパムメールの中で、92のメールが`font`という単語を含んでいて出現率が高く、それと相関して`edu`か`;`が出現率が低くなっているということを示している。




## 人気のスマートフォンを解析する


価格.comの売れているスマートフォンランキングのうち、上位5機種を売れている機種、正例として、下位(45位~50位)の売れてない機種を負例として、解析を行う。

http://kakaku.com/keitai/smartphone/

### データ作成

まず、解析を行うためのデータを作成する。

重要な要素を属性として登録し、各データの該当する属性を定義していく。今回は以下のように`popularsmp.names`を定義した。

```
| POPULAR SMARTPHONE DATABASE ATTRIBUTES (WIP)

popular.    | popular, non-popular classes

maker:                    sony, huawei, kyocera, sumgsun, sharp, google.
color_variety:            continuous.
career:                   docomo, au, free.
max_waiting_duration_3g:  continuous.   
max_waiting_duration_lte: continuous.
memory_ram:             continuous. | GB
memory_rom:             continuous. | GB
core_number:            4,8.        | octo and quad
battery:                continuous. | mAh
display_size:           continuous. | inch
max_down_speed:         continuous. | Mbps
camera_pixel_number:    continuous.
weight:                 continuous. | g

popular:                1, 0.
```

それぞれ意味は以下の通りである。

- maker: メーカー
- color_variety: 端末の色の種類数
- career: キャリア
- max_waiting_duration_3g: 3G時における連続待機時間
- max_waiting_duration_lte: LTE時における連続待機時間
- memory_ram: RAMの容量
- memory_rom: ROMの容量
- core_number: コアの数
- battery: バッテリー容量
- display_size: ディスプレイのサイズ
- max_down_speed: 下り時最大容量（一番大きな値を選んだ）
- camera_pixel_number: カメラ画素数
- weight: 重量


この中でも特に、`memory_ram`、`memory_rom`、`camera_pixel_number`が重要だと予測した。

これらのデータの従属変数と独立変数は以下のようになっている。従属変数として`popular`という名前の変数を定義し、`1`が人気機種、`0`が人気でない機種とした。


## TODO: continuousにするかクラスで分けるかの違いについて考察する


```
sony,   4,docomo,480,470,3,32,8,2930,5.2,225,2070,144,1           | Xperia Z4 SO-03 docomo
huawei, 3,free,  500,450,2,16,8,2200,5.0,150,1300,131,1           | P8lite SIM Free
sony,   4,au,    460,460,3,32,8,2930,5.2,225,2070,144,1           | Xperia Z4 SOV31 au
sony,   4,docomo,750,640,3,32,4,3100,5.2,150,2070,152,1           | Xperia Z3 SO-01 docomo
sony,   4,docomo,800,650,2,16,4,2600,4.6,150,2070,129,1           | Xperia Z3 Compact SO-02G docomo
kyocera,2,au,    810,700,2,16,4,3100,4.5,150,800, 182,1           | TORQUE G01 au
sumgsun,2,docomo,470,430,3,32,4,3000,5.6,150,1600,177,0           | GALAXY Note Edge SC-01G docomo
sony,   3,docomo,740,610,2,32,4,3000,5.0,150,2070,171,0           | Xperia Z1 SO-01F docomo
sharp,  3,docomo,700,580,2,32,4,3000,5.0,150,1630,139,0           | AQUOS PHONE ZETA SH-01F docomo
google, 2,free,  ?,  330,3,32,4,3220,5.9,?,  1300,184,0           | Nexus 6 32GB SIM Free
```

今回は売り上げの上位5機種と、45~50位の5機種をデータとして登録した。

また、`popularsmp.data`の従属変数の定義からもわかるように、最後の数字`1`か`0`が、今回の従属変数（target attribute）となる。

まとめると、今回は10つのケースと、14の属性を用いて決定木を生成していく。


### 決定木の生成

では、このデータと変数の定義から決定木を生成する。

出力は以下のようになった。

```
$ ../C50/c5.0 -f popularsmp

C5.0 [Release 2.07 GPL Edition]      Fri Jul 10 01:27:13 2015
-------------------------------

    Options:
    Application `popularsmp'

Class specified by attribute `popular'

Read 10 cases (14 attributes) from popularsmp.data

Decision tree:

memory_rom <= 16: 1 (3)
memory_rom > 16:
:...camera_pixel_number <= 1630: 0 (3)
    camera_pixel_number > 1630: 1 (4/1)


Evaluation on training data (10 cases):

        Decision Tree
      ----------------
      Size      Errors

         3    1(10.0%)   <<


       (a)   (b)    <-classified as
      ----  ----
         6          (a): class 1
         1     3    (b): class 0


    Attribute usage:

        100%  memory_rom
         70%  camera_pixel_number


Time: 0.1 secs
```

決定木の部分を見てみると、まずROMメモリの容量`memory_rom`によって決定が行われ、`memory_rom`が16より小さければ人気スマフォ、16より大きければ次のノードへ進む。次のノードではカメラ画素数`camera_pixel_number`が1630以下であれば人気のないスマートフォンで、1630より大きければ人気機種という解析を行っている。

決定木の部分を見てみると、サンプルで動かしたものより非常にコンパクトになっていることがまず気になった。これはデータ数が属性の数が少なったかったのも要因の一つとして、Ruleをあまり見つけることができなかったのではないかと予想した。

ということで、Ruleを確認してみる。出力は以下のようになった。

```
$ ../C50/c5.0 -f ./popularsmp -r               

C5.0 [Release 2.07 GPL Edition]      Fri Jul 10 02:20:03 2015
-------------------------------

    Options:
    Application `./popularsmp'
    Rule-based classifiers

Class specified by attribute `popular'

Read 10 cases (14 attributes) from ./popularsmp.data

Rules:

Rule 1: (3, lift 1.3)
    memory_rom <= 16
    ->  class 1  [0.800]

Rule 2: (7/3, lift 1.4)
    memory_rom > 16
    ->  class 0  [0.556]

Default class: 1


Evaluation on training data (10 cases):

            Rules
      ----------------
        No      Errors

         2    3(30.0%)   <<


       (a)   (b)    <-classified as
      ----  ----
         3     3    (a): class 1
               4    (b): class 0


    Attribute usage:

        100%  memory_rom


Time: 0.0 secs
```

Ruleは`memory_rom`に関するものだけだった。ここから、設定した属地値のうち、与えられたデータから推定される売れるスマートフォンの明確な条件は「`memory_rom`が16より大きいかどうか」ということだと分かる。

例えばRule 1に関してみると、`memory_rom <= 16` の条件によりclass 1、つまり人気のあるスマートフォンと判別された場合、それは0.8の確率で正しさがある、ということが示されている。

また`Evaluation on training data`以下は各ルールが何回使われたかなどの結果が出力されていて、今回の場合はRule 2は3つのエラーを出したので正答率は30%ということがわかり、class 1に分類されたのは3つ、class 0に分類されたのは7つあった。


確かにデータセットを見てみるとこのようなモデルが生成されたことも分からなくはない。ROMの容量は人気機種で32GBが3種、人気のない機種で4種と、人気のない機種の方が少し多くなっている。

だがこれでは到底よい判定、データマイニングができる決定木とはいえないだろう。C5.0にはテストデータを判定する機能もあるので、この決定木の精度を探るために、新たなデータを加えて判定させ、その原因をもう少し探ってみようと思う。

テストデータは先程と同様のランキングから、5, 6位のスマートフォンと、50, 51位のスマートフォンとした。

`popularsmp.test`は以下のようになった。

```
asus,   2,free,  296,497,4,32,4,3000,5.5,?,  1300,170,1           | ZenFone 2 Memory 4GB / Storage 32GB SIM Free
kyocera,3,au,    690,690,2,16,4,2910,4.7,150,1300,203,1           | TORQUE G02 au
htc,    3,au,    490,430,2,32,4,2700,5.0,150,1300,156,0           | HTC J butterfly HTL23 au
huawei, 2,free,  422,404,2,16,4,2500,5.0,150,1300,124,0           | Ascend P7 SIM Free
```

以下のように、テストデータも加えて学習されたルールをみると、生成された木の内容が変わった。

```
$ ../C50/c5.0 -f popularsmp                                

C5.0 [Release 2.07 GPL Edition]      Fri Jul 13 11:41:19 2015
-------------------------------

    Options:
    Application `popularsmp'

Class specified by attribute `popular'

Read 10 cases (14 attributes) from popularsmp.data

Decision tree:

color_variety > 3: 1 (4)
color_variety <= 3:
:...memory_rom <= 16: 1 (2)
    memory_rom > 16: 0 (4)


Evaluation on training data (10 cases):

        Decision Tree
      ----------------
      Size      Errors

         3    0( 0.0%)   <<


       (a)   (b)    <-classified as
      ----  ----
         6          (a): class 1
               4    (b): class 0


    Attribute usage:

        100%  color_variety
         60%  memory_rom
```


Ruleは以下のようになった。

```
Rules:

Rule 1: (4, lift 1.4)
    color_variety > 3
    ->  class 1  [0.833]

Rule 2: (3, lift 1.3)
    memory_rom <= 16
    ->  class 1  [0.800]

Rule 3: (4, lift 2.1)
    color_variety <= 3
    memory_rom > 16
    ->  class 0  [0.833]

Default class: 1
```

`memory_rom`で判定するノードに加えて、`color_variety`が3より大きいかどうかの判定も追加され、カメラのピクセルについての判定はなくなっている。

```
Evaluation on test data (4 cases):

            Rules
      ----------------
        No      Errors

         3    2(50.0%)   <<


       (a)   (b)    <-classified as
      ----  ----
         1     1    (a): class 1
         1     1    (b): class 0
```

そしてテストは半分の正答率となった。4つのデータのうち2つがNo.3のRuleでエラーとなっている。


以下の点が大きな要因だと予測した。

1. ランキング2位であるHuaweiのP8liteは軽量機種であり、スペックを比較するとよい機種には入らない

2. 発売時期を考慮していない


## さいごに

C5.0による決定木の生成を行った。どのようにたくさんの情報の中から新たな知見をみつけるかというプロセス、またそのプロセスにおける特徴の選択の難しさなどを学ぶことができた。

クローラなどを利用してもっと大きなデータを扱って決定木を作るのにも挑戦したいと思った。
