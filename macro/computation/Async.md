# Async builder
C#の`async` `await`に似たコンピュテーション式です．  
ビルダのモナドは`Async[T]`型です．これは，F#の`Async<T>`と違って，コンピュテーション式でしか使いません．  
（トップレベルの名前空間ではなく，コンピュテーション式関連の名前空間で宣言されています．）  
また，Nemerleには別に`Nemerle.Async`があります．  
また，コンピュテーション式が何も返さない場合，型を`Async[FakeVoid]`とします．  

## Execution Context
`ExecutionContext`はAsyncコンピューテション式を用いるうえで重要なクラスの一つとなります．  
このクラスは渡された関数をどのスレッドで実行するか（現在のスレッドか，スレッドプールから取ってくるか，`SynchronizationContext`から取ってくるか）を決めます．
```nemerle
public abstract class ExecutionContext
{
  public abstract Execute(computatuion : void -> void) : void;
}
```
よくGUIの操作でAsyncを使うと思いますが，このときの`ExecutionContext`はUIライブラリで提供される`SynchronizationContext`で継続のコードを実行します．  
UIのコントロールの操作は`UIスレッド`で行う必要があるからです．  

## Async.FromBeginEnd
既存のライブラリを簡単に`Async`対応にするには`Async.FromBeginEnd`を使用します．  
型は`(AsyncCallback->IAsyncResult)*(IAsyncResult->T)->Async[T]`です．  
以下の例は`HttpWebRequest`に`Async`をラップしている例です．  
```nemerle
public GetResponseAsync(this request : HttpWebRequest) : Async[WebResponse]
{
  Async.FromBeginEnd(request.BeginGetResponse(_, null), request.EndGetResponse(_))
  // _ は部分適用で適用しない部分
}
```

## コンピュテーション式の書き方
上でラップしたのを使って，コンピュテーション式を書いていきます．  
```nemerle
public ReadFromDouble() : Async[FakeVoid]
{
  comp async
  {
    def response1 = HttpWebRequest.Create("http://www.example.com/1").GetResponseAsync();
    def response2 = HttpWebRequest.Create("http://www.example.com/2").GetResponseAsync();
    defcomp response1 = response1;
    defcomp response2 = response2;
    ShowResult(response1);
    ShowResult(response2);
  }
}
```
`await`に当たる部分は`defcomp`です．つまり上の例では，先に`response1`と`response2`の要求を発行しておいて，続く`defcomp`の行で待機しているわけです．  
そして，この関数を利用するときは以下のようにします．
```nemerle
ReadFromDouble().Start();
```
`Start`を呼ばなければ実行されないことに注意してください．
また，引数として，`ExecutionContext`と`CancellationToken`を渡すことができます．（渡さなければそれぞれの`Default`プロパティが渡されます．）  

## Asyncの諸関数
### Async.SwitchTo
`ExecutionContext`を切り替えることができます．  
```nemerle
comp async {
    defcomp some = SomeFunc();
    callcomp Async.SwitchTo(UIExecutionContext);
    SomeUI.SomeProperty = some;
}
```
### Async.Yield
`Thread.Yield`のように，別スレッドに実行権を譲ります． 
```nemerle
comp async {
    defcomp some = SomeFunc();
    callcomp Async.Yield();
    defcomp afger = some.hoge();
}.Start(SomeThreadPoolExecutionContext)
```