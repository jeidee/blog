---
title: "unity3d 애니메이션이 완료되거나 특정 부분까지 재생될 때 까지 기다리는 코루틴 만들기"
date: 2015-11-14T10:07:39+09:00
author: jeidee
categories:
- game programming
tags:
- unity3d
---

## 특정횟수의 프레임이 재생될 때까지 기다리는 코루틴

```csharp
	IEnumerator testCoroutine() {
		int i = 0;
		while (true) {
			yield return StartCoroutine(waitForPassFrame(3));
			print ("testCoroutine..." + i++.ToString());
		}
	}

	IEnumerator waitForPassFrame(int frame) {
		int playFrame = 0;

		while (playFrame++ < frame) {
			print ("wait for end of frame..." + playFrame.ToString());
			yield return new WaitForEndOfFrame();
		}
	}

```
위의 코드를 실행하면 다음과 같은 출력을 볼 수 있다.

```
wait for end of frame...1
wait for end of frame...2
wait for end of frame...3
testCoroutine...0
wait for end of frame...4
wait for end of frame...5
wait for end of frame...6
testCoroutine...1
...
```

위와 같은 특성을 활용해서 애니메이션이 특정 비율까지 재생될 때까지 기다렸다가 특정 로직을 처리할 수 있는 코루틴을 만들 수 있다.

```csharp
	//Wait for an animation to be a certain amount complete
	IEnumerator WaitForAnimation(string name, float ratio, bool play)
	{
		//Get the animation state for the named animation
		var anim = animation[name];
		//Play the animation
		if(play) animation.Play(name);
		
		//Loop until the normalized time reports a value
		//greater than our ratio.  This method of waiting for
		//an animation accounts for the speed fluctuating as the
		//animation is played.
		while(anim.normalizedTime + float.Epsilon + Time.deltaTime < ratio)
			yield return new WaitForEndOfFrame();
		
	}

	IEnumerator Die()
	{
		//Wait for the die animation to be 50% complete
		yield return StartCoroutine(WaitForAnimation("die",0.5f, true));
		//Drop the enemies on dying pickup
		DropPickupItem();
		//Wait for the animation to complete
		yield return StartCoroutine(WaitForAnimation("die",1f, false));
		Destroy(gameObject);
		
	}
```
* 원문은 [coroutine++ 포스트](https://unitygem.wordpress.com/coroutines/)에서 볼 수 있다.