*.Net上で動く静的型付けなマルチパラダイム言語 Nemerle*

手続き型，オブジェクト指向型，関数型のプログラミング手法を混在することができる優れたプログラミング言語です．[Qiita](https://qiita.com/Gnico/items/49a1f2978bd9990b8e61)がNemerleのシンタックスハイライティングしなくなったので移しました．主に備忘録目的．
# 注意
C#のコードとの見比べが沢山あります．  

# 筆者の環境
>Windows 10 20H1  
>Visual Studio 2017(15.9.3)  
>Nemerle v1.2.547.0 (2017-09-01)  
>.Net 4.6.2

よほど変な環境でなければNemerleは動くはずです．（他OSの上で動くmono環境でも）  
.Net 3.5 にも対応しており，Visual Studio 2008から対応します．

# 目次
## 基本編
1. [はじめに](basic/1.firststep.md) 
2. [変数の宣言と代入，コンストラクタの呼び出し](basic/2.variables.md) 
3. [プリミティブ型とリテラル](basic/3.primitive_types.md)
4. [演算子](basic/4.operators.md)
6. [関数](basic/5.function.md)
7. [名前空間とクラス](basic/6.namespace_class.md)

## マクロ集
* [if & when & unlessマクロ : 条件分岐](macro/conditional_branch_if_when_unless.md)
* [repeat & while & do-while & while-otherwise & forマクロ : 繰り返し](macro/loop_for_while_repeat.md)
* [Accessor & FlagAccessorマクロ : プロパティを楽に定義する](macro/accessor_flagaccessor.md)
* [Lateマクロ : ダックタイピング](macro/late.md)
* [Computationマクロ : コンピュテーション式](macro/computation/computation.md)

***
間違えがありましたらIssueをしてください．
***

## 旧版（Qiita）
[基本編1 ～インストール，言語説明，C#に近い機能の説明～](http://qiita.com/Gnico/items/c32185c8900215cd790b)  
>言語説明  
変数・定数・代入・プリミティブ型  
条件分岐  
スクリプト言語風な書き方  
アクセス修飾子  
メソッド  
クラス定義  
レコード（構造体）  
キャスト  

[基本編2 ～便利な機能，ループ，関数型言語の世界への第一歩～](http://qiita.com/Gnico/items/43a84aed8e8d6468ba47)  
>文字列補完  
繰り返し  
高級関数＆ラムダ式  

[基本編3 ～list，パターンマッチ～](http://qiita.com/Gnico/items/cd7f32aa8c973df0711e)  
>リスト  
foreach文  
パターンマッチ  
ガード  

[基本編4 ～tuple，バリアント，もっとパターンマッチ～](http://qiita.com/Gnico/items/72d8f17fef52100963a6)  
>タプル  
バリアント  
パターンマッチ  

[基本編5 ～その他の機能～](http://qiita.com/Gnico/items/6168e9aefcf83b5096b0)  
>delegate  
event  
out/ref  
Generic Method  
interface  
indexer  
using  
演算子のオーバーロード  
try-catch-finally  
P/invoke  

[基本編6 ～配列，Option，マクロの基本，色々なマクロ～](http://qiita.com/Gnico/items/19ee4aebcda592947d01)  
>配列  
全ては式である  
Option  
マクロ紹介  

[～マクロ紹介～](http://qiita.com/Gnico/items/1327bba77977e716c58d)  
> 匿名型  
sizeof  
surroundwith  
ラベルとGoto文  

[マクロ編1 ～マクロ(基礎)，マクロ(構文拡張基礎)～](http://qiita.com/Gnico/items/25735e41e21f7ce4098a)  
>マクロの基礎  
構文拡張基礎  

### 付録：Nemerleの便利なマクロ集
[デザインパターンマクロ](http://qiita.com/Gnico/items/8447e418aaf295cf387e)
