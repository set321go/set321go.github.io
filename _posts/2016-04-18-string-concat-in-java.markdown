---
published: true
title: String concat in java
layout: post
---
We have a piece of code in our product that looks a bit like this

    Iterator<String> parts = Splitter.split('/').iterator()
    String first = parts.next()
    String remainder = Joiner.join(':', parts)

It's used to do uri processing but it really bugs me that it splits the string to just put it back together two lines on. So I decided to test it out and try and make it more efficient.

This was my first try using the micro benchmarking tool [jmh](http://openjdk.java.net/projects/code-tools/jmh/) That some of my colleague have talked about, I have to say from zero to a testable example was ridiculously easy. You can create a benchmark and have it running from the details on that first page.

So my plan was to test 
* Java libs
* Guava
* The fastest possible solution

To begin with I wanted to get some idea of how fast the current solution is and how it compares. The java `String.split` produces an array so after reading the first part I needed to perform an array copy to remove the first element, which seems like a deal breaker from a performance point of view.

Finally rolling our own, we can make use of `String.indexOf` and `String.substring` in this special case because we have a 'char' delimiter. 

    private List<String> split(final char delimiter, final String input) {
        List<String> list = new ArrayList<String>();
        int pos = 0, end;
        while ((end = input.indexOf(delimiter, pos)) >= 0) {
            list.add(input.substring(pos, end));
            pos = end + 1;
        }

        return list;
    }


So was it any good? Yes it was it was unsurprisingly the fastest solution but rolling your own string methods is a bit absurd in the 99% case, if you got to that point you probably want to take a step back and have a think about the design that led you here.

    StringSeperationAndCombinationOfUri.guavaSplitJoin    thrpt  200   3135846.935 ±  49771.302  ops/s
    StringSeperationAndCombinationOfUri.indexOfSplit      thrpt  200   3548182.592 ±  56492.715  ops/s
    StringSeperationAndCombinationOfUri.javaxSplitJoin    thrpt  200   2676796.175 ±  48400.697  ops/s

Finally after testing the existing design with different options can we make it better? Probably, we could do that split and join step in one by replacing the delimiter. When we do this we need to take an extra step to remove any leading slashes if they exist. So after some more benchmarking using replace is about 1 magnitude faster.

    StringSeperationAndCombinationOfUri.guavaReplace      thrpt  200  13124328.801 ± 130148.437  ops/s
    StringSeperationAndCombinationOfUri.indexOfReplace    thrpt  200  13029235.458 ± 215467.837  ops/s
    StringSeperationAndCombinationOfUri.javaxReplace      thrpt  200  14885437.486 ± 244976.090  ops/s
    StringSeperationAndCombinationOfUri.streamingReplace  thrpt  200   1291974.573 ±  37885.598  ops/s

You can find all of these tests on github [here](https://github.com/set321go/benchmark/blob/master/src/main/java/org/sample/StringSeperationAndCombinationOfUri.java)