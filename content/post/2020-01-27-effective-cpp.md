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

### 4項　オブジェクトは、使う前に初期化しよう

### 5項　C++が自動生成・呼び出しするクラスメンバを知っておこう

### 6項　コンパイラが自動生成することを望まない関数は、使用を禁止しよう
- コピーコンストラクタやコピー代入演算子を望まない場合、基底クラスで禁止しておく
- 方法として、Privateで書く

~~~cc
class Uncopyable
{
protected:
  Uncopyable(){}
  ~Uncopuable(){}
private:
  Uncopyable(const Uncopyable&);
  Uncopyable& operator=(const Uncopyable&);
};

class HomeForSale:private Uncopyable{
 ---
};
~~~

### 10項　代入演算子*thisへの参照を戻すようにしよう　
- 数値などは以下のように自己代入できる 

~~~cpp
int main()
{
	int x, y, z;
	x = y = z = 15;
	cout << x << y << z << endl; // 151515
}
~~~

- 自分のクラスに代入する場合、以下のように***this**をreturnする

~~~cpp
#include <iostream>
using namespace std;

class Widget
{
public:
	---
	Widget& operator=(const Widget& rhs)
	{
		-- -
			return *this;
	}

};
~~~

### 11項　operator=の実装では、自己代入に備えよう
　

### 参照
- <a href=#3a>*1</a>:[～　第４章　クラスと関数：その設計と宣言　～](http://www002.upp.so-net.ne.jp/ys_oota/effec/chapter4.htm#21kou)
- <a href=#3b>*2</a>:[第１０章　不動の構え](http://www7b.biglobe.ne.jp/~robe/cpphtml/html02/cpp02010.html)
- [C++ビギナーに捧ぐ　EffectiveC++入門](http://www002.upp.so-net.ne.jp/ys_oota/effec/)
- [Effective C++（項1〜5）解説](https://qiita.com/MoriokaReimen/items/58f183d421bb932cbbda)
