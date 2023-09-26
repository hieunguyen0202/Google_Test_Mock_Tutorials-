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

