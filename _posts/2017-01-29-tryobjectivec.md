---
layout: post
title: Objective-C 学习笔记
author: 阎曦
category: 技术
tags: [iOS]
---

趁过年在家有点时间，从除夕到今天终于把 [Code School](https://www.codeschool.com/) 里的 [Try Objective-C 课程](http://tryobjectivec.codeschool.com/)学习了一遍。虽然之前看过 OC 语法，但一直觉得比较反人类，这次再学习一遍，收获还是不小.前三个级别对我来说比较容易，后面两个级别还是有些难度的。在这里把其中的主要内容做一下笔记，以备下次可以快速复习。

### Level 1:  NSLog, NSString, NSNumber, NSArray, NSDictionary

```objective_c
NSString *name = @"yanxi";
NSNumber *age = @33;
NSLog(name);
NSLog(@"%@ is %@ years old", firstName, age);

NSArray *apps = @[@"AngryFowl", @"Lettertouch", @"Tweetrobot"];
NSLog(@"%@", apps[1]);

apps[3] = @"Instacanvas";     // 出错了，NSArray is an immutable object.

// 可以重新赋值
apps = @[@"AngryFowl", @"Lettertouch", @"Tweetrobot", @"Instacanvas"];

NSDictionary *appRatings = @{@"AngryFowl": @3, @"Lettertouch": @5};
NSLog(@"%@", appRatings[@"AngryFowl"]);
```

<!-- more -->

### Lever 2: message, NSUInteger, alloc/init, stringWithFormat

```objective_c
NSArray *foods = @[@"tacos", @"burgers"];
NSLog(@"%@", [foods description]);   // Sending a message: [objectName messageName];

NSString *city = @"Ice World";
NSUInteger cityLength = [city length];     // 不含 * 的变量申明
NSLog(@"City has %lu characters", cityLength);     // 如果用 %@ 会出错，必须用 %lu

NSNumber *higgiesAge = @6;
NSNumber *phoneLives = @3;
NSNumber *product = higgiesAge * phoneLives;     // 出错了

NSUInteger higgiesAgeInt = [higgiesAge unsignedIntegerValue];
NSUInteger phoneLivesInt = [phoneLives unsignedIntegerValue];
NSUInteger higgiesRealAge = higgiesAgeInt * phoneLivesInt;

NSString *newString = @"Hello" + @"World";     // 出错了
NSString *fullName = [firstName stringByAppendingString:lastName];
// 嵌套使用
NSString *fullName = [[firstName stringByAppendingString:@" "] stringByAppendingString:lastName];

// 多参数，后面的参数有名字
NSString *replaced = [fullName stringByReplacingOccurrencesOfString:firstName
                                                         withString:lastName];
NSString *copy = [NSString stringWithString:firstName];    // 通过 message 创建 NSString

NSArray *emptyArray = [NSArray array];
NSDictionary *emptyDict = [NSDictionary dictionary];

// alloc/init
NSString *emptyString = [[NSString alloc] init];
NSArray *emptyArray = [[NSArray alloc] init];
NSDictionary *emptyDictionary = [[NSDictionary alloc] init];
NSString *copy = [[NSString alloc] initWithString:otherString];

NSString *fullName = [NSString stringWithFormat:@"%@ %@", firstName, lastName];
```

### Level 3: Bool, if/else, switch/case/break, for, block

```objective_c
Bool mrHiggieIsMean = YES;

// for … in 用法，可用于 NSArray，NSDictionary

NSArray *funnyWords = @[@"Schadenfreude", @"Portmanteau", @"Penultimate"];
for (NSString *word in funnyWords) {
  NSLog(@"%@ is a funny word", word);
}

NSDictionary *funnyWords = @{
  @"Schadenfreude": @"pleasure derived by someone from another person's misfortune.",
  @"Portmanteau": @"consisting of or combining two or more separable aspects or qualities",
  @"Penultimate": @"second to the last"
};
for (NSString *word in funnyWords){
  NSString *definition = funnyWords[word];
  NSLog(@"%@ is defined as %@", word, definition);
}

// code blocks
{
  NSLog(@"Hello from inside the block");
}

// 类似于函数（message），可以接受参数，也有返回值
void (^logMessage)(void) = ^{
  NSLog(@"Hello from inside the block");
};

void (^sumNumbers)(NSUInteger, NSUInteger) = ^(NSUInteger num1, NSUInteger num2){
  NSLog(@"The sum of the numbers is %lu", num1 + num2);
};
void (^myFirstBlock)(NSString *) = ^(NSString *str){
  NSLog(str);
};

[newHats enumerateObjectsUsingBlock:
  ^(NSString *hat, NSUInteger index, BOOL *stop){
    NSLog(@"Trying on hat #%lu: %@", index+1, hat);
  }
];
```

### Level 4: class, inheritance, property, interface, implementation

Phone.h 创建对象 Phone，继承自 [NSObject](https://developer.apple.com/reference/objectivec/nsobject)

```objective_c
@interface Phone : NSObject {
  NSNumber *_batteryLife2;     // instance variable，外部不可见
}
    @property NSString *phoneName;                            // 属性
    @property (readonly) NSNumber *batteryLife;          // 只读属性

    -(void) speak;
    -(NSString *)speak2;          // 定义 method/message
    -(NSString *)speak3:(NSString *)greeting;

    -(void) decreaseBatteryLife:(NSNumber *)arg;
    -(void) reportBatteryLife;
@end
```

Phone.m

```objective_c
#import "Phone.h"

@implementation Phone
- (Phone *)init;                    // 构造函数，用于初始化
{
  _batteryLife = @100;
  _batteryLife2 = @100;
  return [super init];
}

-(void)speak;
{
    NSLog(@"%@ says Hello There!", self);
}
-(NSString *)speak2;
{
    NSString *message = [NSString stringWithFormat:@"%@ says Hello There!", self.phoneName];
    return self.phoneName;
}
-(NSString *)speak3:(NSString *)greeting;
{
    NSString *message = [NSString stringWithFormat:@"%@ says %@", self.phoneName, greeting];
    return message;
}
- (void) decreaseBatteryLife:(NSNumber *)arg;
{
    _batteryLife = @([self.batteryLife intValue] - [arg intValue]);
    _batteryLife2 = @([_batteryLife2 intValue] - [arg intValue]);
}
@end
```

调用 Phone.h

```objective_c
#import "Phone.h"

Phone *talkingiPhone = [[Phone alloc] init];
talkingiPhone.phoneName = @"Mr. Higgie";

[talkingPhone speck];

NSString *phoneMessage = [talkingiPhone speak2];
NSLog(@"%@", phoneMessage);

NSLog(@"%@", [talkingiPhone speak:@"Hello"]);
```

### level 5: exception, protocol, nil，initXXX, object class, Pointer

判断 copyWithZone: 是否存在

```objective_c
if([talkingiPhone respondsToSelector:@selector(copyWithZone)]){
    Phone *copy = [talkingiPhone copy];
    [copy reportBatteryLife];
}
```

Person.h NSCopying protocol 类似于 java 里的接口

```objective_c
@interface Person : NSObject <NSCopying>
@end
```

Person.m

```objective_c
@implementation Person
- (Person *) copyWithZone:(NSZone *)zone;
{
    Person *personCopy = [[Person allocWithZone:zone] init];
    return personCopy;
}
@end
```

nil 表示空，有一个特殊特性，你可以发送 message 给 nil 而不会产生错误，而是返回 nil。

可以自定义初始化函数，名字必须以 `init` 开头。下面是示例: 

Person.h

```objective_c
@interface Person : NSObject
- (Person *) initWithFirstName:(NSString *)firstName
                      lastName:(NSString *)lastName;

@property NSString *firstName;
@property NSString *lastName;
@end
```

Person.m

```objective_c
@implementation Person
- (Person *) initWithFirstName:(NSString *)firstName
                      lastName:(NSString *)lastName;
{
   _firstName = firstName;
   _lastName = lastName;
   return [super init];
}
@end
```

[开闭原则](https://zh.wikipedia.org/wiki/%E5%BC%80%E9%97%AD%E5%8E%9F%E5%88%99)：软件中的对象（类，模块，函数等等）应该对于扩展是开放的，但是对于修改是封闭的

object class

```objective_c
// 当 Phone 子类调用 copyWithZone 时，不应该返回 Phone 对象，这时候需要用到 Object class
// 见下面 [self class] 用法
- (Phone *) copyWithZone:(NSZone *)zone;
{
   Phone *copy = [[[self class] allocWithZone:zone] initWithBatteryLife:_batteryLife];
   copy.phoneName = [NSString stringWithFormat:@"Copy of %@", self.phoneName];
   return copy;
}
```

id 类型

```objective_c
id person = [[Person alloc] init];     // 不需要 *

id person = [[Person alloc] init];
[person setFirstName:@"Eric"];     // id 类型不能用 . 符号
NSLog(@"%@", [person firstName]);
```

*表示指针，NSString * 表示指向 NSString object 的指针，或者是一个 NSString object 的引用。
id 类型永远是一个对象的指针，所以不需要加 *
基本 c 类型变量，比如 int， char 也不需要 *，因为它是直接存值的。如果想 Log 对象的地址，而不是对应的值，将 %@ 修改成 %p 接口。
