# Google_Test_Mock_Tutorials-
___
## Mocking Project Resources

## Introduction Mocking
* Mocking is a type of test double that usually means replacing a `real` project with a `fake` one.
* It's used to isolate the class under test from its dependencies.
* So you can check that method was called or not with what parameters and so on.
#### For example:
If you are testing a class that does some database operations, you don't want to make `real` changes to the database.
### [Fake](https://github.com/markdown-it/markdown-it-emoji)
 * Working implementation
 * Takes a shortcut
 * Not suitable for production
 * Example: in-momory database
### [Stub](https://github.com/markdown-it/markdown-it-emoji)
 * Is a test doubles that responds with pre-defined data
 * They don't work outside the test
 * An example could be if you are replacing a `real` server with a minimal one that responds with predefined data
### [Mock](https://github.com/markdown-it/markdown-it-emoji)
 * Mock also respond with predefine data, but thay can also have expectations. That means th at you can tell the test that you are expecting a certain method to be called.
 * And exception to be thrown and so on.

## Mocking Method
### [Current way](https://github.com/markdown-it/markdown-it-emoji)
Instead of refefining the methods that you want to override, you just have to use the mock method.
#### Syntax:
```
##### MOCK_METHOD(ReturnType, MethodName, (Arguments...))
int sum(int a, int b);
MOCK_METHOD(int, sum, (int, int));
```



