# Unit Test Struture
## Unit Test Struture (Code Example)
### [CMakeLists.txt](https://github.com/markdown-it/markdown-it-emoji)
```txt
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
### [LibraryCode.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include "LibraryCode.hpp"

#include <algorithm>

bool isPositive(int x)
{
 return x >= 0;
}

int countPositives(std::vector<int> const& inputVector)
{
    return std::count_if(inputVector.begin(), inputVector.end(), isPositive);
}
```
### [LibraryCode.hpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#pragma once
#include <vector>

int countPositives(std::vector<int> const& inputVector);
```
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"

TEST(TestCountPositives, BasicTest)
{
    //Arrange
    std::vector<int> inputVector{1, -2, 3, -4, 5, -6, -7};

    //Act
    int count = countPositives(inputVector);

    //Assert
    ASSERT_EQ(3, count);
}
TEST(TestCountPositives, EmptyVectorTest)
{
    //Arrange
    std::vector<int> inputVector{};

    //Act
    int count = countPositives(inputVector);

    //Assert
    ASSERT_EQ(0, count);
}
TEST(TestCountPositives, AllNegativesTest)
{

    //Arrange
    std::vector<int> inputVector{-1, -2, -3};

    //Act
    int count = countPositives(inputVector);

    //Assert
    ASSERT_EQ(0, count);
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
## Assertions
| Fatal | Non-Fatal | What it tests |
| ----- | --------- | ------------- |
| ASSERT_TRUE(condition); | EXPECT_TRUE(condition); | condition is true |
| ASSERT_FALSE(condition);  | EXPECT_FALSE(condition); |condition is not true |


| Fatal | Non-Fatal | What it tests |
| ----- | --------- | ------------- |
| ASSERT_EQ(x, y); | EXPECT_EQ(x, y); | x == y | 
| ASSERT_NE(x, y); | EXPECT_NE(x, y); | x != y | 
| ASSERT_LT(x, y); | EXPECT_LT(x, y); | x < y | 
| ASSERT_LE(x, y); | EXPECT_LE(x, y); | x <= y | 
| ASSERT_GT(x, y); | EXPECT_GT(x, y); | x > y | 
| ASSERT_GE(x, y); | EXPECT_GE(x, y); | x >= y | 
## Assertions on String - The Wrong Way (Code Example)
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"

TEST(ToUpperTest, BasicTest)
{
 //Arrange
 char inputString[] = "Hello World";

 //Act
 toUpper(inputString);

 std::cout << "After toUpper: " << inputString << '\n';
 if(inputString == "HELLO WORLD") // pointer
 {
     std::cout << "Equal strings\n";
 }
 else
 {
     std::cout << "Unequal strings\n";
 }

 if(0 == strcmp(inputString,"HELLO WORLD")) // content
 {
     std::cout << "Equal strings\n";
 }
 else
 {
     std::cout << "Unequal strings\n";
 }
 //ASSERT
 ASSERT_EQ("HELLO WORLD", inputString);
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```
## Assertions on String - The correct Way (Code Example)
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include <iostream>
#include <string>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"

TEST(ToUpperTest, BasicTest)
{
 //Arrange
 char inputString[] = "Hello World";

 //Act
 toUpper(inputString);

 //ASSERT
 ASSERT_STREQ("HELLO WORLD", inputString);
 //ASSERT_STRCASEEQ("Hello WORLD", inputString);

 // or the other example:
 //  std::string str(inputString);
 //  ASSERT_EQ("HELLO WORLD", str);
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```

## Assertions on Exception (Code Example)
### [LibraryCode.hpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#pragma once

double mySqrt(double input);
```
### [LibraryCode.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include "LibraryCode.hpp"
#include <cmath>
#include <stdexcept>
#include <iostream>

double mySqrt(double input)
{
    if(input < 0)
    {
        std::cout << "Exception thrown\n";
        throw std::runtime_error("Negative argument");
    }
    std::cout << "No exception thrown\n";
    return sqrt(input);
}
```
### [testRunner.cpp](https://github.com/markdown-it/markdown-it-emoji)
```c++
#include <iostream>
#include <gtest/gtest.h>
#include "LibraryCode.hpp"

TEST(SquareRootTest, NegativeArgumentTest)
{
    double inputValue = -9;

    //ASSERT_ANY_THROW(mySqrt(inputValue));
    ASSERT_THROW(mySqrt(inputValue), std::runtime_error);
}


TEST(SquareRootTest, PositiveArgumentTest)
{
    double inputValue = 9;

    ASSERT_NO_THROW(mySqrt(inputValue));
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```


