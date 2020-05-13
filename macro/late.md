# ダックタイピングのためのlateマクロ
C#の`dynamic`に似たもので，`Nemerle.Late`に用意されています．
## 書き方
```
late <late-expr>
nolate <expr>
```
`<late-expr>`にダックタイピングしたいコードを入れます．

```nemerle
def hoge = Activator.CreateInstance(some_type);
def ret = late hoge.somefunc("hogehoge");
def ret2 = late {
    _ = hoge.somefunc("fugafuga");
    hoge.somefunc("piyopiyo");
};
hoge |> WriteLine;
```

## 例
例えば次のようなコードをコンパイルします．
```nemerle
def hoge = Activator.CreateInstance(typeof(StringBuilder));
def ret = late hoge.Append("hogehoge");
hoge |> WriteLine;
```
すると次のようにコンパイルされました．
```msil
// typeof(StringBuilder)
IL_0002: ldtoken [mscorlib]System.Text.StringBuilder 
IL_0007: call class [mscorlib]System.Type [mscorlib]System.Type::GetTypeFromHandle(valuetype [mscorlib]System.RuntimeTypeHandle)
// Activator.CreateInstance()
IL_000c: call object [mscorlib]System.Activator::CreateInstance(class [mscorlib]System.Type)
// hoge = Activator.CreateInstance(...)
IL_0011: stloc.0
// var2 = null //??必要??
IL_0013: ldnull
IL_0014: stloc.1
// var2 = hoge
IL_0015: ldloc.0
IL_0016: stloc.1
IL_0017: ldloc.1
// var2.GetType()
IL_0018: callvirt instance class [mscorlib]System.Type [mscorlib]System.Object::GetType()
IL_001d: ldstr "Append"
IL_0022: ldc.i4 256
// Stackの状態
// typeof(StringBuilder) "Append" 256
IL_0027: ldnull
IL_0028: ldloc.1

IL_0029: ldc.i4.1
// Stackの状態
// typeof(StringBuilder) "Append" 256 null var2 1
// ary = new object[1]
IL_002a: newarr [mscorlib]System.Object
IL_002f: dup
// Stackの状態
// typeof(StringBuilder) "Append" 256 null var2 ary
IL_0030: ldc.i4 0
// Stackの状態
// typeof(StringBuilder) "Append" 256 null var2 ary ary 0
IL_0035: ldstr "hogehoge"
// Stackの状態
// typeof(StringBuilder) "Append" 256 null var2 ary ary 0 "hogehoge"

// ary[0] = "hogehoge"
IL_003a: stelem.ref
IL_003b: ldnull
IL_003c: ldnull
IL_003d: ldnull
// Stackの状態
// typeof(StringBuilder) "Append" 256 null var2 ary null null null

// 第2引数の256はBindingFlags.InvokeMethod
// typeof(StringBuilder).InvokeMember("Append", 256, null, var2, ary, null, null)
IL_003e: callvirt instance object [mscorlib]System.Type::InvokeMember(string, valuetype [mscorlib]System.Reflection.BindingFlags, class [mscorlib]System.Reflection.Binder, object, object[], valuetype [mscorlib]System.Reflection.ParameterModifier[], class [mscorlib]System.Globalization.CultureInfo, string[])

// _ = typeof(StringBuilder).InvokeMember(...)
IL_0043: pop
IL_0044: nop

// hoge |> WriteLine
IL_0045: ldloc.0
IL_0046: ldsfld class Program/_N_static_proxy_5725 Program/_N_static_proxy_5725::single_instance
IL_004b: call void class [Nemerle]Nemerle.Builtins.Function`2<object, object>::'|>'(!0, class [Nemerle]Nemerle.Builtins.FunctionVoid`1<!0>)
```

メンバ呼び出しには`Type.InvokeMember`が使われることが分かります．  

-----
ちなみにC#は，
```csharp
dynamic hoge = Activator.CreateInstance(typeof(StringBuilder));
hoge.Append("hogehoge");
Console.WriteLine(hoge);
```
と書くことができ，これは`System.Runtime.CompilerService.CallSite`が使われます．
```msil
// typeof(StringBuilder)
IL_0001: ldtoken [System.Runtime]System.Text.StringBuilder
IL_0006: call class [System.Runtime]System.Type [System.Runtime]System.Type::GetTypeFromHandle(valuetype [System.Runtime]System.RuntimeTypeHandle)
// dynamic = Activator.CreateInstance(typeof(StringBuilder))
IL_000b: call object [System.Runtime]System.Activator::CreateInstance(class [System.Runtime]System.Type)
IL_0010: stloc.0
// 静的メンバを読み出し
IL_0011: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>> CsTest.Program/'<>o__0'::'<>p__0'
// Nullチェック
IL_0016: brfalse.s IL_001a
IL_0018: br.s IL_0058
// NullならばCallSiteを静的メンバに代入します {
IL_001a: ldc.i4 256
IL_001f: ldstr "Append"
IL_0024: ldnull
IL_0025: ldtoken CsTest.Program
IL_002a: call class [System.Runtime]System.Type [System.Runtime]System.Type::GetTypeFromHandle(valuetype [System.Runtime]System.RuntimeTypeHandle)
IL_002f: ldc.i4.2
IL_0030: newarr [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo
IL_0035: dup
IL_0036: ldc.i4.0
IL_0037: ldc.i4.0
IL_0038: ldnull
IL_0039: call class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags, string)
IL_003e: stelem.ref
IL_003f: dup
IL_0040: ldc.i4.1
IL_0041: ldc.i4.3
IL_0042: ldnull
IL_0043: call class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags, string)
IL_0048: stelem.ref
IL_0049: call class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSiteBinder [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.Binder::InvokeMember(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpBinderFlags, string, class [System.Runtime]System.Collections.Generic.IEnumerable`1<class [System.Runtime]System.Type>, class [System.Runtime]System.Type, class [System.Runtime]System.Collections.Generic.IEnumerable`1<class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo>)
IL_004e: call class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<!0> class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>>::Create(class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSiteBinder)
IL_0053: stsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>> CsTest.Program/'<>o__0'::'<>p__0'

IL_0058: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>> CsTest.Program/'<>o__0'::'<>p__0'
IL_005d: ldfld !0 class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>>::Target
// }
// CallSite<Action<System.Runtime.CompilerServices.CallSite, object, string>>.Target(CallSite, "hogehoge");
IL_0062: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>> CsTest.Program/'<>o__0'::'<>p__0'
IL_0067: ldloc.0
IL_0068: ldstr "hogehoge"
IL_006d: callvirt instance void class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, object, string>::Invoke(!0, !1, !2)
IL_0072: nop

// Nullチェック
IL_0073: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>> CsTest.Program/'<>o__0'::'<>p__1'
IL_0078: brfalse.s IL_007c
IL_007a: br.s IL_00bb

// NullならばCallSiteを静的メンバに代入します {
IL_007c: ldc.i4 256
// Console.WriteLine関数を呼び出す．
IL_0081: ldstr "WriteLine"
IL_0086: ldnull
IL_0087: ldtoken CsTest.Program
IL_008c: call class [System.Runtime]System.Type [System.Runtime]System.Type::GetTypeFromHandle(valuetype [System.Runtime]System.RuntimeTypeHandle)
IL_0091: ldc.i4.2
IL_0092: newarr [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo
IL_0097: dup
IL_0098: ldc.i4.0
IL_0099: ldc.i4.s 33
IL_009b: ldnull
IL_009c: call class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags, string)
IL_00a1: stelem.ref
IL_00a2: dup
IL_00a3: ldc.i4.1
IL_00a4: ldc.i4.0
IL_00a5: ldnull
IL_00a6: call class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags, string)
IL_00ab: stelem.ref
IL_00ac: call class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSiteBinder [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.Binder::InvokeMember(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpBinderFlags, string, class [System.Runtime]System.Collections.Generic.IEnumerable`1<class [System.Runtime]System.Type>, class [System.Runtime]System.Type, class [System.Runtime]System.Collections.Generic.IEnumerable`1<class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo>)
IL_00b1: call class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<!0> class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>>::Create(class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSiteBinder)
IL_00b6: stsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>> CsTest.Program/'<>o__0'::'<>p__1'

IL_00bb: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>> CsTest.Program/'<>o__0'::'<>p__1'
IL_00c0: ldfld !0 class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>>::Target
IL_00c5: ldsfld class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite`1<class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>> CsTest.Program/'<>o__0'::'<>p__1'
IL_00ca: ldtoken [System.Console]System.Console
IL_00cf: call class [System.Runtime]System.Type [System.Runtime]System.Type::GetTypeFromHandle(valuetype [System.Runtime]System.RuntimeTypeHandle)
IL_00d4: ldloc.0

// CallSite<Action<System.Runtime.CompilerServices.CallSite, object, string>>.Target(CallSite, typeof(Console), hoge);
IL_00d5: callvirt instance void class [System.Runtime]System.Action`3<class [System.Linq.Expressions]System.Runtime.CompilerServices.CallSite, class [System.Runtime]System.Type, object>::Invoke(!0, !1, !2)
IL_00da: nop
IL_00db: ret
```

CallSiteの実装内部では，ExpressionでType.GetMethodを使って構築しているようです．
また，作ったCallSiteは静的フィールドに保存されてキャッシュされます．

## できないこと
dynamicな型の変数を渡すことはできません．
```nemerle
mul(i : int, j : int) : int {
  i * j;
}
Main() : void
{
  def hoge : object = 2;
  def hoge1 : object = 3;

  late (mul(hoge, hoge1)) |> WriteLine;
  _ = ReadLine();
}
```
なのでこのようにする必要があります．
```nemerle
match((hoge, hoge1)){
    | (i is int, j is int) =>
        mul(i, j) |> WriteLine;
}
```