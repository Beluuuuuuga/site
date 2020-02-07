---
title: "OpenCV4使い方"
date: '2020-02-04'
categories:
  - ComputerVision
tags:
  - OpenCV
---

輪郭描画
------------
- 一番参考にしたサイト
- [OpenCVのfindContoursを利用した文字切り出し](https://www.tcmobile.jp/dev_blog/programming/opencv%E3%81%AEfindcontours%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%9F%E6%96%87%E5%AD%97%E5%88%87%E3%82%8A%E5%87%BA%E3%81%97/)
- OpenCVは2値画像では白をモノとして認識する
- [移動する物体を追跡する](https://cvtech.cc/tracking/2/)
- void findContours(const Mat& image, vector<vector<Point> >& contours, int mode, int method, Point offset=Point())
- [cv::findContours](http://opencv.jp/opencv-2svn/cpp/structural_analysis_and_shape_descriptors.html#cv-findcontours)
- OpenCV4でのグレースケール変換はOpenCV3と異なる
- [Compile error in the examples with OpenCV 4.0.0-alpha ](https://github.com/Dovyski/cvui/issues/52)


~~~cpp
#include <opencv2/opencv.hpp>
#include <vector>

//最小座標を求める
cv::Point minPoint(vector<cv::Point> contours) {
	double minx = contours.at(0).x;
	double miny = contours.at(0).y;
	for (int i = 1; i<contours.size(); i++) {
		if (minx > contours.at(i).x) {
			minx = contours.at(i).x;
		}
		if (miny > contours.at(i).y) {
			miny = contours.at(i).y;
		}
	}
	return cv::Point(minx, miny);
}
//最大座標を求める
cv::Point maxPoint(vector<cv::Point> contours) {
	double maxx = contours.at(0).x;
	double maxy = contours.at(0).y;
	for (int i = 1; i<contours.size(); i++) {
		if (maxx < contours.at(i).x) {
			maxx = contours.at(i).x;
		}
		if (maxy < contours.at(i).y) {
			maxy = contours.at(i).y;
		}
	}
	return cv::Point(maxx, maxy);
}

int main() {
	//cv::Mat src(480, 640, CV_8UC3);
	//cv::imshow("sourse", src);
	//cv::waitKey(0);
	//return 0;
	cv::Mat image = cv::imread("sample.png");
	// グレースケールに変換 opencv4
	cv::Mat grayImage;
	cv::cvtColor(image, grayImage, cv::COLOR_BGR2GRAY);
	//cv::imshow("sourse", grayImage);
	// 二値画像に変換
	cv::Mat binaryImage, binaryInvImage;
	const double threshold = 100.0;
	const double maxValue = 255.0;
	//THRESH_BINARY 二値画像
	cv::threshold(grayImage, binaryImage, threshold, maxValue, cv::THRESH_BINARY);
	cv::threshold(grayImage, binaryImage, threshold, maxValue, cv::ThresholdTypes::THRESH_BINARY); // ThresholdTypesをつけても問題ない
	//THRESH_BINARY_INV 二値反転画像
	cv::threshold(grayImage, binaryInvImage, threshold, maxValue, cv::THRESH_BINARY_INV);
	//binaryImage = ~binaryImage; //二値化した後に反転
	// 出力
	cv::imshow("w1", binaryInvImage);
	//cv::imshow("w2", binaryImage);
	// 輪郭算出 contoursは画像の4隅
	vector< vector<cv::Point> > contours;
	vector<cv::Vec4i> hierarchy;
	cv::findContours(binaryInvImage, contours, hierarchy, cv::RETR_EXTERNAL, cv::CHAIN_APPROX_SIMPLE, cv::Point(0, 0));
	//各輪郭の最大最小座標を求める
	for (int i = 0; i<contours.size(); i++) {
		cout << contours.at(i) << endl;
		cv::Point minP = minPoint(contours.at(i));
		cv::Point maxP = maxPoint(contours.at(i));
		cv::Rect rect(minP, maxP);
		//矩形を描く
		cv::rectangle(image, rect, cv::Scalar(0, 255, 0), 2, 8);
	}
	// 出力
	cv::imshow("w3", image); // 元の画像に矩形を描く
	cv::waitKey(0);
	return 0;
}
~~~

描画
------------
- windowサイズを調整
- [High-level GUI](https://docs.opencv.org/3.4/d7/dfc/group__highgui.html#ggabf7d2c5625bc59ac130287f925557ac3a39cee9c8d57caf2368eaf60980dc5d70)

~~~cpp
	cv::namedWindow("gloss", cv::WINDOW_NORMAL);
	cv::resizeWindow("gloss", 800, 350);
	cv::imshow("gloss", glossBaseImg);
~~~

- セグメンテーションのときに対象クラスでなんだかんだするとき

~~~cpp
	cv::Mat imgTmp;
	cv::bitwise_xor(imgClsf, cv::Scalar(5), imgTmp); //idxClsf2のときだけscalor5で保存, オーバーラップ部分は5なので、足してあげてる
	cv::threshold(imgTmp, imgTmp, 0, 255, cv::ThresholdTypes::THRESH_BINARY_INV);
~~~

### References ###
