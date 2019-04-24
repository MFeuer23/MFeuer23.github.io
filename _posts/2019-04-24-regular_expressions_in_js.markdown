---
layout: post
title:      "Regular Expressions in JS"
date:       2019-04-24 18:10:34 +0000
permalink:  regular_expressions_in_js
---


Regular Expressions are extremely useful! They allow you to check a string of characters for patterns or character combinations. In JavaScript, regular expressions are also objects. There are specific ways to define different patterns you may be looking for, and different ways to go about using Regular expressions. So here's a little guide. [This link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) is also particularly useful! and [Here's](https://regexr.com/) a great interactive resource!

## Creating a Regular Expression
There are two ways to create a regular expression in JavaScript:

Using a 'RegEx literal' or a patern enclosed between slashes, for example: `let regex = /ab + c/`

Or using a constructor function of the `RegExp` object: `let regex = new RegExp('ab+c');`

If you know that the regular expression pattern will be changing, it's best to use the constructor function. Otherwise, if the regular expression remains constant, using the regex literal can improve performance.

## Regular Expression Patterns
Regular Expressions tend to look like gibberish, but let's see if we can make sense of all the different kinds of patterns we can look for.
1.  **Simple Patterns** - For example, `/abc/` matches only when 'abc' occur together and in that order. 'Sing your abc's!' would match, but 'Grab crab' would not.
2.  **Complex Patterns/Special Characters** - when you are looking for something besides a direct match. Like one or more x's, or finding white space. For example, `/ab*c/` where the `*` after 'b' means '0 or more occurrences of the preceding item' so any string with a substring of 'abbc', 'ac', 'abbbbbbbc' would all match.
    * **Assertions** - assertions include look-ahead, and look-behind expressions.
        * **Lookahead assertion** `x(?=y)`: Matches `x` only if `x` is followed by `y`. For example, `/Hello(?=Dolly)/` matches "Hello" only if it is followed by  "Dolly". `/Hello(?=Dolly|Kitty)/` matches "Hello" only if it is followed by "Dolly" or "Kitty". However the match result will only be "Hello".
        * **Negative Lookahead assertion** `x(?!y)`: Matches `x` only if `x` is not followed by `y`. For example, `/\d+(?!\.)/` will match a number only if it is not followed by a decimal point. So `/\d+(?!\.)/.exec('6.02')` matches ".02" but not "6".
        * **Lookbehind assertion** `(?<=y)x`: Matches `x` only if `x` is preceded by `y`. For example, `/(?<=Hello)Dolly/` matches "Dolly" only if it is preceded by "Hello". `/(?<=Hello|Hi)Dolly/` matches "Dolly" only if it is preceded by "Hello" or "Hi". However the match result will only be "Dolly".
        * **Negative Lookbehind assertion** `(?<!y)x`: Matches `x` only if `x` is not preceded by `y`. For example, `/(?<!-)\d+/` matches a number only if it is not preceded by a minus sign. `/(?<!-)\d+/.exec('8')` matches "8". `/(?<!-)\d+/.exec('-8')`  has no match found.

    * ** Boundaries** - boundaries are the beginnings and endings of lines and words.
        * `^` matches the beginning of an input. `/^A/` matches the first "A" in "Anagram" 
        * `$` matches the end of an input. `/a$/` matches the last "a" in "bandana"
        * `\b` matches a word boundary. `/\bm/` matches the "m" in "moon" and `/n\b/` matches the "n" in "moon", but `/m\b/` doesn't match anything because "m" is followed by a word character. `/x\bx/` will never match anything because a word character cannot be followed by both a non-word and a word character at the same time.
        * `\B` matches a non-word boundary. It is the negated version of `/b`.
    * **Character Classes** - distinguish different kinds of characters, like letters vs. digits.
        * `.` matches any single character except line breaks. `/.E/` won't match "E" at the beginning of a new line, but `/E./` will. 
        * `\d` matches any digit. Equivalent to `[0-9]`. `/\d/` or `/[0-9]/` matches "4" in "Re4lly".
        * `\D` matches any non-digit character. Equivalent to `[^0-9]`. `/\D/` or `/[^0-9]/` matches "R", "e", "l", "l" and "y" in "Re4lly".
        * `\w` matches any alphanumeric character, including the underscore. Equivalent to `[A-Z-z0-9_]`
        * `\W` matches any non alphanumeric character, like !@#$%. Equivalent to `[^A-Z-z0-9_]`
        * `\s` matches any single white space character, including space, tab, and other unicode spaces.
        * `\S` matches a single character that is not whitespace.
        * There are a lot of these that I haven't seen as much in the wild- for more refer to [this link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Character_Classes)
    * **Groups and Ranges**
        * `x|y`  matches either `x` or `y`. `/green|red/`
        * `[abcd]` or `[a-d]` is a character set. This will match any one of the enclosed characters. You can specify a range of characters by using a hyphen. You can also specify matching a hyphen by including it at the beginning or end of the square brackets `[abcd-]`.
        * `[^abcd]` or `[^a-d]` is a negated character set. This will match anything that is not one of the enclosed characters. You can specify a range of characters by using a hyphen.
        * `(x)` is a **capturing group** that will match `x` and remember that match.  A regular expression can have multiple capturing groups. Capturing groups have a performance penalty, so only use if you need the matched substring to be recalled. There are also named capturing groups and non-capturing groups. Learn more [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges)
    * **Quantifiers** - indicate numbers of characters of expressions to match
        * `x*` matches the preceding item, "x" 0 or more times. `/ha*/` would match "haaaa" in "haaaappy birthday" and "h" in "hot", but nothing in "apple"
        * `x+` matches the preceding item, "x" 1 or more times.
        * `x?` matches the preceding item, "x" 0 or 1 time. If used immediately after any of the other quantifiers, it makes the quantifier non-greedy- meaning it matches the minimum number of times as upposed to the default of matching the maximum number of times.
        * `x{n}` matches exactly `n` occurrences of the preceding item, "x".
        * `x{n,}` matches at least `n` occurrences of the preceding item, "x".
        * `x{n, m}` where `m > n` and both are positive integers (`n` could also be 0), this would match at least `n` and at most `m` occurrences of "x".
    * **Unicode Properties** - distinguishing based on unicode character properties, such as upper and lower case, symbols, and punctuation.


Additionally, the concepts of:
* **Escaping** - escaping is used if you need to use any of the special characters in a literal context (for example, actually searching for a '+'). You would have to escape it by putting a backslash in front of it. So to search for "1" followed by "+" followed by "b", you'd use `/a\+b/`. The backslash escaptes the '+' making it literal instead of a quantifier. If using the RegExp constructor with a string literal, it needs to be escaped at the string literal level. So `/a\*b/` and `new RegExp("a\\*b")` create the same expression.
* **Using Parentheses** - using parentheses around any part of the regular expression causes that part of the matched substring to be remembered and recalled for other use. Read more about using parenthesized substring matches [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#Using_parenthesized_substring_matches)

## Working with Regular Expressions
There are several methods you can use with Regular Expressions.
* `exec` is a `RegExp` method that searches for a match in a string and returns an array of information, or null if there is no match.
* `test` is another `RegExp` method that tests for a match in a string and returns `true` or `false`.
* `match` is a `String` method that searches for a match in a string that returns an array of information, or null if there is no match.
* `matchAll` is a `String` method that returns an iterator containing all of the matches, including capturing groups.
* `search` is a `String` method that searches for a match in a string and returns the index of the match of -1 if there is no match.
* `replace` is a `String` method that searches for a match in a string and replaces the matched substring with a replacement substring.
* `split` is a `String` method that uses a regular expression or a fixed string to break a string into an array of substrings. 


![](https://media.giphy.com/media/4ZkydYFRV6CYprhvF7/giphy.gif)

Thanks for reading. Regular expressions are a pretty extensive topic, and there is much more to be said.. but hopefully this was helpful!








