---
layout:       post
title:        "google test"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - gtest
    - unit test
---

### build 
1. build with sample `cmake -Dgtest_build_samples=ON ..`

### NOTE 
1. add custom message `ASSERT_EQ(x.size(), y.size()) << "Vectors x and y are of unequal length";`

### Test Fixtures
1. SetUp() and TearDown() 
2. TEST_F() 
3. GoogleTest does not reuse the same test fixture for multiple tests. Any changes one test makes to the fixture do not affect other tests.
4. demo
    ```
    // The fixture for testing class Foo.
    class FooTest : public testing::Test {
     protected:
      // You can remove any or all of the following functions if their bodies   would
      // be empty.

      FooTest() {
         // You can do set-up work for each test here.
      }

      ~FooTest() override {
         // You can do clean-up work that doesn't throw exceptions here.
      }

      // If the constructor and destructor are not enough for setting up
      // and cleaning up each test, you can define the following methods:

      void SetUp() override {
         // Code here will be called immediately after the constructor (right
         // before each test).
      }

      void TearDown() override {
         // Code here will be called immediately after each test (right
         // before the destructor).
      }

      // Class members declared here can be used by all tests in the test   suite
      // for Foo.
    };
    ```



### RUN_ALL_TESTS()
```
GTEST_API_ int main(int argc, char **argv) {
  printf("Running main() from %s\n", __FILE__);
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

* Most users should not need to write their own main function and instead link with gtest_main



### Death Tests 
Note that if a piece of code throws an exception, we don’t consider it “death” for the purpose of death tests, as the caller of the code could catch the exception and avoid the crash

> EXPECT_DEATH and ASSERT_DEATH will fork the current process, test after EXPECT_DEATH can still be run


