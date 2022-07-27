[1]: http://web.archive.org/web/20021120101304/http://www.stsc.hill.af.mil/crosstalk/2001/08/welker.html
[2]: http://www.verifysoft.com/en_maintainability.html

# Maintainability Index for Omnis
This repo makes it possible for Omnis(8.1) users to track the maintainability of their code. The given library provides an object class that contains the full logic behind the maintainability index calculation and a window class to test the object and to display the results.
Since every environment can be different, everything is fairly easy to customize for self-interest. This repo should mainly act as a starting point.
## Maintainability Index
The maintainability index is supposed to give you a rough impression on whether your code could need a refactoring. To do that, the maintainability index makes use of many different code measurement possibilities and puts them all in one formula. In our case, the MI-formula includes the following measurements:

* [Halstead Volume](https://en.wikipedia.org/wiki/Halstead_complexity_measures)
* [McCabe's Cyclomatic Complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity)
* Lines of code
* Comment rate

The formula was inspired by [The Software Maintainability Index Revisited][1] by Kurt D. Welker and [Verifysoft][2] and looks as following.
~~~~
(171 - 5.2 * ln(Halstead Volume) -0.23 * Cyclomatic Complexity - 16.2 * ln(Lines of code)) + 
(50 * sin(sqrt(2.4 * Comment rate)))
~~~~
Especially important is to know that the comment rate is not in percent but in decimal and that the sine is expressed in radians.

## Evaluation
The evaluation of the maintainability index is pretty simple, since it merely returns a single number. The only special part is that if a whole class is to be evaluated, we additionally use the standard deviation. If a class has a high standard deviation, the color tone will be rather dark, otherwise it will be light. 

Results explained:  
MI: <65 = bad, >85 = good, in between = moderate  
Standard deviation: >25 = high, <25 = low  
  
Detailed example:
| MI | Standard Deviation | Result                                 |
|----|--------------------|----------------------------------------|
| 90 | 23                 | Good Maintainability (Light green)     |
| 90 | 46                 | Good Maintainability (Dark green)      |
| 75 | 20                 | Moderate Maintainability (Dark yellow) |
| 75 | 30                 | Moderate Maintainability (Dark yellow) |
| 50 | 23                 | Low Maintainability (Light red)        |
| 50 | 28                 | Low Maintainability (Dark red)         |

## Side Notes
Since this is the very first version, there are some side notes to make.
* Omnis tends to have a slightly better MI than Java, for example. This applies especially to small methods like Getter and Setter
* When calculating the halstead volume the code doesn't work perfectly for values in quotes.  
  The following two statements for example should return the same volume. In our case however, the second one will have a higher volume.
~~~~
  1.  Calculate abc as 'Hello World' 
  2.  Calcualte abc as '(Hello World)'
~~~~
* The standard code that is automatically being generated behind an $event method of a new object does not count as code and does not influence the rating. For example if you create a new Pushbutton, it immediately comes with an $event method containing an onClick event. This would not be included!
* The McCabe Complexity can be inaccurate if your code contains a function that uses multiple parameters within a pick() function.  
  For example, the following statement would increase the complexity by one too many.
~~~~
  Calculate y as pick(pos('abc',x)=0,'abc','xyz')
~~~~
* <=,>=,<> count as one token.
* Commands like "Break to end of loop" that are not included in the switch statement of $mainHalstead are counted as one token.
