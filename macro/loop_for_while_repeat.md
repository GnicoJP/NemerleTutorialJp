# 繰り返しのマクロたち
## repeat文マクロ : 指定された回数繰り返す文
repeat文は指定された回数だけ繰り返す文です．
```
repeat(n){
    <body>
}
```
`n`回`body`を繰り返します．（nは`long`）

```nemerle
repeat(10) {
    WriteLine("Hello");
}
```

## while文マクロ : 条件判定の式のある繰り返し文
while文は条件判定の式によって繰り返しを続けるか判定して繰り返す文です．
```
while(<condition>){
    <body>
}
```
`condition`が`true`の間は`body`を繰り返します．

```nemerle
mutable n = 0;
while(n < 10) {
    WriteLine($"n is $n");
    ++n;
}
```

### while-otherwise文マクロ : 一度もループ内の文が実行されなかった時の動作を指定できるwhile文
while文は条件判定の式によって繰り返しを続けるか判定して繰り返す文です．
```
while(<condition>){
    <body>
} otherwise {
    <other>
}
```
`condition`が`true`の間は`body`を繰り返します．一度も`condition`が`true`にならなかった（`body`が一度も実行されなかった）場合，`other`が実行されます．

## do-while文マクロ : 条件判定の式のある繰り返し文
do-while文は条件判定の式によって繰り返しを続けるか判定して繰り返す文です．  
while文と違って，条件判定は繰り返しの内容を実行した後にされるので，最低でも1度は繰り返しの内容が実行されます
```
do {
    <body>
} while(<condition>)
```
`condition`が`true`の間は`body`を繰り返します．

```nemerle
mutable n = 0;
do {
    WriteLine($"n is $n");
    ++n;
} while(n < 1);
```

## for文マクロ : whileに加え，初期化の式，遷移の式がある繰り返し文
```
for(<initialization>;<condition>;<transition>) {
    <body>
}
```
while文に加えて，for文の最初に一度だけ実行される`initialization`の文と次にループするときに実行される`transition`の文が追加された繰り返し文です．

```nemerle
for(mutable n = 0; n < 10; ++n) {
    WriteLine($"n is $n");
}
```

## foreach文マクロ : コレクションを値についてすべて処理する繰り返す文
```
foreach(<variable> in <collection>) {
    <body>
}
```
`collection`の全要素について，`body`を実行する繰り返し文です．`variable`に要素が代入されます．  
`collection`に指定可能な型は`System.Collections.Generic.IEnumerable`型や`Nemerle.list`型を実装した or 継承した型，または，`System.Array`です．`with`句を付けると，添え字を取ることができます．

```
foreach(<variable> in <collection> with <index_variable>) {
    <body>
}
```

```nemerle
foreach(i in System.Collections.Generic.Enumerable.Range(0,100) with j) {
    unless(i != j)
        Console.WriteLine("Something wrong.");
}
```

### foreach-otherwise文マクロ : 一度もループ内の文が実行されなかった時の動作を指定できるforeach文
```
foreach(<variable> in <collection>){
    <body>
} otherwise {
    <other>
}
```
一度も`condition`が`true`にならなかった（`body`が一度も実行されなかった）場合，`other`が実行されます．