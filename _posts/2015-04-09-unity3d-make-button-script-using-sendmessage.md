---
title: "unity3d SendMessage를 사용한 Sprite 버튼 클릭 스크립트 만들기"
date: 2015-04-09T10:07:39+09:00
author: jeidee
categories:
- game programming
tags:
- unity3d
---

이미지를 사용해서 Sprite 오브젝트를 씬에 추가했다고 가정한다.

먼저 해당 Sprite에 Collider2D 컴포넌트를 추가한다.(Physics2D->Box Collider 2D)

그런 후 다음 스크립트를 생성해서 해당 오브젝트에 스크립트 컴포넌트로 추가한다.

```
using UnityEngine;
using System.Collections;

public class PlayButton : MonoBehaviour {

	// Use this for initialization
	void Start () {
	
	}
	
	// Update is called once per frame
	void Update () {
		//Debug.Log (Application.platform);
		if(Application.platform == RuntimePlatform.Android || Application.platform == RuntimePlatform.IPhonePlayer){
			if(Input.touchCount > 0) {
				if(Input.GetTouch(0).phase == TouchPhase.Began){
					checkTouch(Input.GetTouch(0).position);
				}
			}
		} else {
			if(Input.GetMouseButtonDown(0)) {
				checkTouch(Input.mousePosition);
			}
		}
	}

	void checkTouch(Vector2 pos) {

		Vector3 wp = Camera.main.ScreenToWorldPoint(pos);
		Vector2 touchPos = new Vector2(wp.x, wp.y);

		var collider = gameObject.GetComponent<Collider2D> ();
		if (collider.OverlapPoint (touchPos)) {
			gameObject.SendMessage("Clicked",0,SendMessageOptions.DontRequireReceiver);
		}
		
	}

	void Clicked() {
		Debug.Log ("Clicked");
	}
}
```
34 라인에서 gameObject.SendMessage("Clicked", ...) 함수 호출에 의해 해당 게임 오브젝트의 Clicked 메소드에 메세지를 전달하게 된다.([동일 프레임](http://answers.unity3d.com/questions/253157/is-sendmessage-sync-or-async.html)에서 처리되므로 동기 호출된다고 봐야 할 듯 싶다.)

SendMessage에 대한 좀 더 자세한 내용은 [공식문서](http://docs.unity3d.com/ScriptReference/GameObject.SendMessage.html)를 참고하도록 한다.