---
title: "数値計算"
date: 2021-01-20T02:36:44+09:00
categories: ["数値計算"]
tags: ["勉強ノート"]
---

## 整数の表現

コンピュータ内部では電気信号の有無を 0 と 1 に割り当て 2 進法で数値を表現する。

### 符号付整数型

符号付整数型 (signed integer type) は最上位ビット (MSB = most significant bit) を符号ビットに割り当て、0 ならば値が 0 か正、1 ならば値が負であることを表す。
他のビットは**仮数部**と呼ばれ、正の数であれば数値を、負の数であれば数値の絶対値を 2 の補数 (後述) で表すのが一般的である。

### 2 の補数

**2 の補数 (2's complement)** は負の値を表現する方法の 1 つであり、元の値のすべてのビットを反転したものに 1 を加算したものをいう。

前述の符号ビットと組み合わせると、負の数を含む加算を同じ加算器で扱うことができる。 (加算と減算を同じ演算器で扱える)

### 精度

整数型は一般的に 16 ビット (`short`), 32 ビット (`int`), 64 ビット (`long`, `long long`) のいずれかで表される。
一部言語の一部整数型は実装依存であることもある。 (ex. C/C++ の `long`, Rust の `usize`)

整数型のビット数を {{< katex >}}b{{< /katex >}} とすると、表現可能な数値 {{< katex >}}x{{< /katex >}} の範囲は {{< katex >}}0\le x\le2^b-1{{< /katex >}} となる。
ただし、符号付整数型では MSB が符号ビットになるため、表現可能な数値 {{< katex >}}x{{< /katex >}} の範囲は {{< katex >}}-2^{b-1}\le x\le2^{b-1}-1{{< /katex >}} となる。

### オーバーフロー

演算結果が格納域の記録できる範囲を超えてしまうと、最上位桁より上の桁への繰り上がり (キャリー) が失われたり、本来演算結果を格納する場所とは違う場所 (ex. 符号ビット) に格納される場合がある。
この現象を **(算術) オーバーフロー (overflow)** という。

<!-- TODO アンダーフローについて書く -->

## 浮動小数点数の表現

浮動小数点数では以下に示すデータで数値を表現する。

- 符号
- 指数 (exponent)
- 仮数 (significand)
- 基数 (base)

数値の絶対値は以下の式で表される。

{{< katex display >}}
|数値|=仮数\times 基数^{指数
{{< /katex >}}

#### IEEE 754

IEEE 標準のひとつである IEEE 754 (IEEE Standard for Floating-Point Arithmetic) で定められている浮動小数点数の方式を以下に示す。

半精度浮動小数点数
: 基数は 2 に固定, 1 ビットの符号部, 5 ビットの指数部, 10 ビットの仮数部をもつ (計 16 bits = 2 bytes)
: 指数部には 15 のゲタを履かせた数値で表す

単精度浮動小数点数 (float)
: 基数は 2 に固定, 1 ビットの符号部, 8 ビットの指数部, 23 ビットの仮数部 (計 32 bits = 4 bytes)
: 指数部には 127 のゲタを履かせた数値で表す

倍精度浮動小数点数 (double)
: 基数は 2 に固定, 1 ビットの符号部, 11 ビットの指数部, 52 ビットの仮数部 (計 64 bits = 8 bytes)
: 指数には 1023 のゲタを履かせた数値で表す

### 誤差

丸め誤差 (rounding error)
: 数値のどこかの桁で切り上げ・切り捨てなど端数処理を行った場合に生じる誤差。

打ち切り誤差 (truncation error)
: 反復計算において、必要とされる回数より少ない回数で反復を止める (打ち切り) によって生じる誤差。

情報落ち (loss of trailing digits)
: 精度が限られている条件下で、絶対値の大きい数と絶対値の小さい数を加減算したときに、絶対値の小さい数が無視されてしまう現象。

桁落ち (cancellation)
: 値がほぼ等しく、丸め誤差をもつ数値同士の減算したときに、有効数字が減る現象。

### Rump の例題

{{< katex display >}}
f(a,b)=333.75b^6+a^2(11a^2b^2-b^6-121b^4-2)+5.5b^8+\frac{a}{2b}
{{< /katex >}}

以上で示した関数において、 {{< katex >}}a=77617.0,b=33096.0{{< /katex >}} を与えて {{< katex >}}f(a,b){{< /katex >}} を計算する。

- 単精度浮動小数点数で計算すると {{< katex >}}f(a,b)\sim1.172603\dots{{< /katex >}} が得られる。
- 倍精度浮動小数点数で計算すると {{< katex >}}f(a,b)\sim1.1726039400531\dots{{< /katex >}} が得られる。
- 真の値は {{< katex >}}f(a,b)=-0.82739605\dots{{< /katex >}} である。

Rump の例題はこのように

> ある演算精度での計算結果とそれより高い演算精度での計算結果を比較したときに、双方の値が近ければ、その値はある程度は正しい。

という直感に対する反例である。

### 精度保証付き数値計算

精度保証付き数値計算とは、数学的に厳密な誤差の評価を伴う数値計算のことをいう。

## 任意精度演算

任意精度演算とは、数値の精度をいくらでも伸ばせるような演算システム (またはデータ構造) のことをいう。

有名な任意精度演算をサポートするライブラリとして、Java の `BigInteger` (整数), `BigDecimal` などがある。

- [BigInteger (Java SE8)](https://docs.oracle.com/javase/jp/8/docs/api/java/math/BigInteger.html)
- [BigDecimal (Java SE8)](https://docs.oracle.com/javase/jp/8/docs/api/java/math/BigDecimal.html)

## 参考文献

- Wikipedia, [コンピュータの数値表現](https://ja.wikipedia.org/wiki/%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF%E3%81%AE%E6%95%B0%E5%80%A4%E8%A1%A8%E7%8F%BE)
- Wikipedia, [整数型](https://ja.wikipedia.org/wiki/%E6%95%B4%E6%95%B0%E5%9E%8B)
- Wikipedia, [2 の補数](https://ja.wikipedia.org/wiki/2%E3%81%AE%E8%A3%9C%E6%95%B0)
- Wikipedia, [算術オーバーフロー](https://ja.wikipedia.org/wiki/%E7%AE%97%E8%A1%93%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%95%E3%83%AD%E3%83%BC)
- Wikipedia, [浮動小数点数](https://ja.wikipedia.org/wiki/%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0)
- Wikipedia, [数値解析](https://ja.wikipedia.org/wiki/%E6%95%B0%E5%80%A4%E8%A7%A3%E6%9E%90)
- Wikipedia, [誤差](https://ja.wikipedia.org/wiki/%E8%AA%A4%E5%B7%AE)
