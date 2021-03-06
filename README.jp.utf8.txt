# -*- coding:utf-8 -*-
MTToolBox 疑似乱数生成器開発ライブラリ README ファイル

doxygen ドキュメント http://msaito.github.io/MTToolBox/
doxygen ドキュメント http://msaito.github.io/MTToolBox/en/ (英語)
github プロジェクト  https://github.com/MSaito/MTToolBox

1. はじめに

MTToolBox は疑似乱数生成器開発用のC++ライブラリである。このライブラリは
SFMT, dSFMT, MTGP, TinyMT の開発に使われたツールをまとめたものであり、
その意味ではある程度実績のあるライブラリであると考えてよい。

ただし、上記の疑似乱数生成器の開発に伴ってライブラリは変化しており、今
回かなり書き直しているので、その意味ではあまり実績はない。とはいえ、
samples ディレクトリにあるサンプルをコンパイルして実行できるならば、正
しいと考えてよいだろう。

MTToolBox はGF(2)線形疑似乱数生成器を作成するためのライブラリであり、そ
れ以外の疑似乱数生成器の開発には役に立たない。ただし、GF(2)線形状態遷移
関数とGF(2)非線形出力関数という組合せの開発を行う上では、それなりに役に
立つと思われる。

なお、作者（斎藤）は、「Ｃ＋＋　全然わからん」というレベルなので、多数
の誤りが含まれると思われるが、そこは「Ｃ＋＋　もちろんわかります」レベ
ルの人が修正してくれるものと期待している。作者（斎藤）のレベルの低さは
後置インクリメントを多用していることからも伺い知れるであろう。なお、作
者（松本）は、Ｃ＋＋を知らない。

2. 必要ライブラリ

NTL
version 6.0 以降
http://www.shoup.net/ntl/
GF(2)係数多項式の計算に使用。このプロジェクトがC++で書かれている最大の
理由はNTLを使うためである。

tr1/shared_ptr C++/tr1, C++11
shared_ptr にしなくても動作すると思うが、不安なので一部で使用。

openssl
http://www.openssl.org/
MTGP32dcのサンプルの出力を公開済みのMTGPの出力と同様にするため。
インストールされていなくても、他のサンプルのコンパイルには影響しない。


3. 実行速度について

MTGP, TinyMT の開発時には実行速度を重視して、テンプレートを多用し継承や
仮想メソッドはほとんど使用していなかったが、今回のライブラリでは継承や
仮想メソッドを多用している。その理由は、利用者からのエラーメッセージな
どの問合せを極力減らしたいからである。TinyMTやMTGPのパラメータを求めた
いならそれらのリリースに含まれるdynamic creator を利用した方が高速にパ
ラメータを求めることが出来る。

とはいえ、高速性を求めるのがC++ プログラマーの性（さが）であるから、
branch して高速版を作成することは何の問題もなく、むしろ推奨されると
言ってもよい。

3. sample について、

現状では、MT, MTGP, RMT, TinyMT がサンプルとして入れてあるが、
今後、SFMT, dSFMT を追加する予定である。

これらのサンプルを修正して新しい疑似乱数生成器を作成してもよい。ライセ
ンス上許可されるという以上に推奨される。斎藤個人としての意見だが、
dSFMTとTinyMTは完成度が高めなので改良の余地は少ないと思っている。一方、
SFMTとMTGPは完成度が低めなので、改良の余地は大きい。特にMTGPのテーブル
ルックアップによる状態遷移パラメータとテンパリングパラメータは、やりす
ぎであろう。テーブルルックアップするならサイズはあまり関係ないだろうと
いう考えから、大きめにしてしまったという反省がある。SFMTはSSE2に基づい
ているので、AVX2に基づいた疑似乱数生成器という発想は当然あるだろう。

4. ドキュメントについて

日本語のみなので、英訳してくれる人がいるとうれしい。斎藤が英語で書こうとすると
どうしても必要最小限のドキュメントになってしまう。今回は、それでは不足する
という意識から日本語にしてしまった。まあ、論文にしないから、日本語でもいいや
という考えもあったわけだが。

暇があれば、google翻訳などを使って少しずつ英訳を入れる予定だが、
だれかがbranchを作るなりしてやってくれても全く問題はない。

5. インターフェイス変更
ver.1.0 でインターフェイスが大幅に変わり、過去のバージョンを使ってるユーザーは書き換えが必要。
主に setUpParam の引数である乱数生成器のクラスが ParameterGeneratorに変更になった。
それに伴って多くのテンプレートで（デフォルトの値を持つ）第二引数がなくなった。
ParameterGeneratorクラスは getUint32()とgetUint64()メソッドを持つので、
どちらかを使用する。

ver.0.2.10 でテンパリング関係のインターフェイスが変わったので、これまでの
ユーザープログラムはエラーになる。(この部分はver.1.0で元に戻った。つまり二番目の引数が不要になった)

旧：
    typedef AlgorithmPartialBitPattern<uint32_t, 32, 1, 9, 5, true> stlsb32;
新：
    typedef AlgorithmPartialBitPattern<uint32_t, uint32_t, 32, 1, 9, 5, true>
    stlsb32;
二番目のuint32_t は疑似乱数生成器のパラメータを生成する時に使う疑似乱数成績の出力タイプを指定する。
過去のプログラムなら上のように同じ型を二回指定すればよいだろう。
