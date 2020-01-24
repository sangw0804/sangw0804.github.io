---
layout: post
title: 'DataBinding추상화.md'
date: 2019-12-24 14:30:37 +0900
categories: java spring
---


# Property Binding 추상화

- 기술적인 관점 : 프로퍼티 값을 타겟 객체에 설정하는 기능
- 사용자 관점 : 사용자 입력값을 어플리케이션 도메인 모델에 동적으로 변환해 넣는 기능

> ex. 유저 인풋 "1"을 Event { id: 1 } 인스턴스로 변환해 주는 것!

## ProtertyEditor

- 스프링 3.0 이전까지 DataBinder 빈이 사용하던 인터페이스.
- 쓰레드-세이프 하지 않음 : value 상태값을 저장하고 있음!!

> 따라서 싱글톤 빈으로 등록해서 사용하면, 여러 쓰레드가 status를 읽고 쓰는 과정에서 문제가 생길 수 있다.

- Object 와 String 변환만 할 수 있어 제한적.

```java
// ProtertyEditor 는 인터페이스라 구현해야하는 메소드가 많은데, ProtertyEditerSupport는 클래스라 필요한 메소드만 오버라이딩해서 사용 가능.
public class EventProtertyEditor extends ProtertyEditerSupport {
  @Override
  public String getAsText() {
    return ((Event) getValue()).getTitle();
  }

  @Override
  public void setAsText(String text) throws IllegalArgumentException {
    int id = Integer.parseInt(text);
    Event event = new Event();
    event.setId(id);

    setValue(event);
  }
}
```

## Convertor 와 Formatter

### **Convertor**

- A 타입을 B 타입으로 변환할 수 있는 일반적인 변환기.
- stateless == 스레드 세이프
- ConvertorRegistry 로 등록해서 사용.

```java
class StringToEventConvertor implements Convertor<String, Event> {
  @Override
  public Event convert(String source) {
    Event event = new Event();
    event.setId(Integer.parseInt(source));

    return event;
  }
}
```

### **Formatter**

- PropertyEditor의 대체제
- Object와 String 간 변환 담당.
- 문자열을 Locale에 따라 다국화하는 (MessageSource 사용) 기능 존재.
- FormatterRegistry 에 등록해서 사용.

```java
class EventFormatter implements Formatter<Event> {
  @Override
  public Event parse(String source, Locale locale) throws IllegalArgumentException {
    Event event = new Event();
    event.setId(Integer.parseInt(source));

    return event;
  }

  @Override
  public String print(Event event, Locale locale) {
    return event.getId().toString();
  }
}
```

### **ConversionService**

- 실제 변환 작업은 이 인터페이스를 구현한 빈을 통해, 쓰레드-세이프하게 진행.
- 스프링 MVC, 빈 설정(value), SpEL 에서 사용
- DefaultFormattingConversionService

  - 여러 기본 Convertor & Formatter 등록해줌.

- 스프링 부트
  - 웹 어플리케이션의 경우, DefaultFormattingConversionService 를 상속해서 만든 WebConversionService를 빈으로 자동 등록.
  - WebConversionService빈은 Convertor와 Formatter빈을 찾아 자동으로 등록해준다!
