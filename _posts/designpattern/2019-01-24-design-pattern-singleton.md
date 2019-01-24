---
layout: post
title: 싱글톤(Singleton) 패턴
category: 디자인패턴
permalink: /designpattern/:year/:month/:day/:title/

tag: [Design Pattern, S/W]
---


디자인 패턴

#UML

싱글톤 패턴의 UML은 다음과 같습니다. 클래스 하나 뿐이기 때문에 UML이 단순합니다.

![Image](/assets/design-patterns/singleton.gif)

<br>

# 예제 코드

싱글톤 패턴을 코드로 구현하면 다음과 같습니다.


<pre class="prettyprint">public class Singleton {

  private static Singleton mInstance = null;

  private Singleton() {
  }

  public static Singleton getInstance() {
    if(mInstance == null) {
      mInstance = new Singleton();
    }

    return mInstance;
  }
}
</pre>
<br>

위 코드는 멀티쓰레드 환경에서 getInstace() 메소드가 끝나기 전에 여러 쓰레드에서
동시에 접근을 할 수 있기 때문에 재수가 없으면 Singleton 인스턴스가 여러 개 생성될 수 있습니다.

<br>


