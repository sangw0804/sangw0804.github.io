---
layout: post
title: 'Mongoose Model 활용하기'
date: 2018-10-05 23:30:37 +0900
categories: web back-end mongoose mongodb tips
---

Mongoose 는 mongodb 를 더욱 편리하게 쓸 수 있도록 도와주는 nodejs 라이브러리이다. <br>
오늘은 Mongoose 의 여러 편리한 기능 중 model hook 에 대해 알아보자. <br>

## Mongoose Model

Mongoose model 은 Mongodb 의 collection 을 하나의 객체로 연결해 db 를 쉽게 다룰 수 있게해준다. 나는 지금까지 model 을 사용할 때, 해당 collection 의 스키마 정도만 정의해서 사용했었다.

```javascript
// models/post.js 파일
...

const postSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true
  },
  date: Date,
  content: String
});

const Post = mongoose.model('Post', postSchema);

module.export = Post;
```

간단한 어플리케이션에선 문제가 없었지만, 위와 같이 모델 파일이 간단하면 디비가 복잡해질 때, 추가되는 db 관리 로직이 라우팅 파일에 들어간다는 뜻이 된다.<br>
예를 들어, post 디비에 comment 라고하는 디비가 1:n 으로 참조된다고 생각해보자.

```javascript
// routes/commentRoutes.js 파일
...

// 새로운 comment 생성 라우트
router.post('/posts/:post_id/comments', async (req, res) => {
  try {
    const post = await Post.findById(req.params.post_id); // 우선 생성되는 comment를 참조할 post doc를 찾는다.
    if (!post) { // 존재여부 유효성 검사
      throw new Error('post not found!');
    }
    const createdComment = await Comment.create(req.body); // comment doc 생성
    post._comments.push(createdComment._id); // post doc의 reference field에 생성된 comment doc 참조값 추가
    await post.save();

    res.status(200).send(createdComment); // 성공
  } catch (e) {
    res.status(400).send(e); // 에러 처리
  }
})
```

단순한 1:n 참조도 위와 같이 코드가 길어지는데, 만약 더 많은 collection 들이 생기고 서로 참조한다고 생각해 보라! 얼마나 많을 로직을 하나의 라우트에서 처리해야 할 지, 생각만으로도 끔찍하다. <br><br>

하지만 우리에겐 Mongoose Model 이 있다.

```javascript
// models/comment.js 파일
...

async function validatePostId(next) { //  this binding이 필요하기에 arrow function은 쓸 수 없다.
  const post = await Post.findById(this._post); // 여기서 this는 저장될 doc객체이다.
  if (!post) {
    return next('post not found!'); // next 콜백함수를 실행해야 model 미들웨어가 다음으로 진행한다. next함수에 인자를 주면 에러가 발생했음을 인지한다.
  }
  next();
}

async function updatePost(doc, next) {
  try {
    const post = Post.findById(doc._post);
    post._comments.push(doc._id);
    await post.save();

    next();
  } catch (e) {
    next(e);
  }
}

commentSchema.pre('save', validatePostId); // pre 는 첫번째 인자로 들어온 행동을 하기 전에 두번째 인자 콜백을 실행하게 만든다.
commentSchema.post('save', updatePost); // post 는 첫번째 인자로 들어온 행동을 한 후에 두번째 인자 콜백을 실행하게 만든다.

const Comment = mongoose.model('Comment', commentSchema);

module.exports = Comment;
```

위 코드는 잠재적으로 수많은 코드와, 에러들과, 유지보수에 드는 노력을 줄일 수 있다. 앞으로 모든 comment model 객체들은 save 를 하기 전/후에 위 콜백들을 실행하며 자동적으로 db 관리를 할 것이다!!
