---
layout:     post
title:      Spock Ahoi!
date:       2015-07-06
summary:    Use Spock! Your tests want to be fabulous as well!
categories: testing, android
---

Writing good tests is a hard, time consuming and confusing task. 

**Just take a look at the following test case:**

```java
public class UniverseTestCase extends InstrumentationTestCase {

    // Tests

    public void testThatTheSumOfTheUniverseIs42() {
        Universe universe = new Universe();
        int sum = universe.sum();
        assertThat(sum).is(42);
    }

    public void testThatTheSumOfTheUniverseIs43IfItContainsOneBlackHole() {
        Universe universe = new Universe();
        BlackHole blackHole = new BlackHole();
        universe.add(blackHole);
        int sum = universe.sum();
        assertThat(sum).is(43);
    }

    public void testThatTheSumOfTheUniverseThrowsIllegalStateExceptionIfItContainsChuckNorris() {
        Universe universe = new Universe();
        universe.add(ChuckNorris.getInstance());

        try {
            universe.sum();

     	    fail("Exception not thrown!");
        } catch(IllegalStateException exception) {
            // Success
        }
    }
}
```

###Why is it bad?

**Well, let's state the obvious:**

```java 
public void testThatTheSumOfTheUniverseThrowsIllegalStateExceptionIfItContainsChuckNorris() {}
```
This is probably not the most human readable method name you'll ever encounter. It is decriptive yet very, and I mean very, very long and therefore hardly readable.

**There are a few more things that are not optimal:**

```java
try {
	universe.sum();
	
	fail("Exception not thrown!");
} catch(IllegalStateException exception) {
	// Success
}

```

Using try and catch in your tests to ensure something is throwing an exception is not nice either.

Also, the second test looks very crowded. Spacing and some comments may fix the problem but we can do better for sure...

###Well,... how?

**Spock!**

![spock](/images/spock/spock_character.jpg)

**Tell me more!**

There is a testing framework called [Spock](https://github.com/spockframework/spock). It is inspired by [JUnit](http://junit.org/), [jMock](http://www.jmock.org/), [RSpec](http://rspec.info/), [Groovy](http://groovy-lang.org/) many more well-know projects. It is designed to be as descriptive and narrative as possible. So let's see how our tests look like after the Spock face lifting.

**Woop Woop!**

```groovy
@Title("Universe.sum()")
class UniverseSumSpecs extends AndroidSpecification {

    // Scenarios

    def "is 42 by default"() {

        given: "A new universe"
            Universe universe = new Universe()

        when: "asked for the sum"
            int sum = universe.sum()

        then: "it is 42"
            sum == 42
    }

    def "is 43 if contains one black hole"() {

        given: "A new universe"
            Universe universe = new Universe()

        and: "a new black hole is added to it"
            Blackhole blackhole = new Blackhole()
            universe.add(blackhole)

        when: "asked for the sum"
            int sum = universe.sum()

        then: "it is 43"
            sum == 43
    }

    def "throws IllegalStateException if it contains chuck norris"() {

        given: "A new universe"
            Universe universe = new Universe()

        and: "a new black hole is added to it"
            Blackhole blackhole = new Blackhole()
            universe.add(blackhole);

        when: "asked for the sum"
            int sum = universe.sum()

        then: "it throws an IllegalStateException"
            thrown(IllegalStateException)
    }
}
```

**First things first:**

This is not Java. This is Groovy. Why? Because Spock is a framework for Java and Groovy applications. And out of the two languages, Groovy is way cooler and way more modern than good old Java 6. We'll see later why.

**So lets examine our changes:**

```groovy
@Title("Universe.sum()")
class UniverseSumSpecs extends AndroidSpecification {
```

In Spock we write *Specifactions* not *Test Suites*. This is a well-known concept in [Behavior-driven development](https://en.wikipedia.org/wiki/Behavior-driven_development#Behavioural_specifications). All our specifications have to extend the ```AndroidSpecification``` class. The ```@Title("")``` annotation describes what the specification is about. At [Rheinfabrik](http://www.rheinfabrik.de/) we decided to use a string based on the class name and the method name that is under test. But basically it can be everything such as "Universe class specification that ensures the correctness of the method sum().".

**Let's get back to the tests:**

```groovy
def "is 42 by default"() {

	given: "A new universe"
		Universe universe = new Universe()

	when: "asked for the sum"
    	int sum = universe.sum()

	then: "it is 42"
    	sum == 42
}
```

**That's nice!**

As stated above Spock is inspired by some Behavior-driven development syntax frameworks such as [RSpec](http://rspec.info/). With the use of custom labels for ```given```, ```when``` and ```then``` we can provide nice descriptions for our now [Gherkin](https://github.com/cucumber/cucumber/wiki/Gherkin) like styled tests. Note that we could read those descriptions in code and log them if we want to. 

Oh and did I mention that we  no longer need camelcase method names? A simple string is enough to define a method in Groovy. This is espacially handy for our last test:

```groovy
def "throws IllegalStateException if it contains chuck norris"() {}
```

**Let's dive deeper into the last test:**

```groovy
given: "A new universe"
	Universe universe = new Universe()

and: "a new black hole is added to it"
    Blackhole blackhole = new Blackhole()
    universe.add(blackhole)

when: "asked for the sum"
    int sum = universe.sum()

then: "it throws an IllegalStateException"
    thrown(IllegalStateException)
```

So instead of try and catch we can use the method ```thrown()```. It can take a specific exception or even no argument at all if you simply want to assert that anything is thrown.

**That's it?**

Of course not! This post aims to provide only a small glance over the possibilities of the Spock Framework. For more goodness take a look at the [excellent documentation](http://spockframework.github.io/spock/docs/1.0/index.html). There you may find features that will blow your mind! Easy *Stubbing*, *Mocking* and the amazing output of a failing test are just a few worth mentioning. I might add another post describing some of the nicest gems in Spock. Let me know if you're interested. Meanwhile you can take a look at the tests of my [MVVM example project](https://github.com/rheinfabrik/android-mvvm-example/tree/master/MVVM-Example/app/src/androidTest/groovy).

**So how to get it?**

In order to get Spock running on Android you have to use [android-spock](https://github.com/pieces029/android-spock). It is fairly easy to integrate it into your existing projects. Just follow the rich documentation and you are good to go!

**So remember:**

> Use Spock! Your tests want to be fabulous as well!
