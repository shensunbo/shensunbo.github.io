---
layout:       post
title:        "google mock"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - gtest
    - unit test
    - gmock
---

1. 什么样的类是可以使用gtest测试的

2. 编写类时如何注意适配gmock

3. 什么样的类无法使用gtest测试
   1. 不使用依赖注入的方式引入的依赖项无法被mock
   2. 类内直接使用依赖项的实例作为成员时，gmock不能生效，必须与EXPECT_CALL中的mock对象指向的是同一个对象，EXPECT_CALL才会被满足
4. When a mock is destructed, gMock will automatically check whether all expectations on it have been satisfied.
5. By creating an object of type InSequence, all expectations in its scope are put into a sequence and have to occur sequentially. Since we are just relying on the constructor and destructor of this object to do the actual work, its name is really irrelevant.
   ```
   using ::testing::InSequence;
    ...
    TEST(FooTest, DrawsLineSegment) {
      ...
      {
        InSequence seq;

        EXPECT_CALL(turtle, PenDown());
        EXPECT_CALL(turtle, Forward(100));
        EXPECT_CALL(turtle, PenUp());
      }
      Foo();
    }
   ```

6. You must always put a mock method definition (MOCK_METHOD) in a public: section of the mock class, regardless of the method being mocked being public, protected, or private in the base class. 
7. if you don’t mock all versions of the overloaded method, the compiler will give you a warning about some methods in the base class being hidden. To fix that, use using to bring them in scope:`using Foo::Add;`