# Introduction to C++11

Note from https://www.thegeekstuff.com/2016/02/c-plus-plus-11/

## Range based loop 

```cpp
for(int& i: someDataStructure) { doSomething();}
```

- using the reference
- no `&` = access by value

## Strongly Typed enums

```cpp
enum class CoordinateArea { FirstArea, SecondArea, ThirdArea, FourthArea};

CoordinateArea caOne = CoordinateArea::FirstArea;

CoordinateArea caSome= CoordinateArea::FourhtArea;
```

## Lamba Expressions of Lambda Functions

```cpp
[firstPart](secondPart) TypeYouReturn{ BodyOfLambda}(acctualParameters);
```

- firstPart: variable scope which will be exploited inside the lambda function
    - `[]` it means that you will not provide anything to give to lambda.
    - `[&]` it is used to say that you have some references to mess with.
    - `[=]` it is used to make the copy.
    - `[this]` is used to the enclosing class.
- secondPart is necessary for parameter list for nameless function but it could be left empty as well.
- TypeYouReturn : register what type will be returned from your lambda.
- BodyOfLambda : actions you wish to do, in here you will type some code that will be used to perform actions you are intended to apply in the body of this function.
- actualParameters : provide input into lambda function.

Example: 
```cpp
double dUpperPart = [](double dX, double dY)double{ return dX*dX +dY*dY;} 

vectror<int> iVector;
for_each( begin(iVector), end(iVector), [](int n){if(n%2==0)cout<<n<<end;});
```

## Move and `&&`

- lvalue: l stands for left side. It is one of the basic properties lvalue has it is an object **in memory** that could be at the left side of expression; an example would be some variable.
- rvalue: r stands for right side. This will be located on the right side of expression and it should not be found on right side, the specimen for it is a constant.

We can use rvalue as reference as well.

Move constructor: 
```cpp
MovableClass(MovableClass&&);

MovableClass&& operator=(MovableClass&&); 
```

## Pointers

`unique_ptr`: If something has ownership it could not be shared, but it is movable. This means that you could transfer it to another unique pointer.

```cpp
unique_ptr<someType> suniquePtr(new someType(args));
...
uniquePtr.release();
```

`shared_ptr`: require ownership of some resource in the memory to be shared

```cpp
shared_ptr<someType> somePtr(new someType(args));
```

`weak_ptr`: access to something that might exist in memory. Granted if you have object that occupy the memory, and it is possible to delete that object, the necessary destructor is called upon if it has been used last time.

```cpp
weak_ptr<someType> weakPtr= somePtr;
```



