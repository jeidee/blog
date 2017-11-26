---
id: 283
title: virtual box vdi 용량 늘리기
date: 2015-03-16T12:48:44+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=283
permalink: '/2015/03/16/virtual-box-vdi-%ec%9a%a9%eb%9f%89-%eb%8a%98%eb%a6%ac%ea%b8%b0/'
categories:
  - utility
tags:
  - virtual box
---
windows 7에 virtual box를 설치하고 xubuntu를 guest os로 설치했다고 가정한다.
  
저장소는 vmdk일 경우 먼저 vdi로 변경 후 사이즈를 조절하도록 한다.

## vdi 용량 늘리기

1) vmdk를 vdi로 변경

cmd를 열고 vmdk 파일이 있는 곳으로 이동한다.

VBoxManage.exe의 clonehd 기능으로 vmdk파일을 vdi로 변경한다.

```
  
> "C:Program FilesOracleVirtualBoxVBoxManage.exe" clonehd server1-disk1.vmdk server1-disk1.vdi &#8211;format vdi
  
```

2) vdi 사이즈 수정

vdi의 사이즈를 변경하기 위해서 VBoxManage.exe의 modifyhd 기능으로 사이즈를 변경한다. &#8211;resize 매개변수의 값은 단위가 MB이며 30GB를 원할 경우 30 * 1024 = 31744를 입력하도록 한다.

```
  
> "C:Program FilesOracleVirtualBoxVBoxManage.exe" modifyhd server1-disk1.vdi &#8211;resize 31744
  
```

3) VirtualBox GUI 관리자에서 설정>저장소를 선택 후 기존 vmdk 연결을 제거하고 새로운 vdi 파일을 추가해 준다.

## 우분투 파티션 변경

gparted를 사용해서 파티션을 변경한다.

1) gparted 설치

```
  
sudo apt-get install gparted
  
```

2) gparted 실행

```
  
sudo gparted
  
```

3) 파티션 합치기

디스크 사이즈를 조정했다면 할당되지 않은 새로운 공간이 보일 것이다.
  
주의할 것은 원래 파티션과 새로운 할당되지 않은 빈 공간 사이에 다른 파티션이 있으면 안된다.

만약, 있다면 해당 파티션을 제거해야 한다.

원래의 파티션(합칠 대상)에서 크기조정/이동 메뉴를 선택한 후 원하는 크기만큼 변경하고 v 체크 버튼을 클릭해 변경된 내용을 적용하면 끝이다.

&nbsp;

## centos에서 확장

  * \[링크 참조\](https://gist.github.com/christopher-hopper/9755310)