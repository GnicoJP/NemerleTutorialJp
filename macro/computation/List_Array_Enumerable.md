# list builder
`list[T]`をモナドとして利用できるのが`list builder`です．  
`yield`で値を追加していくことができます．  
実装見るとすごく重そうです．  
```nemerle
def sqrt : list[double] = comp list 
{
  foreach(n in Enumerable.Range(0, 10))
    when(n >= 0)
      yield Math.Sqrt(n);
}
```
F#でもよく見ます．`yieldcomp`を使えば，以下のようなことも可能です．  
```nemerle
def twice(l) {
    comp list{ repeat(2) yieldcomp l; }
}
```
この関数は，渡されたリスト`l`を2回繰り返す関数です．  

# Array builder
`Array[T]`も同様なことができます．  
内部的には`list`で作ったものを`ToArray`しているだけのようです．

# Enumerable builder
`IEnumerable[T]`をモナドとして利用できるのが`Enumerable builder`です．  
F#の`Seq{}`のように`yield`で値を追加していくことができます．  
（`Seq`の型エイリアスが`IEnumerable`にはつけられています．）  
**遅延評価**です．  
```nemerle
def sqrt : IEnumerable[double] = comp enumerable 
{
  foreach(n in Enumerable.Range(0, 10))
    when(n >= 0)
      yield Math.Sqrt(n);
}
```
同様に`yieldcomp`を使えば，以下のようなことも可能です．  
```nemerle
def infinite(l) {
    comp enumerable{ while(true) yieldcomp l; }
}
```
この関数は，渡されたIEnumerable`l`を無限回繰り返す関数です．  