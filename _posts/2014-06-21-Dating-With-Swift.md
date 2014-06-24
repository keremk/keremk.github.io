---
layout: post
title: Dating Swift
description: "Using Swift to create more usable Date APIs that are reminiscent of Ruby's ActiveSupport Date APIs."
modified: 2014-06-22
category: articles
tags: [swift, ios, objectivec, objective-c, ruby, NSDate, NSDateComponents, ActiveSupport, date, DSL]
comments: true  
---

This year Christmas arrived in June for the iOS developers. Out of nowhere, Apple introduced a surprise new language, Swift. Following its introduction, there has been a lot of blog posts on it. So I figured, I should contribute as well.

When I am learning something new, I like to take on a mission and go from there. In this case, my mission is to take the extremely verbose NSDate, NSDateComponents, NSCalendar APIs and provide wrappers using the niceties provided by Swift. Dealing with date computations is a pretty regular requirement for many applications. Reducing the cognitive load on dealing with those computations is a worthy mission to take on. Ruby's ActiveSupport provides some very useful [extensions](http://guides.rubyonrails.org/active_support_core_extensions.html#extensions-to-date) to deal with many Date calculations. It is now possible to bring the same level of syntactic sugar for Date calculations to iOS using Swift. This is also a great exercise on understanding building Domain Specific Languages (DSL) on top of Swift and learning more about some of the features Swift introduces.

## Expressing days, months, years

Writing readable and concise code is always a great win. Well, out-of-the-box in Objective-C, dealing with Date APIs does not really lend itself to a very concise code. (For Objective-C, there is a great project called [ObjectiveSugar](https://github.com/supermarin/ObjectiveSugar) that adds simplifying syntactic sugar.)

So let's start with expressing days, months, years etc. in a very readable format. How about something like 3.days, 4.months, 2.years ?

Luckily literal integers are of struct type ```Int``` in Swift. So you can extend them, just like you can extend any other types.

      extension Int {     
        var days: NSTimeInterval {
          let DAY_IN_SECONDS = 60 * 60 * 24
          var days:Double = Double(DAY_IN_SECONDS) * Double(self)
          return days
        }
      }

Voila! Now you can type ```3.days``` and you will get the corresponding ```NSTimeInterval``` that describes 3 days. 

## Calculating dates

Now that we extended the ```Int``` type to return ```NSTimeInterval``` for days, months, years etc. we can start making some date calculations. 

      NSDate.date()

returns today's date. Let's try and calculate 

      NSDate.date() - 2.months

If we use a similar extension to the above ```Int``` extension, unfortunately we will not be taking into account the correct number of days in a month for the last 2 months. Our months extension will just assume that every month is 30 days, and return the number of seconds for 30 days. And then if we just use the ```dateByAddingTimeInterval``` method on ```NSDate```, we won't get our expected result. To solve this problem, as you know from the Cocoa APIs, we need to use ```NSDateComponents``` and explicitly indicate that we are subtracting months. In order to do that, we need to differentiate between one month worth of seconds vs. thirty day worth of seconds. Type system comes to the rescue:

Let's introduce simple new type called TimeInterval:

      struct TimeInterval {
        var interval: Int
        var unit: TimeIntervalUnit
                
        init(interval: Int, unit: TimeIntervalUnit) {
          self.interval = interval
          self.unit = unit
        }
      }

where the TimeIntervalUnit is the below enumeration:

      enum TimeIntervalUnit {
        case Seconds, Minutes, Hours, Days, Months, Years
        
        func dateComponents(interval: Int) -> NSDateComponents {
          var components:NSDateComponents = NSDateComponents()

          switch (self) {
            case .Seconds:
              components.second = interval
            case .Minutes:
              components.minute = interval
            case .Days:
              components.day = interval
            case .Months:
              components.month = interval
            case .Years:
              components.year = interval
            default:
              components.day = interval
          }
          return components
        }
      }

Notice how we can add functions to enumerations! In this case our function simply creates an ```NSDateComponents``` object and maps enumerations to the ```NSDateComponent``` types.

With that we can now change our extension to ```Int``` to return ```TimeInterval``` instead of the ```NSTimeInterval``` :

      extension Int {
        var months: TimeInterval {
          return TimeInterval(interval: self, unit: TimeIntervalUnit.Months);
        }
      }

And now with that we can create a new operator overload for dates to take ```TimeInterval``` as the right operand:

      @infix func - (let left:NSDate, let right:TimeInterval) -> NSDate {
        var calendar = NSCalendar.currentCalendar()
        var components = right.unit.dateComponents(-right.interval)
        return calendar.dateByAddingComponents(components, toDate: left, options: nil)
      }

So now the below gives us the correct date (exactly one month from today, not 30 days):

      var lastMonth = NSDate.date() - 1.months

Well now that we have a strong type called Month, wouldn't it be great if we can do this too?

      var twoMonthsAgo = 2.months.ago

And of course, it is doable as well, lets go back to TimeInterval:

      struct TimeInterval {
        var interval: Int
        var unit: TimeIntervalUnit
        
        var ago: NSDate {
          var calendar = NSCalendar.currentCalendar()
          let today = NSDate.date()
          var components = unit.dateComponents(-self.interval)
          return calendar.dateByAddingComponents(components, toDate: today!, options: nil)
        }
        
        init(interval: Int, unit: TimeIntervalUnit) {
          self.interval = interval
          self.unit = unit
        }
      }

## Comparing dates

Cocoa also has very verbose APIs to compare 2 dates with each other, and this presents another perfect reason to once again use operator overloads:

      @infix func < (let left:NSDate, let right: NSDate) -> Bool {
        var result:NSComparisonResult = left.compare(right)
        var isEarlier = false
        if (result == NSComparisonResult.OrderedAscending) {
          isEarlier = true
        }
        return isEarlier
      } 

So now this is much better and more readable (at least in my opinion) than using the ```NSOrderAscending```, ```NSOrderDescending``` stuff...

      var lastMonth = NSDate.date() - 1.months
      if (lastMonth < NSDate.date()) {
        println("Last month is earlier than this month!")
      }

## Simple date helpers

While we are at it, wouldn't it be great to introduce quick helpers for well known colloquial date accessors, like yesterday, last week, tomorrow etc.? That is also easy, let's see how we can build out yesterday. Here we will be adding a class function instead of an instance one to the ```NSDate```

      extension NSDate {
        class func yesterday() -> NSDate {
          return NSDate.date() - 1.days
        }
      } 

And voila! 

      var yesterday = NSDate.yesterday()

## Formatting Dates

Another painful API is the verbose NSDateFormatter APIs to format ```NSDate``` to ```String``` and back. Wouldn't it be great if there was a function like toS: (using camel casing in Swift, as opposed to the to_s in Ruby)

      var formatted = yesterday.toS("mm/dd/yyyy")

Well easy:

      extension NSDate {  
        func toS(let format:String) -> String? {
          var formatter:NSDateFormatter = NSDateFormatter()
          formatter.locale = NSLocale(localeIdentifier: "en_US_POSIX")
          formatter.timeZone = NSTimeZone()
          formatter.dateFormat = format          
          return formatter.stringFromDate(self)
        }
      }

      var yesterday = NSDate.yesterday().toS("MM/dd")

And here is the inverse:

      extension String {  
        func toDate(let format:String = "dd/MM/yyyy") -> NSDate? {
          var formatter:NSDateFormatter = NSDateFormatter()
          formatter.locale = NSLocale(localeIdentifier: "en_US_POSIX")
          formatter.timeZone = NSTimeZone()
          formatter.dateFormat = format
          
          return formatter.dateFromString(self)
        }
      }

      var withFormat = "12/01/2014".toDate(format: "MM/dd/yyyy")
      var usingDefaultFormat = "12/01/2014".toDate()


## One more thing

So there is always one more thing (Or we always hoped there is). This one is not about date APIs but something else we use all the time; iterating over a range, you know the good old for loops we used to use before blocks and functional concepts were in. Well, Swift introduces a great new built-in feature called Ranges. So you can write code like:

      for i in (0..7) {
        println("Hello \i")
      }

Well that is nice, but not as nice as:

      (0..7).each { println("\$0") }

Can we do that in Swift? Yep! For that we need to extend the ```Range``` built-in type with a new function called ```each```. This function will take a closure with a generic type (where we pass in the iteration index) and returns void. Here it is:

      extension Range {
        func each(iterator : (T) -> ()) {
            for i in self {
              iterator(i)
            }
          }
      }

And here is a controversial (may be going too far with operator overloading) but fun one. Do you want to append to the end of a string using a << operator? (Like Ruby)

      @infix func <<<T> (inout left: T[], let right: T) {
        left.append(right)
      }

      var input = Int[]()

      (0..10).each { input << $0 }

## Conclusion

In short, Swift gives us a very expressive set of language tools to create concise and useful APIs. The current Cocoa API set is not yet making use of these language features. So using APIs like NSDate out-of-the-box is still a pain in the ass. But the good news is, building your own wrappers on top of the existing Cocoa APIs is very easy and a lot fun. Apple will probably introduce more native Swift APIs (probably starting with Foundation APIs) in the next year. But in the meantime, we can build our own with very little effort as well.

You can access the full playground for this a [gist here](https://gist.github.com/keremk/c6da4c24953eb66f21b2).

Have fun!
