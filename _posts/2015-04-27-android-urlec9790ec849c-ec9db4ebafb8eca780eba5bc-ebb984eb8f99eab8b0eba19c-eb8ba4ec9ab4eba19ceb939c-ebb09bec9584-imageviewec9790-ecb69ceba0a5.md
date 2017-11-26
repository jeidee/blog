---
id: 325
title: android url에서 이미지를 비동기로 다운로드 받아 ImageView에 출력하기
date: 2015-04-27T21:09:00+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=325
permalink: '/2015/04/27/android-url%ec%97%90%ec%84%9c-%ec%9d%b4%eb%af%b8%ec%a7%80%eb%a5%bc-%eb%b9%84%eb%8f%99%ea%b8%b0%eb%a1%9c-%eb%8b%a4%ec%9a%b4%eb%a1%9c%eb%93%9c-%eb%b0%9b%ec%95%84-imageview%ec%97%90-%ec%b6%9c%eb%a0%a5/'
categories:
  - android
tags:
  - asynctask
  - image
---
Custome List Adapter의 getView() 함수 안에서 다음과 같이 사용한다.

```java
  
new DownloadImageTask(pView.GetIconView()).execute(roster.vcard().photo());
  
```

```java
      
private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
          
ImageView bmImage;

public DownloadImageTask(ImageView bmImage) {
              
this.bmImage = bmImage;
          
}

protected Bitmap doInBackground(String&#8230; urls) {
              
String urldisplay = urls[0];
              
Bitmap mIcon11 = null;
              
try {
                  
InputStream in = new java.net.URL(urldisplay).openStream();
                  
mIcon11 = BitmapFactory.decodeStream(in);
              
} catch (Exception e) {
                  
Log.e("Error", e.getMessage());
                  
e.printStackTrace();
              
}
              
return mIcon11;
          
}

protected void onPostExecute(Bitmap result) {
              
bmImage.setImageBitmap(result);
          
}
      
}
  
```

## 출처

  * [Load image from url](http://stackoverflow.com/questions/5776851/load-image-from-url)