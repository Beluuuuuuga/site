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

### 12項　コピーするときは、オブジェクト全体をコピーしよう
- コピー関数はオブジェクトのデータメンバーと基底クラスのすべてをコピーするようにする

### 13項　リソース管理にオブジェクトを使おう
- 例外処理・return文などでポインタのdeleteが行われないことがある
- リソースをオブジェクトの中に置き、そのオブジェクトが削除されるときに自動的にデストラクタされるようにする
- スマートポインタなどのリソース管理オブジェクトを使用する
- 動的に確保した配列に対してはスマートポインタは適用されないので注意
- TR1: Technical Report 1
  - ISO/IEC TR 19768:2007の非公式名称で、標準C++ライブラリの拡張についての標準規格である
- 通常のポインタではdeleteしないとリークとなる
~~~cpp
#include <iostream>
using namespace std;

int main() {
	int* ptr = new int(10);
	for (int i = 0; i < 10; i++)
	{
		*ptr += i;
	}
	cout << "ptr=" << *ptr << endl;
	delete ptr;
	return 0;
}
~~~

- スマートポインタではdeleteは必要ない
- auto_ptrではコピーするとコピー先にメモリの所有権が移る
~~~cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
	std::auto_ptr<int> ptr(new int(10));
	for (int i = 0; i < 10; i++)
	{
		*ptr += i;
	}
	cout << "ptr=" << *ptr << endl;
	
	return 0;
}
~~~
 - uniq_ptrはメモリの所有権は1つだけ
 - shared_ptrは同一メモリの所有権を複数で共有できる
~~~cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
	std::shared_ptr<int> ptr;
	{
		std::shared_ptr<int> ptr2(new int(0));
		ptr = ptr2;
		*ptr += 10;
		*ptr2 += 10;
	}
	cout << "ptr=" << *ptr << endl;
}
~~~

### 17項　newで生成したオブジェクトをスマートポインタに渡すのは、独立したステートメントで行うようにしよう
- スマートポインタにnewで作成したポインタを渡した後に、クラスのコンストラクタを使用する
~~~cpp
std::tr1::shared_ptr<Widget>pw(new Widget);
processWidget(pw,priority);
~~~

- [C++11スマートポインタ入門](https://qiita.com/hmito/items/db3b14917120b285112f)


### 18項　インターフェースは、正しく使うときは使いやすく、間違った使い方では使いにくいものにしよう
- 関数の引数をクラスや構造体を作成し、制限する
- 後でもう一度見る

### 19項　クラスのデザインの型のデザインとして考えよう
- いろいろ書いている

### 20項　値渡しよりconst参照渡しを使おう
- オブジェクトを関数の引数とする場合、返り値としてオブジェクトのコピー（コピーコンストラクタ）が返される
~~~cpp
void func(obj a);
~~~
- スライス問題: 派生クラスを引数として渡すとき、派生部分が切り取られ、基底部分のみが参照される
- int, STLは値渡しで渡されることが考えられている
- 組み込み型、STLの反復子、関数オブジェクトは値渡しで、それ以外はconst参照渡し
- スライスされる場合
~~~cpp
#include <iostream>
using namespace std;

class Base
{
protected: //派生クラスからはアクセスできる
	int i;
public:
	Base(int a) { i = a; }
	virtual void display()
	{
		cout << "I am Base class object, i = " << i << endl;
	}
};

class Derived : public Base
{
	int j;
public:
	Derived(int a, int b) : Base(a) { j = b; }
	virtual void display()
	{
		cout << "I am Base class object, i = " << i << ", j = " << j << endl;
	}
};

void somefunc(Base obj)
{
	obj.display();
}

int main() {
	Base b(33);
	Derived d(45, 54);
	somefunc(b); // => I am Base class object, i = 33
	// Object Slicing, the member j of d is sliced off
	somefunc(d);  // => I am Base class object, i = 45
	return 0;
}
~~~
- スライス回避した場合
~~~cpp
#include <iostream>
using namespace std;

class Base
{
protected: //派生クラスからはアクセスできる
	int i;
public:
	Base(int a) { i = a; }
	virtual void display()
	{
		cout << "I am Base class object, i = " << i << endl;
	}
};

class Derived : public Base
{
	int j;
public:
	Derived(int a, int b) : Base(a) { j = b; }
	virtual void display()
	{
		cout << "I am Base class object, i = " << i << ", j = " << j << endl;
	}
};

void somefunc(Base &obj)
{
	obj.display();
}

int main() {
	Base b(33);
	Derived d(45, 54);
	somefunc(b); // => I am Base class object, i = 33
	// Object Slicing, the member j of d is sliced off
	somefunc(d);  // => I am Base class object, i = 45, j = 54
	return 0;
}
~~~
- コンスト参照渡しの場合
~~~cpp
#include <iostream>
using namespace std;

class Base
{
protected: //派生クラスからはアクセスできる
	int i;
public:
	Base(int a) { i = a; }
	virtual void display() const
	{
		cout << "I am Base class object, i = " << i << endl;
	}
};

class Derived : public Base
{
	int j;
public:
	Derived(int a, int b) : Base(a) { j = b; }
	virtual void display() const
	{
		cout << "I am Base class object, i = " << i << ", j = " << j << endl;
	}
};

void somefunc(const Base &obj)
{
	obj.display();
}

int main() {
	Base b(33);
	Derived d(45, 54);
	somefunc(b); // => I am Base class object, i = 33
	// Object Slicing, the member j of d is sliced off
	somefunc(d);  // => I am Base class object, i = 45, j = 54
	return 0;
}
~~~
- ポインタを使用することでもスライス問題を回避できる
~~~cpp
void somefunc(Base *objp)
{
	objp->display();
}

int main() {
	Base *bp = new Base(33);
	Derived *dp = new Derived(45, 54);
	somefunc(bp); // => I am Base class object, i = 33
	// Object Slicing, the member j of d is sliced off
	somefunc(dp);  // => I am Base class object, i = 45, j = 54
	return 0;
}
~~~
- [Object Slicing in C++](geeksforgeeks.org/object-slicing-in-c/)

### 21項　オブジェクトを戻すべき時に参照を戻そうとしないこと



### 参照
- <a href=#3a>*1</a>:[～　第４章　クラスと関数：その設計と宣言　～](http://www002.upp.so-net.ne.jp/ys_oota/effec/chapter4.htm#21kou)
- <a href=#3b>*2</a>:[第１０章　不動の構え](http://www7b.biglobe.ne.jp/~robe/cpphtml/html02/cpp02010.html)
- [C++ビギナーに捧ぐ　EffectiveC++入門](http://www002.upp.so-net.ne.jp/ys_oota/effec/)
- [Effective C++（項1〜5）解説](https://qiita.com/MoriokaReimen/items/58f183d421bb932cbbda)
