# 条件分岐マクロたち
## if式マクロ: 条件がTrueの場合，Falseの場合で分岐させる
```
if(<condition>) {
  <A>
} else {
  <B>
}
```

両方共，`condition`が`true`の時は，`A`のコードを，`false`の時は，`B`のコードを実行します．
C#のようにカッコを省略することができます．**しかし，elseは省略できません．**  

```nemerle
def a = int.Parse(Console.ReadLine());
if(a == 0) {
  Console.WriteLine("a is Zero.");
} else {
  Console.WriteLine("a is not Zero.");
}

// 括弧を省略
if(a == 0)
  Console.WriteLine("a is Zero.");
else
  Console.WriteLine("a is not Zero.");
```

さて，このif式は，式なので，値を返すことができます．
従って，C#の三項演算子のように使えます．Nemerleのifマクロは一般的な関数型言語のif式と同じ機能を持っている訳です．
```nemerle
// 先ほどのサンプルコードは，Console.WriteLineに渡す文字列だけを条件分岐しているだけなので，次のように書くこともできます．
def a = int.Parse(Console.ReadLine());
Console.WriteLine(if(a == 0) "a is Zero." else "a is not Zero.");
```
if式と同じ機能を持つので，else節が省略できないようになっているんですね．  
（else節を省略してしまっては，falseの時に返すべき値が分からなくなってしまいます．）

## when文マクロ: 条件がTrueの場合のみ実行する
ということで，Nemerleでelse節を省略したい場合は別のマクロを用いることになります．

```
when(<condition>) {
  <Code>
}
```

`condition`が`true`の時，`Code`が実行されます．

```nemerle
def a = int.Parse(Console.ReadLine());
when(a == 0) {
  Console.WriteLine("a is Zero.");
}
```

## unless文マクロ: 条件がFalseの場合のみ実行する
```
unless(<condition>) {
  <Code>
}
```

`condition`が`false`の時，`Code`が実行されます．  
（when文で，論理否定演算子使って済ませることもできます．）