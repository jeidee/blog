---
title: "unity3d fading scene"
date: 2015-04-18T10:07:39+09:00
author: jeidee
categories:
- game programming
tags:
- unity3d
---

유니티에서 씬 전환시 fade-in/out을 사용하기 위해 다음 스크립트를 사용한다.

```csharp
using UnityEngine;
using System.Collections;

public class Fading : MonoBehaviour {

	public Texture2D fadeOutTexture;	// the texture that will overlay the screen. This can be a black image or a loading graphic
	public float fadeSpeed = 0.8f;		// the fading speed

	private int drawDepth = -1000;		// the texture's order in the draw hierarchy: a low number means it renders on top
	private float alpha = 1.0f;			// the texture's alpha value between 0 and 1
	private int fadeDir = -1;			// the direction to fade: in = -1 or out = 1
	private bool skip = false;

	void Start() {
		StartCoroutine ("FadeComplete");
	}

	IEnumerator FadeComplete() {
		skip = false;
		yield return null;

		yield return new WaitForSeconds(fadeSpeed);
		skip = true;
	}

	void OnGUI()
	{
		if (skip)
			return;

		// fade out/in the alpha value using a direction, a speed and Time.deltaTime to convert the operation to seconds
		alpha += fadeDir * fadeSpeed * Time.deltaTime;
		// force (clamp) the number to be between 0 and 1 because GUI.color uses Alpha values between 0 and 1
		alpha = Mathf.Clamp01(alpha);

		// set color of our GUI (in this case our texture). All color values remain the same & the Alpha is set to the alpha variable
		GUI.color = new Color (GUI.color.r, GUI.color.g, GUI.color.b, alpha);
		GUI.depth = drawDepth;																// make the black texture render on top (drawn last)
		GUI.DrawTexture(new Rect(0, 0, Screen.width, Screen.height), fadeOutTexture);		// draw the texture to fit the entire screen area
	}

	// sets fadeDir to the direction parameter making the scene fade in if -1 and out if 1
	public float BeginFade (int direction)
	{
		StartCoroutine ("FadeComplete");
		fadeDir = direction;
		return (fadeSpeed);
	}

	// OnLevelWasLoaded is called when a level is loaded. It takes loaded level index (int) as a parameter so you can limit the fade in to certain scenes.
	void OnLevelWasLoaded()
	{
		// alpha = 1;		// use this if the alpha is not set to 1 by default
		BeginFade(-1);		// call the fade in function
	}
}
```
위 스크립트는 [2D Platformer Cource](http://brackeys.com/preview/2d-platformer-course/) 사이트에서 2D Assets Pack을 다운로드 받으면 Fading.cs파일이 포함되어 있다.

해당 스크립트에서 Fading이 완료된 후  OnGUI() 코드가 더 이상 실행되지 않도록 FadeComplete 코루틴을 추가했다.

위 스크립트를 씬의 빈 오브젝트에 추가한 후 fadeOutTexture(리소스 절약을 위해서 1픽셀의 단일 색상)를 할당하면 된다.

Fading.cs 스크립트는 다음과 같이 동작한다.

- 씬 로드시 Fade-in
- 외부의 특정 이벤트(보통 씬 전환 시) 발생시 BeginFade(1) 함수를 호출해서 Fade-out

BeginFade(1)를 호출하는 예는 다음과 같다.

```csharp
	IEnumerator ChangeLevel() {
		float wait = GetComponent<Fading> ().BeginFade (1);
		yield return new WaitForSeconds (wait);

		GetComponent<AudioSource> ().Play ();
		Application.LoadLevel (1);
	}
```

## 출처

* [Fading between scenes](http://unity3d.com/learn/tutorials/modules/intermediate/graphics/fading-between-scenes)
* [2D Platformer Cource](http://brackeys.com/preview/2d-platformer-course/)