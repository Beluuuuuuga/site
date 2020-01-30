---
title: "CMakeまとめ"
date: '2020-01-23'
categories:
  - OpenSource
tags:
  - CMake
---


Usages
============

### ビルド
- buildディレクトリを作成し、移動
- Visual Studioのバージョンによってビルド方法変更
  - 2017の場合、Gの部分を **Visual Studio 15 2017** に変更する

~~~
mkdir build
cd build
cmake -G "Visual Studio 14 2015" -A x64 ..
cmake --build . --config Debug
~~~

- ビルド後はbuild内でプロジェクトファイル(.vcxproj)が作成されるので、Visual Studioなどで開く
- Visual Studioでビルドする場合は、プロジェクトを右クリックし、スターティングプロジェクトにして実行

References
============
