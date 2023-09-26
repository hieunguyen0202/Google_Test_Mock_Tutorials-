# Fixture_Tutorials-
## Test Fixtures
### [CMakeLists.txt](https://github.com/markdown-it/markdown-it-emoji)
```
cmake_minimum_required(VERSION 3.13)
set(CMAKE_CXX_STANDARD 11)

find_package(GTest REQUIRED)
message("GTest_INCLUDE_DIRS = ${GTest_INCLUDE_DIRS}")

add_library(commonLibrary LibraryCode.cpp)

add_executable(mainApp main.cpp)
target_link_libraries(mainApp commonLibrary)

add_executable(unitTestRunner testRunner.cpp)
target_link_libraries(unitTestRunner commonLibrary ${GTEST_LIBRARIES} pthread)


```
### [LibraryCode.hpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#pragma once

class Account
{
 public:
   Account();
   void deposit(double sum);
   void withdraw(double sum);
   double getBalance() const;
   void transfer(Account &to, double sum);
 private:
   double mBalance;
};

```
### [LibraryCode.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include "LibraryCode.hpp"
#include <stdexcept>

Account::Account() : mBalance{0}
{
}

void Account::deposit(double sum)
{
    mBalance += sum;
}

void Account::withdraw(double sum)
{
    if(mBalance < sum)
    {
        throw std::runtime_error("Insufficient funds");
    }
    mBalance -= sum;
}

double Account::getBalance() const { return mBalance; }

void Account::transfer(Account &to, double sum)
{
    withdraw(sum); //will throw an error if not enough
    to.deposit(sum);
}
```
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include <iostream>
#include <gtest/gtest.h>
#include <stdexcept>
#include "LibraryCode.hpp"


TEST(AccountTest, TestEmptyAccount)
{
  Account account;

  double balance = account.getBalance();

  ASSERT_EQ(0, balance);
}

class AccountTestFixture: public testing::Test
{
  public:
   AccountTestFixture();
   virtual ~AccountTestFixture();
   void SetUp() override;
   void TearDown() override;
   static void SetUpTestCase();
   static void TearDownTestCase();
  protected:
   Account account;
};

AccountTestFixture::AccountTestFixture()
{
  std::cout << "Constructor called\n";
}

AccountTestFixture::~AccountTestFixture()
{
  std::cout << "Destructor called\n";
}

void AccountTestFixture::SetUpTestCase()
{
  std::cout << "SetUpTestCase called\n";
}

void AccountTestFixture::TearDownTestCase()
{
  std::cout << "TearDownTestCase called\n";
}

void AccountTestFixture::SetUp()
{
    std::cout << "SetUp called\n";
    account.deposit(10.5);
}

void AccountTestFixture::TearDown()
{
    std::cout << "TearDown called\n";
}


TEST_F(AccountTestFixture, TestDeposit)
{ 
  std::cout << "Test body\n";
  ASSERT_EQ(10.5, account.getBalance());
}


TEST_F(AccountTestFixture,  TestWithdrawOK)
{
  account.withdraw(3);

  ASSERT_EQ(7.5, account.getBalance());
}


TEST_F(AccountTestFixture,  TestWithdrawInsufficientFunds)
{
  ASSERT_THROW(account.withdraw(300), std::runtime_error);
}


TEST_F(AccountTestFixture,  TestTransferOK)
{
  Account to;

  account.transfer(to, 2);
  
  ASSERT_EQ(8.5, account.getBalance());
  ASSERT_EQ(2, to.getBalance());
}


TEST_F(AccountTestFixture,  TestTransferInsufficientFunds)
{
  Account to;

  ASSERT_THROW(account.transfer(to, 200), std::runtime_error);
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```
## Fixture Flow
* First, the fixture is created so the constructor gets called
* Next, `SetUp` is called and then the actual test body is called
* After that, `TearDown` is called and finally the fixture gets destroyed
* So, the destructor gets called

### Why do we need to use constructor and destructor ?
* const
* Because you may forget to explicity call `SetUp` and `TearDown`
### Wrong write Setup instead of SetUp
If you spell it incorrectly, the actual method is not overridden and it is not called autimatically
To avoid such a mistake, use the `override` keyword available with c++ 11 and onward 

### Why do we need to use `SetUp` and `TearDown` ?
* When you must call virtual methods
* If you call a virtual method in the contructor, the call is not polymorphysim, so the base method will always get called.
* Do not use fatal assertions (ASSERT_*) in contructors - instead of be used in `SetUp`
* Do not call code that can throw exceptions in the destructor, instead of be used in `TearDown`
* If an exception is thrown in the destructor, this can result in undefined behaviour or even crashes

## Parameterized Tests

### [LibraryCode.hpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#pragma once

class Account
{
 public:
   Account();
   void deposit(double sum);
   void withdraw(double sum);
   double getBalance() const;
   void transfer(Account &to, double sum);
 private:
   double mBalance;
};

```
### [LibraryCode.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#pragma once
class Validator
{
public:
    Validator(int low, int high);
    bool inRange(int valueToTest) const;
private:
    int mLow, mHigh;
};
```
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)

#### Basic in Range
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"
// Validator(5, 10)
// 4, 5, 6, 7, 9, 10, 11 

class ValidatorFixture : public testing::TestWithParam<int>
{
public:
protected:
 Validator mValidator{5, 10};
};

TEST_P(ValidatorFixture, TestInRange)
{
  std::tuple<int, bool> tuple = GetParam();

  int param = GetParam();
  bool isInside = mValidator.inRange(param);

  ASSERT_EQ(isInside);
}

INSTANTIATE_TEST_CASE_P(InRangeTrue, ValidatorFixture, testing::Values(5,6,7,9,10));

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```
#### Basic not in Range
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"
// Validator(5, 10)
// 4, 5, 6, 7, 9, 10, 11 

class ValidatorFixture : public testing::TestWithParam<int>
{
public:
protected:
 Validator mValidator{5, 10};
};

TEST_P(ValidatorFixture, TestNotInRange)
{
  std::tuple<int, bool> tuple = GetParam();

  int param = GetParam();
  std::cout << "Param (not in range = " << param << std::endl;
  bool isInside = mValidator.inRange(param);

  ASSERT_EQ(isInside);
}

INSTANTIATE_TEST_CASE_P(InRangeFalse, ValidatorNegativeFixture, testing::Values(-11,40,4,11));

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```


#### Advanced
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"
// Validator(5, 10)
// 4, 5, 6, 7, 9, 10, 11 

class ValidatorFixture : public testing::TestWithParam<std::tuple<int, bool>>
{
public:
protected:
 Validator mValidator{5, 10};
};

TEST_P(ValidatorFixture, TestInRange)
{
  std::tuple<int, bool> tuple = GetParam();

  int param = std::get<0>(tuple);
  bool expectedValue = std::get<1>(tuple);

  std::cout << "param = " << param << " expected value = " << expectedValue << '\n';

  bool isInside = mValidator.inRange(param);

  ASSERT_EQ(expectedValue, isInside);
}

INSTANTIATE_TEST_CASE_P(InRangeTrue, ValidatorFixture, testing::Values(
                                                          std::make_tuple(-50, false),
                                                          std::make_tuple(4, false),
                                                          std::make_tuple(5, true),
                                                          std::make_tuple(6, true),
                                                          std::make_tuple(7, true),
                                                          std::make_tuple(9, true),
                                                          std::make_tuple(10, true),
                                                          std::make_tuple(11, false),
                                                          std::make_tuple(100, false)
));

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```
