---
layout: post
title: 'Embedded & Referenced DB Schema'
date: 2018-08-24 17:30:37 +0900
categories: jekyll update
---

# Embedded & referenced data

<br>

## 0. 시작하기 전에

---

- mongodb / db.collection.document

- 하나의 collection 안에서도 서로 다른 schema 를 가질 수 있음! 하지만 schema validation 을 이용해 mongodb 자체에서 schema 를 강제할 수 있다.

<br>

## 1. Embedded Data Model === "denomalized" model

---

```javascript
{
    _id: "1234abcd",
    username: "hangyeolBabo",
    contact: {
        phone: "01012345678",
        email: "hangyeol@likelion.org"
    }
}
```

- embedded 모델을 사용하게 되면 관련 정보들 그 자체를 하나의 document 안에 모조리 저장 할 수 있다. 그 결과로 관련 정보에 대한 작업 시 더 적은 query 를 사용하게 되고, 시스템에 부하가 적다. 따라서 performance 도 좋다고 합니다.

- 그렇다면 어떤 경우에 embedded 모델을 사용하는 것이 나을까?

  - 1 : 1 관계

    ```javascript
    // reference model
    {
        _id: "joe",
        name: "Joe Bookreader"
    }

    {
        patron_id: "joe",
        street: "123 Fake Street",
        city: "Faketon",
        state: "MA",
        zip: "12345"
    }
    ```

    ```javascript
    // embedded model
    {
        _id: "joe",
        name: "Joe Bookreader",
        address: {
                    street: "123 Fake Street",
                    city: "Faketon",
                    state: "MA",
                    zip: "12345"
                }
    }
    ```

  - 1 : N 관계
    - 1:N 의 경우에는 연관 모델을 자주 불러오는 경우 embed 로 설계하는 것이 효과적

  ```javascript
    {
        title: "MongoDB: The Definitive Guide",
        author: [ "Kristina Chodorow", "Mike Dirolf" ],
        published_date: ISODate("2010-09-24"),
        pages: 216,
        language: "English",
        publisher: {
                        name: "O'Reilly Media",
                        founded: 1980,
                        location: "CA"
                    }
    }

    {
        title: "50 Tips and Tricks for MongoDB Developer",
        author: "Kristina Chodorow",
        published_date: ISODate("2011-05-06"),
        pages: 68,
        language: "English",
        publisher: {
                        name: "O'Reilly Media",
                        founded: 1980,
                        location: "CA"
                    }
    }
  ```

<br>

- 쿼리문을 알아보자 : [mongodb/embedded/query](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/#read-operations-embedded-documents)

<br>

## 2. Reference Data Model === "normalized" model

---

```javascript
{
    _id: "1234abcd",
    username: "hangyeolBabo",

}

{
    _id: "1235zzzx",
    user_id: "1234abcd",
    phone: "01012345678",
    email: "hangyeol@likelion.org"
}
```

- reference model 을 사용하게 되면 무엇보다 데이터 같은 데이터가 여러번 저장되는 것을 막을 수 있다.

- 그렇다면 어떤 경우에 reference 모델을 사용하는 것이 나을까?

  - N : N 관계

  - 1 : N 관계의 경우

  ```javascript
    {
        name: "O'Reilly Media",
        founded: 1980,
        location: "CA",
        books: [123456789, 234567890, ...]
    }

    {
        _id: 123456789,
        title: "MongoDB: The Definitive Guide",
        author: [ "Kristina Chodorow", "Mike Dirolf" ],
        published_date: ISODate("2010-09-24"),
        pages: 216,
        language: "English"
    }

    {
        _id: 234567890,
        title: "50 Tips and Tricks for MongoDB Developer",
        author: "Kristina Chodorow",
        published_date: ISODate("2011-05-06"),
        pages: 68,
        language: "English"
    }
  ```

  - 데이터의 중복 저장을 막을 수 있다.

<br><br>

## 3. mongoose 를 사용해서 해보자.

---

```javascript
User.findOne({ name: 'Bob' })
  .populate('posts')
  .exec((err, user) => {
    err ? console.log(err) : console.log(user);
  });
```
