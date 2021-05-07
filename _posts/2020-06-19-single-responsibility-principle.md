---
layout: post
title: সিঙ্গেল রেস্পন্সিবিলিটি প্রিন্সিপল (Single Responsibility Principle)
description: Single Responsibility Principle
comments: true
categories: [Software Design Principle, Software Engineering]
---

অব্জেক্ট ওরিয়েন্টেড প্রোগ্রামিং নিয়ে যারা কাজ করেছেন এবং কোনদিন ডিজাইন প্রিন্সিপল বিষয়ে গুগল করেছেন - SOLID principles টার্মটার সাথে তারা কমবেশি সবাই পরিচিত।

অব্জেক্ট ওরিয়েন্টেড প্রোগ্রামিং এর ৫-টি গুরুত্বপূর্ণ ডিজাইন প্রিন্সিপলের প্রথম অক্ষর নিয়ে এই S.O.L.I.D. টার্মটির উদ্ভব হয়েছে। এইসব ডিজাইন প্রিন্সিপলগুলো একটি সফটওয়্যার ডিজাইনকে সহজে বুঝতে সাহায্য করে, সেটাকে রক্ষণাবেক্ষণে সাহায্য করে এবং পরবর্তীতে সফটওয়্যার-এর পরিধি বৃদ্ধিকে সহজ করে। সকল সফটওয়্যার ইঞ্জিনিয়ারের এইসব ডিজাইন প্রিন্সিপল সম্পর্কে ধারণা থাকা জরুরি।

আজকে আমরা এই S.O.L.I.D. -এর S নিয়ে আলোচনা করবো।

S = Single Responsibility Principle(SRP)

এই প্রিন্সিপলের মূল কথা হচ্ছে - “A class should have one, and only one, reason to change.” যেটাকে আরেকটু সহজবোধ্য করা যায় যদি বলি - একটি ক্লাস শুধুমাত্র একটি নির্দিষ্ট বিষয়েই কাজ করবে এবং সেই কাজের জন্যই শুধুমাত্র পরিবর্তিত হবে। এটি ক্লাসের মেথডের জন্যও সত্য, আবার একটি মডিউলের কথা বললে সেখানেও সত্য।

দেখুন, দৈনন্দিন জীবনে প্রতিনিয়ত আমরা এই প্রিন্সিপল মেনেই কিন্তু কাজ করছি। উদাহরণস্বরূপ, ইউনিভার্সিটিতে ভর্তি প্রক্রিয়ার কথা চিন্তা করতে পারি। আমরা রেজিস্ট্রার অফিস থেকে একটা ফর্ম তুলি এবং সেটা ফিলাপ করি। এরপরে ব্যাংকে নির্দিষ্ট পরিমাণ টাকা জমা দিয়ে রিসিপ্ট সংগ্রহ করি, মেডিকেল চেকাপ করিয়ে একটা এপ্রুভাল সংগ্রহ করি, আরো কোন বাড়তি কাজ থাকলে সেগুলো সম্পন্ন করে সকল কাগজ একসাথে আবার রেজিস্ট্রেশন অফিসে জমা দিই। মেডিকেল চেকের সময় কিন্তু ডাক্তার টাকা জমা নিচ্ছে না, কিংবা ব্যাংকে টাকা জমা দেয়ার সময় আমার হেলথ চেকাপের কাজটিও করে দিচ্ছে না।

উপরের উদাহরণের প্রত্যেকটি কাজ স্বতন্ত্র, শুধু রেজিস্ট্রার অফিস চেক করে প্রত্যেকটি কাজ সঠিকভাবে সম্পন্ন হয়েছি কি-না। প্রত্যেকে আলাদাভাবে কিভাবে কাজ করেছে সেটা কিন্তু আবার তার দেখার বিষয় না।

উপরের বাস্তবিক ঘটনা থেকে একধরনের আন্দাজ পাওয়ার কথা Single Responsibility বলতে কি বোঝায়। সেখানে একজনের কাজের ধরনে পরিবর্তন হলে অন্য আরেকজনের কাজে তা প্রভাব ফেলবে না। ভার্সিটি এখন যদি বিকাশের মাধ্যমে টাকা নেয়ার শুরু করে তবে কিন্তু শুধুমাত্র একটা জায়গায় পরিবর্তন হচ্ছে, বাকিসব আগের মতোই থাকছে। আমরা এই লিখা থেকে যা বুঝতে চাচ্ছি তার মূল উদ্দেশ্যই এখানে। যে যার যার মতো কাজ করলে একজনের পরিবর্তন অন্যজনকে প্রভাবিত করবে না। যা একটি ভালো সফটওয়্যারের বৈশিষ্ট্য হওয়া উচিত।

যেহেতু এখানে আমরা অব্জেক্ট ওরিয়েন্টেড প্রোগ্রামিং এর প্রিন্সিপল হিসেবে একে ব্যাখ্যা করতে চাচ্ছি, এবার তবে কোড দেখা যাক।
(পাইথন প্রোগ্রামিং ল্যাঙ্গুয়েজ ব্যবহার করে কোডিং উদাহরণগুলো দেয়া হবে)

```python
class Student:
    def __init__(self, name, id):
        self.name = name
        self.id = id

    def calculate_semester_fee(self):
        # calculate semester_fee
        return semester_fee

    def calculate_semester_cgpa(self):
        # calculate semester_cgpa
        if semester_cgpa < 2.0:
            # send message to guardian
        return semester_cgpa
```

এখানে Stuednt - নামের একটি ক্লাস তৈরি করা হয়েছে। যেখানে স্বাভাবিকভাবেই Student এর নাম এবং আইডি ফিল্ড আছে(__init__ মেথডে ইনিশিয়ালাইজেশনের কাজ হয়ে থাকে)। তাছাড়া আমরা আরো দু’টি মেথড দেখতে পাচ্ছি -

১. calculate_semester_fee - যার কাজ সেমিস্টারের ফি ক্যালকুলেট করা,

২. calculate_semester_cgpa - যার কাজ সেমিস্টারের CGPA ক্যালকুলেট করা।

Single Responsibility Principle এর মূলকথা এখানে কিভাবে ক্ষুন্ন হচ্ছে তা একটু দেখে নেয়া যাক। নিম্নোক্ত ব্যাখ্যা পড়ার আগে নিজে একবার চেষ্টা করে দেখুন, ভুলগুলো বের করতে পারেন কি-না কিংবা ভুলগুলো নিম্নোক্ত ব্যাখ্যার সাথে মিলে কি-না।
একটা Student ক্লাসে যা কিছু থাকবে তার প্রতিটি-ই Student কেন্দ্রিক হতে হবে। আর এখানে যদি কোন পরিবর্তন আনতেই হয় সেটাকে অবশ্যই Student সম্বন্ধীয় হতে হবে।

প্রথমে, calculate_semester_fee মেথডের কথায় আসি। একজন Student প্রতি সেমিস্টারে কি পরিমাণ ফি প্রদান করবে সেটা সেই Student এর দেখার বিষয় না। এটা ইউনিভার্সিটির কর্তৃপক্ষের দেখার বিষয়।

একটা নির্দিষ্ট সময় পর পর ইউনিভার্সিটিগুলোর সেমিস্টার ফি-র পরিবর্তন হয়। তাছাড়াও নানা কারণে যেমন মাঝে একবার ভ্যাট দিতে হলো, এমন কোন কারণে সেমিস্টার ফি ক্যালকুলেশনের পরিবর্তন ঘটতে পারে। আমার বর্তমান Student ক্লাসের ডিজাইন অনুযায়ী, সেমিস্টার ফি-র ক্যালকুলেশন পরিবর্তনের জন্য আমার Student ক্লাসের calculate_semester_fee মেথডের ভিতরে পরিবর্তন করতে হচ্ছে। যেটা Student ক্লাসের সাথে সরাসরি সম্পর্কিত না।

দ্বিতীয়ত, calculate_semester_cgpa মেথডে একজন Student এর এক সেমিস্টারের সিজিপিএ হিসেব করা হচ্ছে। সেই সিজিপিএ যদি আবার ২ এর থেকে কম হয় সেক্ষেত্রে অভিবাবকের কাছে ম্যাসেজ চলে যাবে। একটা ক্লাস দিয়ে সাধারণত আমরা কোনকিছুকে উপস্থাপন করি, উপস্থাপনায় কি ছাত্র-ছাত্রীর সিজিপিএ কিভাবে ক্যালকুলেট করা হচ্ছে তা থাকা উচিত? তার উপর সেই সিজিপিএ একটা নির্দিষ্ট সংখ্যার নিচে গেলে কি করতে হবে তাও বলে দেয়া হচ্ছে। ইউনিভার্সিটি যদি কোনদিন সিদ্ধান্ত নেয় ২ এর বদলে ১.৫ এর নিচে সিজিপিএ হলে স্তুডেন্টের অভিবাবকের কাছে ম্যাসেজ চলে যাবে কিংবা অভিভাবকের মোবাইলে ম্যাসেজ যাওয়ার পরিবর্তে মেইল যাবে  এসবক্ষেত্রে আমাকে কোডে পরিবর্তন করতে হচ্ছে, যেটা কাম্য নয়।

Student ক্লাসে আমার এমন দু’টি মেথড আছে যারা আমার ক্লাসের সাথে সরাসরি সম্পর্কিত না এবং তাদের জন্য আমার ক্লাসের কোডকে বারবার পরিবর্তন করতে হচ্ছে। এখানেই আমি Single Responsibility Principle মান্য করি নি।

তবে সমাধান কি?
উপরোক্ত ক্লাসকে ভেঙ্গে আমরা নতুন তিনটি ক্লাস তৈরি করতে পারি, যার প্রত্যেকে নির্দিষ্ট কাজের জন্য দায়ী থাকবে।

```python
class Student:
    def __init__(self, name, id):
        self.name = name
        self.id = id

class SemesterFeeCalculationService:
    def calculate_semester_fee(self, course_taken):
        # calculate semester_fee
        return semester_fee

class CgpaCalculationService:
    def calculate_semester_cgpa(self, student, minimum_cgpa):
        # calculate semester_cgpa
        if semester_cgpa < minimum_cgpa:
            # send message to guardian
        return semester_cgpa
```

এখন আমি যদি Student ক্লাসে নতুন ফিল্ড যোগ করতে চাই(যেমনঃ address) তবে **Student** ক্লাসকে, সেমিস্টার ফি ক্যালকুলেশনে পরিবর্তন করতে চাইলে **SemesterFeeCalculationService**  ক্লাসকে কিংবা সিজিপিএ ক্যালকুলেশনে পরিবর্তন করতে চাইলে শুধুমাত্র ***CgpaCalculationService** ক্লাসকেই আমার পরিবর্তন করতে হবে। প্রত্যেকটি ক্লাস একটি নির্দিষ্ট ডোমেইনে কাজ করবে বলে এটি সম্ভব হয়েছে। উপরোক্ত প্রতিটা ক্লাসেই আমি নতুন মেথড এড করতে পারি, যার জন্য অন্য কারো কাজে কোন ক্ষতিও হবে না।

একটি ক্লাস ভেঙ্গে নতুন ৩ টি ক্লাস তৈরি করার পরও এক জায়গায় সমস্যা রয়ে গেছে, সেটা **CgpaCalculationService** ক্লাসে। ক্যালকুলেটরের কাজ হবে শুধু ক্যালকুলেশন করে দেয়া, কম বা বেশি চেক করা তার কাজ হওয়া উচিত না। আবার সেটার প্রেক্ষিতে কোন একশন নেয়াও তার কাজের মধ্যে পড়ে না। এখন আমি মোবাইলে ম্যাসেজ পাঠাচ্ছি কিন্তু পরে মেইল যদি করার কথা চিন্তা করি কিংবা উভয়ই একসাথে করার কথা চিন্তা করি তবে কিন্তু আমার এই মেথডে পরিবর্তন আনতে হবে।

আসুন, এই সমস্যাটা কিভাবে দূর করা যায় তা একটু দেখে নিই। প্রথমে **CgpaCalculationService** ক্লাসকে পরিষ্কার করি।

```python
class CgpaCalculationService:
    def calculate_semester_cgpa(self, course_gpas):
        # calculate semester_cgpa
        return semester_cgpa
```

আমরা কন্ডিশন চেকিং এর যে জায়গাটা গায়েব করে দিলাম সেটা তো কাউকে না কাউকে দেখতে হবে, এখন সেই ক্লাসটা তৈরি করা যাক -

```python
class DecisionMakingService:
    def __init__(self, cgpa_calculation_service, minimum_cgpa, email_service, message_service):
        self.cgpa_calculation_service = cgpa_calculation_service
        self.minimum_cgpa = minimum_cgpa
        self.email_service = email_service
        self.messaging_service = messaging_service

    def make_decision(self, student_course_gpas):
        semester_cgpa = cgpa_calculation_service.calculate_semester_cgpa(student_course_gpas)

        if semester_cgpa < self.minimum_cgpa:
            self.email_service.send_mail("আপনার ছেলে/মেয়ে পঁচা")
            self.messaging_service.send_message("আপনার ছেলে/মেয়ে পঁচা")
        return semester_cgpa
```

উপরোক্ত **DecisionMakingService**-এ আরো রিফ্যাক্টরিং করে উন্নতির সুযোগ আছে। তবে সেগুলো অন্য আরেকদিনের জন্য থাকলো।

Single Responsibility Principle বুঝতে পারা সব থেকে সহজ অন্য প্রিন্সিপলগুলো থেকে। কিন্তু এই প্রিন্সিপলটাই সবচেয়ে বেশি লঙ্ঘন করে থাকি আমরা।

>দ্রষ্টব্যঃ এই লিখায় পাইথন কোডকে অনেকটা সুডোকোডের মতো করে ব্যবহার করা হয়েছে। আর লিখার মূল ফোকাস যেন একটি প্রিন্সিপলেই থাকে সেজন্য বেশকিছু জায়গায় ছাড় দিতে হয়েছে। লিখার কোন ভুল থাকলে জানাবেন এবং মাফ করবেন।

#### References

- [SOLID Principles: Explanation and examples](https://itnext.io/solid-principles-explanation-and-examples-715b975dcad4)