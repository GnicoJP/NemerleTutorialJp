# コンピュテーション式マクロ
コンピュテーション式マクロはF#のコンピュテーション式と同等の機能を実現するためのマクロです．  
コンピュテーションはMonadやMonoid，Transformers，Applicative，Functerなどの抽象的な構造を用いた分かりやすい計算の流れを記述できます．

## 基本
まず，コンピュテーションのビルダのクラスを作成します．F#ではモジュールですが，Nemerleではインスタンス化できるクラスも使用できます.  
```nemerle
class CompTest
{
  public Return[T](obj : T) : ???[T]
  { 
    ...
  }
  public Bind[T, U](obj : ???[T], f : T->???[T]) : ???[U]
  {
    ...
  }
  public Empty[T]() : ???[T]
  {
    ...
  }
}
```
定義すると使えるメソッドは以下の通りです．  
|関数名|型|説明|
|-|-|-|
|Zero|`void->M[T]`|零元|
|Return|`U->M[T]`|`return`して値を返して終了する|
|ReturnComp|`M[T]->M[T]`|値を取り出してまた`return`して返して終了する. F#の`ReturnFrom`と等価|
|Yield|`U->M[T]`|コレクション用で，`return`して値を返し継続する|
|YieldComp|`M[T]->M[T]`|コレクション用で，値を取り出してまた`return`して返し継続する. F#の`YieldFrom`と等価|
|Bind|`M[T]*(T->M[U])->M[U]`|Haskellの`fmap`と等価で，値を取り出して関数を適用する|
|Delay|`(void->M[T])->M[T]`|コンピュテーション式を関数として包む|
|Combine|`M[void]*M[T]->M[T]またはM[T]*M[T]->M[T]`|2つのコンピュテーション式を並べる|
|While|`(void->bool)*M[T]->M[T]`|whileループで使われる|
|ForEach|`M[T]*(T->M[U])->M[U]`|foreachループで使われる. F#の`For`と等価|
|TryFinally|`M[T]*(unit->unit)->M[T]`|`try ... finally`で使われる|
|TryCatch|`M[T]*(Exception->M[T])->M[T]`|`try ... catch`で使われる|
|Using|`V*(V->M[T])->M[T] where V : IDisposable`|`using`で使われる|

そして，次のように使います．  
```nemerle
def builder = SomeBuilder()
comp builder {
    def hoge = 32; // builderと関係ない普通の関数定義
    defcomp fuga = SomeFunc(); // builder.Bindが使われる．SomeFunc()はM[T]である．
    callcomp SomeFunc(); // 上と同様にbuilder.Bindが使われる．戻り値は破棄される．
    return fuga; // builder.returnが使われる．
    returncomp SomeFunc(); // defcomp -> returnと同じ意味になる．
    defcomp piyo = SomeFunc2();
    yield piyo; // builder.yieldが使われる．
    foreach(i in SomeFunc2()) {
        // builder.ForEachが使われる．yieldが使えるならば大抵ForEachは実装されているはずである．
    }
}
```

# Computationのサブコンテンツ
まだなし．  