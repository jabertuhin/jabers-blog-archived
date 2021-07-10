---
layout: post
title: স্ট্র্যাটিজি প্যাটার্ন (Strategy Pattern)
description: Strategy Design Pattern
toc: true
categories: [Design Pattern, Python, Bangla]
image: images/strategy-pattern.jpg
---
ডিজাইন প্যাটার্নের গুরুত্ব আমরা সকলেই কমবেশি জানি। সফটওয়্যার ডেভলপমেন্টের ক্ষেত্রে ডিজাইন প্যাটার্নগুলো জানা থাকলে আমাদের বিভিন্ন প্রবলেম সঠিক ডিজাইন মেইনটেইন করে সলভ করা সহজ হয়ে যাবে। এই ব্লগ পোস্টে আমরা এরই লক্ষ্যে "স্ট্র্যাটেজি প্যাটার্ন" সম্পর্কে জানতে চেষ্টা করবো। 

প্রথমে naive এপ্রোচে একটা সলিউশন দাঁড় করানোর চেষ্টা করবো। পরবর্তীতে স্ট্র্যাটেজি প্যাটার্নকে ব্যবহার করে কিভাবে  আমরা সেটাকে আরও ভালোভাবে ডিজাইন করতে পারি সেটা দেখবো।

## প্রবলেম

আমরা ছোট একটা ভিডিও গেমের কথা চিন্তা করি - ক্রিকেটের একটা গেম হবে, যেখানে বিভিন্ন দেশের বিভিন্ন খেলোয়াড় থাকবে। এখন এই খেলোয়াড়দের কথা মাথায় রেখে আসুন আমরা কিছু ক্লাস ডিজাইন করি।

## সমাধান

### :arrow_right: সাধারণ পন্থায়

প্রথমে OOP এর কনসেপ্ট ধরে যদি চিন্তা করি - আমরা Player নামের একটা ইন্টার্ফেস তৈরি করতে পারি। যার `bat()` এবং `bowl()` নামের দুইটা মেথডকে ইমপ্লিমেন্ট করতে হবে। Player ক্লাসটা সেক্ষেত্রে নিম্নরূপ হবে :point_down:

(:hash: এখানে একটু বলে রাখা ভালো, পাইথনের ইন্টার্ফেস তৈরি পদ্ধতি অন্য স্ট্যাটিক টাইপড ল্যাঙ্গুয়েজ থেকে বেশ আলাদা। )

```python
from abc import ABC, abstractmethod


class Player(ABC):
    def __init__(self, name: str, country: str):
        self.name = name
        self.country = country    

    @abstractmethod
    def bat(self) -> None:
        raise NotImplementedError

    @abstractmethod
    def bowl(self) -> None:
        raise NotImplementedError

    def __str__(self) -> str:
        return f"My name is {self.name}. I'm from {self.country}."
```
এখন এই Player ইন্টার্ফেসকে ব্যবহার করে আমি আমাদের ওপেনার তামিম ইকবাল খান-এর একটা ক্লাস যদি তৈরি করতে চাই। সেই ক্লাসটি হবে নিম্নরূপ :point_down:

```python
class Tamim(Player):    
    def __init__(self, name: str, country: str):
        super().__init__(name, country)        

    def bat(self) -> None:
        """Implementation of Tamim's batting."""

    def bowl(self) -> None:
        """Implementation of Tamim's bowling."""
```
একজন ওপেনিং ব্যাটসম্যানের পরে এখন আমরা একজন বোলার, মুস্তাফিজুর রহমানের ক্লাস অনুরূপভাবে তৈরি করতে পারি।

```python
class Mustafizur(Player):    
    def __init__(self, name: str, country: str):
        super().__init__(name, country)        

    def bat(self) -> None:
        """Implementation for Mustafizur's batting."""

    def bowl(self) -> None:
        """Implementation for Mustafizur's bowling."""
```
এইক্ষেত্রে আমরা যদি তামিম এবং মুস্তাফিজুরের অব্জেক্ট তৈরি করতে চাই, তা খুব সহজেই আমরা করতে পারবো নিম্নোক্তভাবে :point_down:

```python
Tamim = Tamim(name="Tamim Iqbal", country="Bangladesh")
Mustafizur = Mustafizur(name="Mustafizur Rahman", country="Bangladesh")
```
আপাত দৃষ্টিতে সব ঠিক মনে হচ্ছে। তামিমের জন্য আলাদা একটা ক্লাস ডিজাইন করে নিয়েছি, মুস্তাফিজের জন্যও আলাদা ক্লাস করে নিয়েছি। 

এখন আমরা যদি বাংলাদেশ জাতীয় দলের অন্যান্য খেলোয়াড়দের  ক্লাস তৈরি করতে চাই? এরপর ভারতের খেলোয়াড়দের?

প্রত্যেক খেলোয়াড়ের জন্যই তাহলে আমাকে আলাদা করে একটা ক্লাস তৈরি করতে হবে এবং সেই ক্লাসে `bat()` ও `bowl()` দুইটা মেথডকে প্রতিবার ইমপ্লিমেন্টও করতে হবে। এখন যদি একটু খেয়াল করি- তামিম, সাকিব, সৌম্য এরা প্রত্যেকেই বামহাতি ব্যাটসম্যান। সুতরাং, প্রত্যেক বামহাতি প্লেয়ারের জন্যও আমি বারবার `bat()` মেথড একইভাবে ইমপ্লিমেন্ট করছি। এবার মুস্তাফিজ বামহাতি ব্যাটসম্যান হলেও তাকে আমরা ঠিক ব্যাটসম্যান ক্যাটাগরিতে ফেলবো না। তার ক্ষেত্রে ব্যাটিং করার ইমপ্লিমেন্টেশন আলাদা হওয়া উচিত। 

আমাদের বর্তমান ডিজাইন অনুযায়ী ব্যাটিং, বোলিং এর ইমপ্লিমেন্টেশন একেকটা ক্লাসের অর্থাৎ একেকজন খেলোয়াড়ের সাথে কাপল্ড(coupled) হয়ে যাচ্ছে, যাকে পরবর্তীতে আমরা পুনর্বার ব্যবহারও করতে পারছি না।

### :arrow_right: Strategy Pattern ব্যবহারের মাধ্যমে

সামনে যাওয়ার পূর্বে আমাদেরকে দুটো কন্সেপ্টের সাথে পরিচিত থাকতে হবে। কনসেপ্টগুলো হলো -

:one: [Composition over Inheritence](https://betterprogramming.pub/prefer-composition-over-inheritance-1602d5149ea1)

:two: [Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection)

এগুলোর সাহায্য নিয়ে আমরা  **Strategy Pattern**  প্রয়োগ করে উপরের সমস্যাগুলো থেকে মুক্তি পেতে পারি।

আমরা এখন `IBattingStyle`  ও `IBowlingStyle` এ দু'টো নতুন ইন্টার্ফেস নিয়ে আসছি। এই ইন্টার্ফেসগুলো ব্যবহার করে আমরা আমাদের পুর্বের Player ক্লাসকে নতুন করে ডিফাইন করবো।

```python
from abc import ABC, abstractmethod

class IBattingStyle(ABC):
    @abstractmethod
    def bat(self) -> None:
        raise NotImplementedError

class IBowlingStyle(ABC):
    @abstractmethod
    def bowl(self) -> None:
        raise NotImplementedError
```
এখন আমরা আমাদের নতুন Player ক্লাসটা যদি লক্ষ্য করি :point_down:
```python
class Player:
    def __init__(self, name: str, 
                       country: str,
                       batting_style: IBattingStyle,
                       bowling_style: IBowlingStyle):
        self.name = name
        self.country = country
        self.batting_style = batting_style
        self.bowling_style = bowling_style

    def bat(self) -> None:
        self.batting_style.bat()

    def bowl(self) -> None:
        self.bowling_style.bowl()

    def __str__(self) -> str:
        return f"My name is {self.name}. I'm from {self.country}."
```
Player ক্লাসটার কাজ বদলায় নি, কিন্তু চেহারা বদলে গেছে। আমাদের ক্লাস এখন  `__init__` এ দু'টোর বদলে মোট ৪ টা আর্গুমেন্ট এক্সপেক্ট করছে। এখানে আমরা প্রতিটা খেলোয়াড়ের ব্যাটিং স্টাইল, বোলিং স্টাইল সেই খেলোয়াড়ের অব্জেক্ট তৈরি করার সময় দিয়ে দিতে পারবো। আর Player ক্লাসের  `bat()` ও `bowl()` মেথড যাদের পুর্বে ইমপ্লিমেন্ট করতে হতো, এখন সেই মেথডগুলো শুধু `batting_style` ও `bowling_style` অবজেক্টের ইমপ্লিমেন্টেড মেথডকে কল করবে । এখানে Player ক্লাসকে কোন নির্দিষ্ট ব্যাটিং, বোলিং স্টাইলের ইমপ্লিমেন্টেশনের বিস্তারিত জানতে হচ্ছে না। শুধু  নির্দিষ্ট একটা কাজ করার জন্য ইন্টার্ফেসের কোন মেথডকে কল করতে হবে, সেটা জানলেই হচ্ছে। 

`IBattingStyle`, `IBowlingStyle` এর কি ধরনের ইমপ্লিমেন্টেশন হতে পারে যদি একটু দেখে নিই, তাহলে আমাদের পুরো জিনিসটা বুঝতে হয়তো আরেকটু সুবিধা হবে।

Implementations of `IBattingStyle`
```python
class LeftHandBatting(IBattingStyle):
    def bat(self) -> None:
        """Implementation for Left-hand Batting."""

class RightHandBatting(IBattingStyle):
    def bat(self) -> None:
        """Implementation for Right-hand Batting."""
```
এখানে :point_up_2: আমাদের প্রয়োজন হলে `LeftHandAggresiveBatting` একটা ইমপ্লিমেন্টেশনও যোগ করতে পারি। 

Implementations of `IBowlingStyle`
```python
class LeftHandFastBowling(IBowlingStyle):
    def bowl(self) -> None:
        """Implementation for Left-hand fast bowler."""

class RightHandFastBowling(IBowlingStyle):
    def bowl(self) -> None:
        """Implementation for Right-hand fast bowler."""
```
আর :point_up_2: এখানে তো আমাদের অবশ্যই `LeftArmOffBreakBowling`, `RightArmOffBreakBowling` ইমপ্লিমেন্টেশনগুলোর কথা চিন্তা করতেই হবে। 

আমরা পুর্বে যে তামিম, মুস্তাফিজুরের অবজেক্ট তৈরি করেছিলাম, তা বর্তমান ডিজাইনের সাপেক্ষে কেমন হবে তা দেখে নেয়া যাক :point_down:
```python
# For the sake of the example, assume Tamim is Left-hand fast bowler.
Tamim = Player(name="Tamim Iqbal", 
            country="Bangladesh",
            batting_style=LeftHandBatting(),
            bowling_style=LeftHandFastBowling())

Mustafizur = Player(name="Mustafizur Rahman", 
                country="Bangladesh", 
                batting_style=LeftHandBatting(), 
                bowling_style=LeftHandFastBowling())
```
এখন আমরা চাইলেই খুব সহজে সাকিব আল হাসান, বিরাট কোহলির মতও খেলোয়াড়দের অবজেক্ট তৈরি করে ফেলতে পারবো। যার জন্য Player ক্লাসে কোন ধরনের পরিবর্তন আসবে না। বিরাট কোহলি এর জন্য শুধু নতুন  `RightHandMediumFastBowling` এর ইমপ্লিমেন্টেশন লাগবে আমাদের, আর `RightHandBatting` এর ইমপ্লিমেন্টেশন আমাদের কাছে ইতিমধ্যেই আছে। এরপর শুধু অবজেক্ট তৈরি করার সময় তার নাম, দেশের নাম দিয়ে দিলেই হয়ে যাবে।

অনুশীলনের জন্য আপনি আরও কিছু খেলোয়াড়ের অবজেক্ট তৈরি করার কথা চিন্তা করতে পারেন। কিভাবে করবেন, আর তাদের জন্য কি লাগতে পারে।

এই যে আমরা আমাদের প্লেয়ারদের ব্যাটিং, বোলিং করার ধরনকে আলাদা রাখছি এবং তা রানটাইমে বলে দিচ্ছি - এর মাধ্যমেই আমরা **Strategy Design Pattern**-কে ফলো করছি।
এখন যদি **Strategy Design Pattern** এর কিতাবি সংজ্ঞা দেখে নিই

> The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

এই সংজ্ঞা এর সাথে আমাদের উপরের উদাহরণটা মিলিয়ে নিলেই বুঝতে পারবেন আমরা কিভাবে *Strategy Design Pattern* কে ফলো করেছি। 
ডিজাইন প্যাটার্নের ৩ টি ধরন আছে, এই *Strategy Design Pattern* তার মধ্যে **Behavioral Patterns** এর মধ্যে পড়ে।  

## References

- [RefactoringGuru: Strategy Pattern](https://refactoring.guru/design-patterns/strategy)

- [Keep it Simple with the Strategy Design Pattern](https://blog.bitsrc.io/keep-it-simple-with-the-strategy-design-pattern-c36a14c985e9)

- [Head First Design Patterns](https://www.oreilly.com/library/view/head-first-design/0596007124/)

- [Python Dependency Injection](https://testdriven.io/blog/python-dependency-injection/)