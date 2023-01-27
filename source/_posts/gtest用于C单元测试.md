---
title: gtest用于C单元测试
tags:
  - C
date: 2023-01-27 11:14:09
---


gtest是google发布的一款跨平台的C++单元测试框架，它提供了丰富的断言、致命和非致命判断。

<!--more-->

在gtest好的测试应该遵循的守则，以及为什么要选择GTEST：

* 测试应当是独立并且可以重现的。调试一个由其他测试结果而导致的成功或者失
败是一件痛苦的事情。GTEST在不同的对象上运行每个测试用例，从而将每个测试
隔离开来，当一个测试失败时，GTEST可以单独调试那一个测试。
* 测试应该是组织良好并且能够反应被测试代码的结构。
* 测试应当是轻便且可重用的。
* 当测试用例失败时，他们应当尽可能多的提供信息。
* GTEST将编写测试的开发人员从杂务中解放出来，使他们能够专心在测试内容上。
* 编写测试应当便捷

## 使用

### 断言

gtest使用一系列断言的宏来检查值是否符合预期，主要分为两类：ASSERT和EXPECT。
区别在于ASSERT不通过的时候会认为是一个fatal的错误，退出当前函数（只是函数）。
而EXPECT失败的话会继续运行当前函数，所以对于函数内几个失败可以同时报告出来。
通常我们用EXPECT级别的断言就好，除非你认为当前检查点失败后函数的后续检查没有意义。

**基础断言**

|Fatal assertion | Nonfatal assertion |	Verifies |
|:------|:------|:-----|
| `ASSERT_TRUE(condition);`| `EXPECT_TRUE(condition);`| condition is true |
| `ASSERT_FALSE(condition);` | `EXPECT_FALSE(condition);`| condition is false |

**数值比较**

| **Fatal assertion** | **Nonfatal assertion**       | **Verifies** |
|:--------------------|:-----------------------------|:-------------|
|`ASSERT_EQ(_val1_, _val2_);`| `EXPECT_EQ(_val1_, _val2_);` | _val1_ `==` _val2_ |
|`ASSERT_NE(_val1_, _val2_);`| `EXPECT_NE(_val1_, _val2_);` | _val1_ `!=` _val2_ |
|`ASSERT_LT(_val1_, _val2_);`| `EXPECT_LT(_val1_, _val2_);` | _val1_ `<` _val2_ |
|`ASSERT_LE(_val1_, _val2_);`| `EXPECT_LE(_val1_, _val2_);` | _val1_ `<=` _val2_ |
|`ASSERT_GT(_val1_, _val2_);`| `EXPECT_GT(_val1_, _val2_);` | _val1_ `>` _val2_ |
|`ASSERT_GE(_val1_, _val2_);`| `EXPECT_GE(_val1_, _val2_);` | _val1_ `>=` _val2_ |

**字符串比较**

| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
| `ASSERT_STREQ(`_str1_`, `_str2_`);`    | `EXPECT_STREQ(`_str1_`, `_str_2`);`     | the two C strings have the same content |
| `ASSERT_STRNE(`_str1_`, `_str2_`);`    | `EXPECT_STRNE(`_str1_`, `_str2_`);`     | the two C strings have different content |
| `ASSERT_STRCASEEQ(`_str1_`, `_str2_`);`| `EXPECT_STRCASEEQ(`_str1_`, `_str2_`);` | the two C strings have the same content, ignoring case |
| `ASSERT_STRCASENE(`_str1_`, `_str2_`);`| `EXPECT_STRCASENE(`_str1_`, `_str2_`);` | the two C strings have different content, ignoring case |

### 简单的测试例子

创建一个简单的测试例子：

1. 使用`TEST()`宏来定义和命名一个测试函数，这些宏就是没有返回值的普通C++函数。
2. 在这个函数中，可以包含任何有效的C++语句，使用各种GTEST断言来检查值。
3. 测试结果由断言决定；如果测试中的任何断言失败，或者测试崩溃，则这个测试用例失败

```C++
TEST(TestSuiteName, TestName) {
  ... test body ...
}
```

当两个或者更多测试需要使用相似的数据的时候，可以使用TestFixtures。这可以对不同测试重用相同的数据对象配置。

当使用TestFixture时，需要使用`TEST_F()`来替代`TEST()`，因为`TEST_F()`允许你在TestFixture中获取对象和子程序。

```C++
TEST_F(TestFixtureName, TestName) {
  ... test body ...
}
```

`TEST()`和`TEST_F()`向googletest隐式注册其测试。定义测试后，可以使用`RUN_ALL_TEST()`来运行它们，如果所有的测试成功，将返回0，否则返回1。

当调用`RUN_ALL_TEST()`宏的时候，程序会发生：

* 保存所有的GTEST标志的状态
* 为第一个测试创建一个test fixture对象
* 通过`SetUp()`初始化这个对象
* 在fixture对象上运行测试
* 通过`TearDown()`函数进行清理
* 删除fixture对象
* 恢复所有的GTEST标志的状态
* 重复上述步骤直到测试结束

也就是说对于每一个TEST_F函数，对应的执行过程如下：

1. 创建test fixture对象（也就是说每一个TEST运行时都有一个运行时创建的test fixture对象）。
2. 使用`SetUp()`初始化这个对象
3. 运行测试集
4. 通过`TearDown()`函数进行清理
5. 删除fixture对象

## gmock

gmock是用来配合google test对C++项目做单元测试的mock框架。可以用来在单元测试中设定对期望行为的定义。对期望行为定义的语法格式如下：

```Cpp
EXPECT_CALL(mock_object, method(matcher1, matcher2, ...))
    .With(multi_argument_matcher)
    .Times(cardinality)
    .InSequence(sequences)
    .After(expectations)
    .WillOnce(action)
    .WillRepeatedly(action)
    .RetiresOnSaturation();
```

例如我们的嵌入式设备中有如下代码：

```Cpp
int GetTime()
{
    // Code that reads oscillator register
    ...
}

int ReadGpio(int line, int *value)
{
    // Code that reads from GPIO line
    ...
}

int WriteGpio(int line, int value)
{
    // Code that writes to GPIO line
    ...
}
```

在Gtest中我们可以mock/重写这些方法如下：

```Cpp
int GetTime()
{
    return GetMock<OscillatorMock>().GetTime();
}

int WriteGpio(int line, int value)
{
    return GetMock<GpioMock>().WriteGpio(line, value);
}

int ReadGpio(int line, int *value)
{
    return GetMock<GpioMock>().ReadGpio(line, value);
}

```

**一个例子**

```C++
#include "gtest/gtest.h"
#include "gmock/gmock.h"

#include "tpm-v2.h"

using namespace ::testing;
using ::testing::Return;

class halTpmInterfaceMock {
public:
    virtual ~halTpmInterfaceMock(){}

    /* mock api */
    MOCK_METHOD2(uclass_first_device_err, int(enum uclass_id id, struct udevice **devp));
    MOCK_METHOD3(tpm_get_desc, int(struct udevice *dev, char *buf, int size));
};

class TpmTestFixture: public ::testing::Test {
public:
    TpmTestFixture() {
        _dmTpmMock.reset(new ::testing::NiceMock<halTpmInterfaceMock>());
    }
    ~TpmTestFixture() {
        _dmTpmMock.reset();
    }

    virtual void SetUp(){}
    virtual void TearDown(){}

    // pointer for accessing mocked library
    static std::unique_ptr<halTpmInterfaceMock> _dmTpmMock;
};

std::unique_ptr<halTpmInterfaceMock> TpmTestFixture::_dmTpmMock;
struct udevice *mockTpmDev;
char dummyTpm[TPM_INFO_SIZE];

// fake lib functions
int uclass_first_device_err(enum uclass_id id, struct udevice **devp)
{
    (void) devp;
    return TpmTestFixture::_dmTpmMock->uclass_first_device_err(id,&mockTpmDev);
}

int tpm_get_desc(struct udevice *dev, char *buf, int size)
{
    return TpmTestFixture::_dmTpmMock->tpm_get_desc(mockTpmDev,dummyTpm,TPM_INFO_SIZE);
}

class tpmPostUnitTest : public TpmTestFixture{
public:
    tpmPostUnitTest() {}
};

TEST_F(tpmPostUnitTest, missDev)
{
    EXPECT_CALL(*_dmTpmMock, uclass_first_device_err(UCLASS_TPM,&mockTpmDev))
                .WillRepeatedly(Return(1));

    EXPECT_EQ(CMD_RET_FAILURE, tpm_post_test(0));
}

TEST_F(tpmPostUnitTest, failGetTpmInfo)
{
    EXPECT_CALL(*_dmTpmMock, uclass_first_device_err(UCLASS_TPM,&mockTpmDev))
                .WillRepeatedly(Return(0));

    EXPECT_CALL(*_dmTpmMock, tpm_get_desc(mockTpmDev, dummyTpm, TPM_INFO_SIZE))
                .WillRepeatedly(Return(-1));

    EXPECT_EQ(CMD_RET_FAILURE, tpm_post_test(0));
}

TEST_F(tpmPostUnitTest, testPass)
{
    EXPECT_CALL(*_dmTpmMock, uclass_first_device_err(UCLASS_TPM,&mockTpmDev))
                .WillRepeatedly(Return(0));

    EXPECT_CALL(*_dmTpmMock, tpm_get_desc(mockTpmDev, dummyTpm, TPM_INFO_SIZE))
                .WillRepeatedly(Return(0));

    EXPECT_EQ(CMD_RET_SUCCESS, tpm_post_test(0));
}
```

## ref

1. [using GoogleTest and GoogleMock frameworks for embedded C](https://www.codeproject.com/Articles/1040972/Using-GoogleTest-and-GoogleMock-frameworks-for-emb)
2. [Googletest Primer](https://github.com/google/googletest/blob/b3bfebd796a5875360ee1c90237931e8e0bae9f5/docs/primer.md)
3. [google gtest快速入门](https://github.com/AngryHacker/articles/blob/master/src/open_source_components/google_gtest.md)
4. [gtest Quickstart: Building with CMake](https://google.github.io/googletest/quickstart-cmake.html)
