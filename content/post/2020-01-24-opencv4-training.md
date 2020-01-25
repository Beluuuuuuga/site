---
title: "OpenCV4を用いた画像処理プログラミング入門"
date: '2020-01-24'
categories:
  - ComputerVision
tags:
  - OpenCV
---

1.イントロダクション
============
* 講師:浦西友樹（うらにし　ゆうき）

### どのようにして本を見つけたか？
* 本はどこにある？などの物体認識は難しい
* 目から画像を入力→知識から本の特徴を推定→画像から自分の知っている本に似た物体を探す

### 画像処理・認識技術の応用例
* スマイルシャッター:2000年代の商品は課題を簡単にして処理していた。人間の顔は似ているので処理できる
* アイサイト:限られた状態でしかありえない
* 問題が一般化すると課題を難しくなる

### コンピュータにおけ画像のデータ構造
* コンピュータにおける画像は、1画素（ピクセル）の並び
* スマホですら4K, 2K（800dpi）→普通はどれくらいなのか？
* 画素は青・緑・赤が並び正方形
* 255 = 2^8 - 1 :1画素あたり24(8*3)ビットある: 10000ぐらい
* 画素密度は50倍ぐらいであるが、1画素は長年変化ない
* 300dpi(dot / inchi)が人間がアナログデータとデジタルを認識できる限界: 大画面でみるとdpiが下がる: 物理的にテレビの限界がある
* ディスプレイが明るさを出せる幅は人間が対応できる（知覚できる）幅に対して狭いので24ビットから変化がない

### C言語による画像処理プログラミング
* データに対するアクセスは配列に対する処理
* ビットマップファイルはヘッダに書かれた画像情報を読み取る必要がある（縦1000*横1000 OR 縦1*横10000）
* エンコードされたデータを扱うためにデータ構造を理解しておく必要がある（デコードするために）
* ビットマップファイルを読み込んで処理はアップルウォッチでさえできる

### OpenCV(Open Conputer Vision Library)
* 1990年代にIntelが開発していたImageProcessingLibraryが元
* Intel→2008 Willow Garage→Intel: 開発者の移動に伴っている
* BSD(Berkeley Software Distribution)ライセンスに基づいている
* マルチプロットフォーム(win/linux/mac/ios/and, c/c++/python/java)
* バックエンドでCPU/GPU
* 現在2000万ヒットとデファクトスタンダードとなっている
* ダウンロードトップカントリーはかつてはアメリカ・日本だが、現在は中国がトップ。韓国・日本が３番手を争う
* CLIではダウンロードカウントされない

### OpenCVでできること
* 画像に関することはなんでもできる

### OpenCVの利点
* 産総研ではspyderやMatlabなどのライブラリがあったが、2000にでてきたOpenCVは簡単である
* 簡単:インストール方法が確立、最新アルゴリズムも関数を呼び出すことで組み込める
* ライブラリを用いているが、早い
* BSDライセンスなので、商用利用であってもライセンス表示するのみで、ソースコードを公開することなく利用できる

### OpenCVのバージョンの変遷 
* 2006. 10 1.0: C
* 2009.  9 2.0: C/C++
* 2015.  6 3.0
* 2.0系では少しの変化でもバージョンがアップしていた
* 3.0系ではbaseとcontributionsで分けられている
* インストーラーではbaseのみがダウンロードされる
* contoributionsでは特許で保護されている

### T-API(Transparent API)
* 3.0以降では実行環境によってGPUとCPUが使い分けられる: インストーラーでも同じ
* 内臓では7倍から10倍の速度向上が見込める

### OpenCV 4.0
* C言語のインターフェースを消している
* 深層学習などもモジュールが強くなっている
* Kinect Fusionの実装とは？
* QRコード検出およびデコーダの実装
* C++11に対応
* 3.0と基本的な使い方は変わらない
* 4.0は2018年に発表

2.開発環境の導入
============

### OpenCVインストール
* Win32をビルドする場合はx64ではなくx84にディレクトリパスを読み替える
* それぞれ対応　vc14 => VS 2015, vc15 => VS 2017

### Visual Studioで使われる用語
* プロジェクト: 複数のソースファイルを束ねて1つの実行可能ファイルを作成する単位
* ソリューション: 複数のプロジェクトをまとめる単位 
* コンパイル: コンパイルすると関数ごとにオブジェクトコードが生成される
* リンク: 生成されたオブジェクトコードをリンクという
* ビルド: コンパイルとリンクを作業をビルドという。linuxだとmakeという
* 実行可能ファイル(.exe)は実行時にopencvのdllを参照
* リンカーエラー: OpenCVのライブラリファイルのありかが間違えてる
* VSでは、どこのエラーか教えてくれる。コンパイルエラーでは細かく教えてくれる

### Visual Studioの設定と動作確認
* コンソールの空プロジェクトの場合、構成のC/C++が存在しないのでファイルを作成する必要がある
* 設定の際に構成はすべての構成にする
* ビルドの方法は3種類
* 構成プロパティのリンカの入力に書き込むかヘッダに書き込んで読み込む

~~~cpp
#ifdef _DEBUG
#pragma comment(lib, "opencv_world412d.lib")
#else
#pragma comment(lib, "opencv_world412.lib")
#endif
~~~

* 空のウィンドウが1枚描画されれば正常に動作

~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>


int main() {
	cv::Mat src(480, 640, CV_8UC3);
	cv::imshow("sourse", src);
	cv::waitKey(0);
	return 0;
}

~~~
* hederファイルを参照することなくmainに書き込むとき

~~~cpp
# include <opencv2/opencv.hpp>

#ifdef _DEBUG
#pragma comment(lib, "opencv_world412d.lib")
#else
#pragma comment(lib, "opencv_world412.lib")
#endif

int main() {
	cv::Mat src(480, 640, CV_8UC3);
	cv::imshow("sourse", src);
	cv::waitKey(0);
	return 0;
}
~~~

* コードの説明

~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>


int main() {
	// cv Mat型のsrcの変数という宣言; 行列
	// 縦480 * 横640
	// CV_8UC3; 8=>8bit(depth)
	// U=>Unsigned(符号なし 0〜255)
	// S=>signed(-128〜127まで)、差分で表現するときなどはsignedにするときもある
	// 3=>ch=3、3色青・緑・赤
	cv::Mat src(480, 640, CV_8UC3);
	// "source"=>windowの識別で文字列一致、source2にすると違うwindowになる
	cv::imshow("source", src);
	// waitKey(0)=>keyを待つ、0もしくは負の値を入れた場合に入力を無限に待つ
	cv::waitKey(0);
	return 0;
}

~~~

### その他のライブラリ
* Caffe by BVLC@UCB
* Anacondaのダウンロード: https://www.anaconda.com/distribution/
* OpenCV for Pythonのダウンロード: https://www.lfd.uci.edu/~gohlke/pythonlibs/
* opencv_python-m.n.o+contrib-cpXX-cpXXm-win_amd64.whlを上記からダウンロード
* Macでは1つのプロジェクトで数百メガバイトのバイナリファイルが作成されてしまうので、Visual Studioにライブラリを紐づけるのが最適解


3.OpenCVの基礎的なプログラム
============

### 画像ファイルの読み込みと保存

* カレントディレクトリを確認する必要がある
=> プロジェクトファイル(.vcxproj)が存在している場所
* opencvにはサンプルデータがたくさんあるopencv\sources\samples\data
 
~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>


int main(int argc, char** argv) { // 引数は省略可能
	// 引数を宣言、画像を読み込み
	cv::Mat src = cv::imread("sample.jpg");
	// 画像を表示
	cv::imshow("source", src);
	// 画像を保存
	cv::imwrite("output.jpg", src);
	cv::waitKey(0);
	return 0;
}
~~~

* pngで読み込んでjpgに変換できる

~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>


int main(int argc, char** argv) { // 引数は省略可能
	// 引数を宣言、画像を読み込み
	cv::Mat src = cv::imread("sample.png");
	// 画像を表示
	cv::imshow("source", src);
	// 画像を保存
	cv::imwrite("output.jpg", src);
	cv::waitKey(0);
	return 0;
}
~~~

### カメラ画像の取り込み
* 動画ファイルなども扱うこともできる
* escキーを押すと終了する

~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>


int main(int argc, char** argv) { // 引数は省略可能
	// 引数を宣言、画像を読み込み
	cv::VideoCapture capture(0);
	cv::Mat frame;
	// ウィンドウを作成
	cv::namedWindow("Camera Image");
	for (;;) {
		capture >> frame; // カメラ画像を取り込む
		cv::imshow("Camera Image", frame);
		if (cv::waitKey(30) == 27) { // 27はescキー
			break;
		}
	}
	return 0;
}
~~~

### 幾何的画像処理
* **スケーリング**, **シアーリング**, **回転**: 2行2列
* **アフィン変換**: 2行3列
  * 回転・縮小・平行移動はできるが、奥行きを表すことはできない
* **射影変換**: 奥行きを表すことができる
* 幾何変換の変換行列を先に求めることは困難なので移動後の点を使用して、変換行列を求める　P^' = AP: Aを求める

### 光学的画像処理
* **画像の色変換**
  * 動画画像がグレーで映る

~~~cpp
#include "stdafx.h"
# include <opencv2/opencv.hpp>

int main(int argc, char** argv) { // 引数は省略可能
	// 引数を宣言、画像を読み込み
	cv::VideoCapture capture(0);
	cv:: Mat frame, gray;


	// ウィンドウを作成
	cv::namedWindow("Camera Image");
	do {
		capture >> frame;
		// 3チャンネルRGBからグレースケールに変換
		cv::cvtColor(frame, gray, cv::COLOR_BGR2GRAY);
		
		cv::imshow("Camera Image", gray);
	} while (cv::waitKey(10) != 27);

	
	return 0;
}
~~~

* **2値化**
  * グレースケールで入力する必要がある
  * OpenCVの引数では(input, input, binary, binary, output)みたいな感じになる

4.OpenCVの基礎的なプログラム
============

### 画素への直接アクセス
* 画像は二次元だが一次元でアクセスする必要がある; ラスタ表現
* RGBであれば、1画素に3つの成分が存在
* 各行の先頭の配列のポインターを直接アクセスする関数: src.ptr<uchar>
  * 1つが高速化のためと自分でライブラリを作成するときに使用するため
  * しかし、開発のメモリに直接アクセスするため未知のバグを引き起こす可能性があり、バグを知らせてくれるわけではない

### 空間フィルタ
* **フィルタ演算**: カーネルと同じ領域に対して、処理を行う
  * エッジ検出で使用される
  * エッジ検出ではフィルタ演算を行う: Sobel(Vertical),Sobel(Horizontal),Laplacian
  * ノイズ除去ではフィルタはすべてで1/9などにする; しかし画像全体がぼやけてしまう


### 2値画像処理
* 数を数えるときなどに使用する; 2値化=>ラベリング=>カウント
* **ラベリング**
  * 関数がラベリングではないので注意: int cv::connectedComponents()
* **輪郭線追跡**; 形状を知りたいときとかに使用、どれだけ円に近いのかなど
* **直線検出**: y = ax + b のように直線があるかどうかを検出する
  * y = ax + bに対して原点から垂線を下ろし、ρとΘ（ρ-Θ平面）で表現する(有限の値): 計算機でa,bは無限の値を持つので値を変換する
　* 点に対する曲線を描画し、重なりによって何本重ねっていれば直線のように判断する
  * ハフ変換: [直線を検出するHough変換をやさしく解説](http://www.allisone.co.jp/html/Notes/image/Hough/index.html)

### テンプレートマッチング
* 1つのテンプレートでは対処できない
* **局所特徴量**: 拡大・縮小・回転に対して変化が少ない部分
  * SIFTなど
  * ディープラーニング以前の考え方
  * あくまでルールベースで一般的ではない

### 機械学習による物体認識
* 人間によってハンドクラフトで特徴量を作成する必要がなくなった
* 特徴量を機械自身が獲得する

* **物体認識**: 特定物体認識(Identificaiton)と一般物体認識(Classification)に分けられる 
* **特徴量抽出**: 100*100だと10000次元データが存在し、データとして大きいので、抽象度を上げた特徴量を抽出する(圧縮)
  * 円形度を求める: C = 4*pi*S/(L^2); 円であった場合 L=2*pi*rになるのでC=1となる
  * その他の方法: NormalBayesClassifier, KNearest, SVM
  * データを持っているほうが有利になっている

### OpenCLでOpenCV
* **OpenCL(Open Computing Language): #include <opencv2/core/ocl.hpp>宣言することでGPUを作成できる

質問
============
* 画像処理系の論文は
=> トップカンファレンスの論文を読む、ICCV, ECCV, CVPR 3強 [MIRU](http://cvim.ipsj.or.jp/MIRU2019/)(ミル日本)
* C++で機械学習を使用するときはどうしているのか
=> 基本C++でしない、一応ある、、
* 最新の情報はどこから得ているか
=> 学会に行って情報収集、MIRUとかで情報収集できる
* 画像処理で必要な数式はどのようにして身に着けることができるのか
=> 線形代数、群論、元、数値計算系が多い、誤差を定義したりする
* C++でtensorflowを使用するにはどうすればいいのか？おすすめのライブラリなど
=> 結論的に難しい
* 画像処理などを考慮した企業で技術的に強い企業は？
=> 
* ファイルにアクセスして処理するときは？
=> ファイルを読み込むとき、for文でくるんで回す; シェルスクリプトでファイルアクセスを行う

References
============
* [Yuki Uranishi, Ph.D.](https://sites.google.com/site/uranishi/opencv_imageprocessing/python_opencv)
* コンピュータビジョンアルゴリズムと応用が良い
* [SlideShare資料](https://www.slideshare.net/uranishi/opencv-64929179)
