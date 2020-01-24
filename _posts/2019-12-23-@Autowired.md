---
layout: post
title: '@Autowired.md'
date: 2019-12-23 14:30:37 +0900
categories: java spring
---


# @Autowired

> 필요한 의존 객체의 "타입" 에 해당되는 빈을 찾아 주입한다.

## 사용 위치

- 생성자
- setter
- 필드

## 경우의 수

- 해당 타입의 빈이 IoC 컨테이너에 없는 경우 => 에러
- 해당 타입의 빈이 IoC 컨테이너에 1개인 경우 => 그 빈을 주입
- 해당 타입의 빈이 IoC 컨테이너에 2개 이상인 경우
  - @Primary 빈 우선
  - @Qualifier("빈 이름") 으로 찾기

## 동작 원리

- Bean Lifecycle 에서 BeanPostProcessor 라이프 사이클에 실행
  - 새로 만들어진 빈 인스턴스를 수정할 수 있는 라이프사이클
- AutowiredAnnotationBeanPostprocessor extends BeanPostProcessor
  - @Autowired, @Value 어노테이션을 처리하는 빈.

```java
@Component
class Car {
  private Tire tire;

  @Autowired
  @Qualifier("koreaTire")
  public void setTire(Tire tire) {
    this.tire = tire;
  }
}

@Component
class KoreaTire implements Tire {

}
```
