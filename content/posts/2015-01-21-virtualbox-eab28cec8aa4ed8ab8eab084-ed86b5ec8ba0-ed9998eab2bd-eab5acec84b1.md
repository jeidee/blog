---
author: jeidee
categories:
- utility
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/virtualbox-%ea%b2%8c%ec%8a%a4%ed%8a%b8%ea%b0%84-%ed%86%b5%ec%8b%a0-%ed%99%98%ea%b2%bd-%ea%b5%ac%ec%84%b1/
id: 36
permalink: /2015/01/21/virtualbox-%ea%b2%8c%ec%8a%a4%ed%8a%b8%ea%b0%84-%ed%86%b5%ec%8b%a0-%ed%99%98%ea%b2%bd-%ea%b5%ac%ec%84%b1/
tags:
- virtualbox
title: virtualbox 게스트간 통신 환경 구성
url: /2015/01/21/virtualbox-eab28cec8aa4ed8ab8eab084-ed86b5ec8ba0-ed9998eab2bd-eab5acec84b1
---

virtualbox에 다음과 같은 환경을 구축하려고 한다.

  * 호스트 
      * Windows 7
  * 게스트 
      * ubuntu 14.10 desktop #1
      * ubuntu 14.10 desktop #2

호스트와 게스트간 통신도 가능해야 하고 게스트간 통신도 가능해야 한다.

  1. virtualbox에서 파일>환경설정>네트워크를 선택한다.
  2. 호스트 전용 네트워크가 비어 있을 경우 새로 추가한다.
  3. 추가한 호스트 네트워크를 더블 클릭한다.
  4. IPv4 주소를 192.168.56.1로 설정한다.
  5. IPv4 서브넷 마스크를 255.255.255.0으로 설정한다.
  6. DHCP 서버 사용에 체크를 해제한다.
  7. ubuntu 게스트 OS를 설치하고 게스트 OS의 네트워크 설정을 선택한다.
  8. 어댑터 1에는 NAT를 선택하고,
  9. 어댑터 2에는 호스트 전용 어댑터를 선택한다.
 10. ubuntu 게스트 OS를 실행하고 ifconfig를 입력해 nic이 두 개 모두 정상 설정되었는지 확인한다.
 11. 두 번째 카드의 ip를 192.168.56.101로 설정한다.

두번째 게스트 OS는 #1을 복제해서 IP를 변경한다.
  
게스트 OS간 ping을 실행해 정상적으로 통신이 가능한지 확인한다.