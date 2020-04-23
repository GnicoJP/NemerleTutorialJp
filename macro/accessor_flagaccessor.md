# Accessorマクロ : プロパティを楽に定義する
AccessorマクロはNemerle.Utilityにあり，プロパティを楽に定義することができます．
## 基本：getter-onlyの定義
```nemerle:getter-only
mutable some_field : int; 
public SomeField : int 
{
  get { some_field }
} 
```
こんなgetter-onlyなプロパティを定義したい場合，次のように定義できます．
```nemerle:getter-only
using Nemerle.Utility;

[Accessor (SomeField)] mutable some_field : int;
```
Accessorにはプロパティ名を渡しますが，省略することができます．すると，次のようにプロパティ名が定義されます．  
* `_`が削除され，`_`の後の文字が大文字になります．（例：`_some_field`→`SomeField`）  
* 先頭の文字が大文字になります．（例：`some`→`Some`）

## flags引数
flags引数で様々なプロパティを定義することができます．同時に指定する場合は，`|`で繋げます．

### WantSetter, Setter : Setterあり
Setterが欲しい場合は，`WantSetter`か`Setter`を指定します．（コードを見ると違いはないようです．）
```nemerle:get-set
using Nemerle.Utility;

[Accessor (flags = WantSetter)] mutable some_field : int;
```
↓
```nemerle:getter-only
mutable some_field : int; 
public SomeField : int 
{
  get { some_field }
  set { some_field = value }
} 
```

### 修飾子
`Internal`, `Protected`, `Override`, `Virtual`修飾子を指定することができます．
```nemerle:access
using Nemerle.Utility;

[Accessor (flags = WantSetter | Internal)] mutable some_field : int;
```
↓
```nemerle:getter-only
mutable some_field : int; 
internal SomeField : int 
{
  get { some_field }
  set { some_field = value }
} 
```

# FlagAccessorマクロ : フラグに関するプロパティを楽に定義する
Enumには`System.Flags`属性をつけることでビットフィールドとして使用できるようになります．  
```nemerle
[System.Flags]
enum MyFlags {
  | A = 0x0001
  | B = 0x0002
  | C = 0x0004
}

MyFlags flag = MyFlags.A | MyFlags.B;
WriteLine($"A is $(flag.HasFlag(MyFlags.A))."); // A is true.
```

よって，次のようなプロパティを作ることがあるでしょう．
```nemerle
mutable state : MyFlags;

public A : bool {
  get { state.HasFlag(MyFlags.A) }
  set { if(value)
          state |= MyFlags.A;
        else 
          state &= ~MyFlags.A;
  }
}
```
これを，`FlagAccessor`マクロで簡単に定義することができます．
```nemerle
[FlagAccessor(A, flags = WantSetter)]
mutable state : MyFlags;
```
`flags`は`Accessor`マクロと同じ値が指定できます．
```nemerle
[FlagAccessor(A, B, C, flags = WantSetter)]
mutable state : MyFlags;
```
また，一つのマクロでプロパティを作りたいだけ作ることができます．
