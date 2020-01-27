---
title: "Effective C++ 第3版"
date: '2020-01-27'
categories:
  - Programming
tags:
  - C++
---

### 3項　できるだけconstを使おう
- constオブジェクトはconstのメンバ関数のみで使用できる 1,2

~~~cpp
#include <iostream>
#include <string>
using namespace std;

class Sample {
private:
	string value;
public:
	Sample(const char* a): value(a){}
	void unconst_func() { cout << value << endl; }
	void const_func() const { cout << value << endl; }
};

int main() {
	const Sample a("a"); // constオブジェクト
	a.unconst_func(); // エラー
	a.const_func(); // エラーでない
}
~~~

### 参照
- <a href=#3a>*1</a>:[～　第４章　クラスと関数：その設計と宣言　～](http://www002.upp.so-net.ne.jp/ys_oota/effec/chapter4.htm#21kou)
- <a href=#3b>*2</a>:[第１０章　不動の構え](http://www7b.biglobe.ne.jp/~robe/cpphtml/html02/cpp02010.html)
- [C++ビギナーに捧ぐ　EffectiveC++入門](http://www002.upp.so-net.ne.jp/ys_oota/effec/)
- [Effective C++（項1〜5）解説](https://qiita.com/MoriokaReimen/items/58f183d421bb932cbbda)
