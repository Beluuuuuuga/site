---
title: "OpenCV4環境構築"
date: '2020-01-23'
categories:
  - ComputerVision
tags:
  - OpenCV
---

Environment
============

Windows
------------

* 公式からインストール。[Releases](https://opencv.org/releases/)を参照<sup><a href=#1>*1</a><sup>
* 展開し環境変数にbinまで登録　C:\OpenCV\OpenCV34\opencv\build\x64\vc14\bin
* VisualStudioで新しいプロジェクトを作成
* プロジェクトのプロパティのインクルードディレクトリ・リンカのライブラリ・入力の依存ファイル(Releaseはdなし、Debugはdあり)を登録
* テストを行いビルドし、小さい窓が出れば完了

~~~cpp
#include <opencv2/opencv.hpp>

using namespace cv;

int main()
{
    Mat image = Mat::zeros(100, 100, CV_8UC3);
    imshow("", image);
    waitKey(0);
}
~~~


* はまりやすいポイントととして、リンカーの入力ファイルをwindowsのGUI上で見ると.libが消えており、ビルドできなかった
* OpenCV4系でコードを書いているサイトを検索するように心がける。本当はビルドできるのにビルドできてないと怒られたので<sup><a href=#5>*5</a><sup>

### References ###
* <a href=#1>*1</a>:[Releases](https://opencv.org/releases/)
* <a href=#2>*2</a>:[【覚書】VisualStudioでOpenCVを使う場合の簡単な各種設定（Windows10 64Bit版用）【環境構築】](https://madeinpc.blog.fc2.com/blog-entry-1277.html)
* <a href=#3>*3</a>:[KinectV2 VisualStudio2017でのLNK2019](https://teratail.com/questions/100794)
* <a href=#4>*4</a>:[Visual Studio 2015でOpenCV 3.4環境構築(Windows10)](http://tecsingularity.com/opencv/opencv34/)
* <a href=#5>*5</a>:[OpenCV 4.2.0をVisual Studio 2019から使用する時の手順](https://qiita.com/h-adachi/items/aad3401b8900438b2acd)