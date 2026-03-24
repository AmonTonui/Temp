**SENG 637 - Dependability and Reliability of Software Systems**

**Lab. Report \#4 – Mutation Testing and Web App Testing**

| Group \#:      |     |
| -------------- | --- |
| Student Names: |     |
|                |     |
|                |     |
|                |     |

# Introduction
In this assignment, we explore Mutation Testing using Pitest Eclipse Plugin to evaluate effectiveness of our existing test suite at detecting faults in source code. After analyzing the initial Mutation results, we improved test suite by designing & adding new test cases, increasing mutation score by at least 10%. This process helps us better understand strengths & weaknesses of our current tests and highlights the importance of writing test cases that can detect subtle behavioral changes in the program. 
### In another part of assignment,........................................................................................................................................................
# Analysis of 10 Mutants of the Range class 
**:small_blue_diamond:M1 {Line No. 161}** <br>
Mutation applied by Pitest was `Incremented (a++) double local variable number 3 -> SURVIVED` on method `intersects(double, double)`. This mutation was applied to line of code below

   ```java
   return (b0 < this.upper && b1 >= b0);
   ```

This mutation applies post-increment to local variable that's used only once in return expression. Even after mutation, comparison still uses original value of operand at time Boolean expression is evaluated. Increment happens only after original value has already been used & updated value isn't reused anywhere else in statement. Therefore, return value of method remains unchanged. In practical terms, behavior observed by test suite is identical before & after mutation. That's why mutant survives & it behaves like an equivalent mutant. Since equivalent mutants don't change semantic meaning of code, they can't be killed by additional black-box test inputs.

**:small_blue_diamond:M2 {Line No. 161}** <br>
Mutation applied by Pitest was `Decremented (a--) double local variable number 3 -> SURVIVED` on method `intersects(double, double)`. This mutation was applied to below line of code

   ```java
   return (b0 < this.upper && b1 >= b0);
   ```

This mutation is similar to previous one, except that it uses post-decrement instead of post-increment. Same reasoning applies here. Because value of variable is consumed in expression before decrement takes effect, Boolean outcome of return statement doesn't change. As a result, tests observe no behavioral difference between original program & mutated program. This is why mutant survives & it's best interpreted as an equivalent mutant rather than gap in test suite.

**:small_blue_diamond:M3 {Line No. 305}** <br>
Mutation applied by Pitest was `changed conditional boundary -> SURVIVED` on method `expandToInclude(Range, double)`. This mutation was applied to below line of code

   ```java
   if (value < range.getLowerBound()) {
   ```

According to Pitest mutation operator, this mutation changes condition `value < range.getLowerBound()` to `value <= range.getLowerBound()`. At 1st glance, this looks important because mutated version allows equality boundary to enter `if` branch. However, when `value` is exactly equal to `range.getLowerBound()`, both original & mutated implementations produce range with same lower & upper bound. In original implementation, method returns original range object. In mutated implementation, it may construct new `Range(value, range.getUpperBound())`. Even though execution path is different, returned interval is semantically identical. Therefore, externally observable behavior remains same, so this mutant survives & behaves like an equivalent mutant.

**:small_blue_diamond:M4 {Line No. 305}** <br>
Mutation applied by Pitest was `removed conditional - replaced comparison check with false -> KILLED` on method `expandToInclude(Range, double)`. This mutation was applied to below line of code

   ```java
   if (value < range.getLowerBound()) {
   ```

This mutation forces condition to evaluate to `false`, which means lower-bound expansion branch can never execute. This is real behavioral change because values smaller than current lower bound should cause returned range to expand. A boundary-focused test that supplies a value strictly below existing lower bound kills this mutant. In original code, lower bound is updated correctly. In mutated version, original range is returned unchanged. Because observed output no longer matches expected expanded range, test fails on mutant & Pitest reports it as killed.

**:small_blue_diamond:M5 {Line No. 161}** <br>
Mutation applied by Pitest was `changed conditional boundary -> KILLED` on method `intersects(double, double)`. This mutation was applied to below line of code

   ```java
   return (b0 < this.upper && b1 >= b0);
   ```

This mutation changes 1st relational check from `b0 < this.upper` to `b0 <= this.upper`. That change affects exact upper-bound boundary case. In original implementation, when `b0` is exactly equal to `this.upper`, result should be `false` because candidate interval only touches range at upper endpoint & doesn't overlap in intended sense. Dedicated boundary test that sets `b0` equal to upper bound detects this difference immediately. Original code returns `false`, whereas mutated code returns `true`. Because assertion distinguishes those II behaviors, this mutant is killed.

**:small_blue_diamond:M6 {Line No. 365}** <br>
Mutation applied by Pitest was `removed call to org/jfree/chart/util/ParamChecks::nullNotPermitted -> KILLED` on method `shift(Range, double, boolean)`. This mutation was applied to below line of code

   ```java
   ParamChecks.nullNotPermitted(base, "base");
   ```

This mutation remove defensive null check for input parameter `base`. In original implementation, null input is rejected immediately, which is intended API behavior. When that call is removed, method no longer preserves same contract for invalid input. Null-input exception test kills this mutant. Original code throws expected `IllegalArgumentException`, while mutated version either proceeds incorrectly/fails in different way later in execution. Because expected exception behavior is part of specification, mutant is killed.

**:small_blue_diamond:M7 {Line No. 448}** <br>
Mutation applied by Pitest was `replaced boolean return with false for org/jfree/data/Range::isNaNRange -> KILLED` on method `isNaNRange()`. This mutation was applied to below line of code

   ```java
   return Double.isNaN(this.lower) && Double.isNaN(this.upper);
   ```

This mutation sets entire return value to `false`. That's strong mutation because it undermines method's core purpose. Method is supposed to report whether both bounds are `NaN` & forcing result to always be false completely breaks that behavior. Test that constructs range whose lower & upper bounds are both `Double.NaN` kills this mutant. Original method returns `true`, while mutated method returns `false`. Since test asserts intended behavior of method, mutant is killed.

**:small_blue_diamond:M8 {Line No. 448}** <br>
Mutation applied by Pitest was `Negated double field lower -> SURVIVED` on method `isNaNRange()`. This mutation was applied to below line of code

   ```java
   return Double.isNaN(this.lower) && Double.isNaN(this.upper);
   ```

This mutation negates `this.lower` before it's passed into `Double.isNaN()`. However, negating double doesn't change whether it's `NaN`. If original value is an ordinary numeric value, both `x` & `-x` are still non-NaN. If original value is `NaN`, then negating it still yields `NaN`. Therefore, truth value of `Double.isNaN(this.lower)` remains unchanged for all possible inputs. Since method's overall Boolean result remains same, this mutant survives & should be classified as equivalent.

**:small_blue_diamond:M9 {Line No. 241}** <br>
Mutation applied by Pitest was `negated conditional -> KILLED` on method `combineIgnoringNaN(Range, Range)`. This mutation was applied to below line of code

   ```java
   if (range1 == null) {
   ```

This mutation negates null-check condition. So, method enters wrong branch whenever `range1` is non-null, which changes control flow & ultimately changes returned value. Test case using II valid, non-null ranges kills this mutant. In original code, method combines ranges correctly. In mutated code, negated condition disrupts that logic & produces wrong outcome. Because expected combined range isn't returned, test detects fault & mutant is killed.

**:small_blue_diamond:M10 {Line No. 248}** <br>
Mutation applied by Pitest was `removed conditional - replaced equality check with false -> KILLED` on method `combineIgnoringNaN(Range, Range)`. This mutation was applied to below line of code<br>

   ```java
    if (range1.isNaNRange()) {
   ```

This mutation forces condition to `false`, which means method no longer handles special case where 1st range is NaN range. That special-case logic is important because `combineIgnoringNaN()` is designed to ignore NaN ranges when determining the result. Test case where 1st range is NaN range & 2nd input is null kills this mutant. In original implementation, method should return `null`. In mutated version, NaN-handling branch is skipped, so method returns an incorrect value. Mismatch between expected & actual output kills mutant.
# Report all the statistics and the mutation score for each test class
After fixing failing assignment_3 tests & rerunning project in Eclipse with Pitest, we measured mutation results for both `Range` & `DataUtilities`. After that, we added new tests & reran Pitest again to determine whether mutation scores improved. There're ii different views that appear in Pitest HTML report:- <br>
:small_orange_diamond:Class-level View for specific class under test<br>
:small_orange_diamond:Package-level View for `org.jfree.data`, which also includes many other classes that we didn't explicitly target in Assignment 3 <br>

Because package-level report includes additional classes, package-level mutation coverage is much lower than focused class-level score. Therefore, both views are useful, but class-level view is more accurate measure of how well our target test suites performed.
### :mag_right: <ins> Official PIT Class-level Results </ins> :mag:

### :page_with_curl: Mutation Terminal, Statistics & Score of Range = $\color{red}{\text{Before}}$
![](images/Range_Mutant_Terminal_Before.png)
![](images/Range_Mutant_Statistics_Before.png)
![](images/Range_Mutant_Score_Before.png)
:memo:
| Name | Line Coverage | Mutation Coverage | Test Strength |
|------|---------------|-------------------|---------------|
| `Range.java` | 98% `101/103` | 68% `850/1259` | 68% `850/1245` |

### :page_with_curl: Mutation Terminal, Statistics & Score of Range = $\color{green}{\text{After}}$
![](images/Range_Mutant_Terminal_After.png)
![](images/Range_Mutant_Statistics_After.png)
![](images/Range_Mutant_Score_After.png)
:memo:
| Name | Line Coverage | Mutation Coverage | Test Strength |
|------|---------------|-------------------|---------------|
| `Range.java` | 100% `103/103` | 78% `983/1259` | 78% `983/1259` |

### :page_with_curl: Mutation Terminal, Statistics & score of DataUtilities = $\color{red}{\text{Before}}$
![](images/DataUtilities_Mutant_Terminal_Before.png)
![](images/DataUtilities_Mutant_Statistics_Before.png)
![](images/DataUtilities_Mutant_Score_Before.png)
:memo:
| Name | Line Coverage | Mutation Coverage | Test Strength |
|------|---------------|-------------------|---------------|
| `DataUtilities.java` | 99% `79/80` | 83% `568/687` | 84% `568/680` |

### :page_with_curl: Mutation Terminal, Statistics & Score of DataUtilities = $\color{green}{\text{After}}$
![](images/DataUtilities_Mutant_Terminal_After.png)
![](images/DataUtilities_Mutant_Statistics_After.png)
![](images/DataUtilities_Mutant_Score_After.png):memo:
| Name | Line Coverage | Mutation Coverage | Test Strength |
|------|---------------|-------------------|---------------|
| `DataUtilities.java` | 99% `79/80` | 93% `639/687` | 93% `639/687` |

As a result, focused class-level results show actual gain in mutation effectiveness much more clearly. This means `Range` test suite improved by 10% points & `DataUtilities` test suite improved by 10% points as well. Therefore, objective of increasing mutation score by at least 10% for both classes was achieved!!
# Analysis drawn on the effectiveness of each of the test classes
Based on our analysis, test suites developed in previous assignments were already reasonably strong. They covered many normal cases, boundary cases & invalid-input scenarios. However, mutation testing showed that traditional line coverage was insufficient to ensure that assertions were strong enough to detect subtle faults.

**:clipboard: Effectiveness of `Range` Test Class** <br>
Original `Range` test suite already exercised several important methods, including `intersects()`, `expandToInclude()`, `shift()` & `combineIgnoringNaN()`. This is reflected in high line coverage of `Range.java` even before more tests were added. However, mutation score of 68% showed that some behaviors were still weakly distinguished, especially around null validation, exact boundary values, NaN behavior & methods that hadn't been covered at all. After adding more targeted tests, mutation score increased to 78%. This indicates that additional tests were effective, especially because they focused on situations where original suite didn't fully distinguish between correct behavior & mutated behavior. Most useful added tests were ones that verified exception handling, exact endpoint behavior, NaN-specific logic & additional methods outside original assignment scope.

**:clipboard: Effectiveness of `DataUtilities` Test Class** <br>
Original `DataUtilities` test suite was stronger than `Range` suite in terms of mutation performance. Even before expansion, it achieved 83% mutation coverage for target class. This suggests that original tests already checked many realistic scenarios with good assertions. Even so, mutation testing still exposed weaknesses, especially in overloaded methods, null-handling cases, ragged arrays & a few behaviors related to additional classes such as `DefaultKeyedValues` & `DefaultKeyedValues2D`. After adding more tests, score improved to 93%, which shows that suite became even more effective at distinguishing real faults from correct behavior.

At the end of the day, key lesson is that both test suites were useful, but neither suite was perfect before mutation testing was performed. Mutation analysis helped identify precise places where tests needed to become more discriminating. In other words, mutation testing didn't replace unit testing; rather, it strengthened it by showing where existing unit tests were still weak.
# A discussion on the effect of equivalent mutants on mutation score accuracy
By definition, equivalent mutants are mutants that are syntactically different but semantically equivalent to original program. Therefore, equivalent mutants aren't simulating real faults in system under test, so they can't be killed by test cases. Since these mutants can't be killed, yet are still counted in mutation report, they reduce apparent mutation score & therefore lower accuracy of mutation score as a pure measurement of real test quality. In our work, several surviving mutants closely resembled their counterparts. The clearest examples were post-increment & post-decrement mutations applied to local variables used only once inside return expressions. Even though syntax changed, return value remained same because mutated variable's value wasn't reused after the increment/decrement. Another example was negation of numeric field before calling `Double.isNaN()`. Negating value doesn't affect whether it's NaN, so Boolean result of `Double.isNaN()` remains unchanged. Therefore, these kinds of mutants survive not because tests are weak, but because program's behavior remains same. Equivalent mutants make it more difficult to interpret mutation results. Test suite may already be strong, yet final score may remain lower than expected because some surviving mutants simply can't be killed by any meaningful test case. For that reason, mutation score should always be interpreted together with the mutation log, not in isolation.
# A discussion of what could have been done to improve the mutation score of the test suites
For this assignment, main objective was to improve Mutation score of test suites for `Range` & `DataUtilities`. One obvious way to improve score was to add more tests for missing edge cases in methods already targeted in Assignment - 3. That's exactly what we did & it produced measurable improvement. However, Pitest reports also showed that both classes contain additional methods that weren't part of original test scope. Therefore, another way to further improve score would be to add dedicated tests for more methods within same classes. This is especially important because class-level mutation score includes all methods within class, not only those originally selected for manual unit testing. For `Range`, further improvement could come from testing more uncovered methods beyond original focus area. For `DataUtilities`, further gains could come from testing additional supporting classes & broader variations of array-based input. At the same time, many of the remaining survivors appear to be equivalent mutants. Therefore, even with greater testing effort, improvement would likely slow over time. Once non-equivalent mutants are mostly killed, remaining survivors no longer indicate missing test quality directly.
# Why do we need mutation testing? Advantages and disadvantages of mutation testing
Mutation Testing is required to evaluate effectiveness of test suite. It's process for determining whether test suite can detect injected faults in source code. Normal coverage report can show that line of code was executed, but it can't show whether test would fail if that code behaved incorrectly. Mutation Testing helps answer that question by creating faulty versions of program & checking whether tests can detect them.

**Advantages:** <br>
:arrow_right: Mutation Testing reveals weaknesses that ordinary line coverage can't expose<br>
:arrow_right: It helps identify missing edge cases & weak assertions<br>
:arrow_right: It increases confidence that tests are checking correctness, not just execution<br>
:arrow_right: It improves both quality of source code & quality of test suite<br>
:arrow_right: It provides measurable evidence of how effective tests really are

**Disadvantages:** <br>
:arrow_right: Mutation Testing is time-consuming & computationally expensive<br>
:arrow_right: It produces equivalent mutants that may survive even when test suite is correct<br>
:arrow_right: It can be difficult to interpret when class under test contains many uncovered methods<br>
:arrow_right: It requires repeated runs, detailed inspection & careful manual analysis<br>
:arrow_right: It's harder to configure than ordinary JUnit execution, especially in IDE environments
# Explain your SELENUIM test case design process

# Explain the use of assertions and checkpoints

# how did you test each functionaity with different test data

# How the team work/effort was divided and managed

# Difficulties encountered, challenges overcome, and lessons learned
There were some issues while setting up Pitest in Eclipse. During configuration, compatibility between Eclipse version, Pitclipse plug-in & Java runtime had to be handled carefully. In particular, Java 8 had to be used for Mutation runs to avoid compatibility problems.
![P1](images/eclipse_error.png)
![P2](images/eclipse-error.png)
Another challenge was that Mutation Testing requires all unit tests to pass before Mutation run can begin. This meant that some failing/outdated Assignment-03 tests had to be fixed 1st before Pitest could be used meaningfully. Apart from this, it was difficult at first to understand why package-level Mutation score looked much lower than expected. Later, we realized that Pitest was including many other classes under `org.jfree.data`, even though our tests were mainly focused on `Range` & `DataUtilities`...this was an important lesson in interpreting mutation reports correctly. Additionally, major lesson learned from this assignment-04 was that line coverage alone isn't enough; test suite may execute large % of code & still miss important faults. Mutation testing exposed several such weaknesses clearly. Another important lesson learned was that carefully designed edge-case tests are more effective than simply adding many generic tests. In our work, tests involving null inputs, NaN values, exact boundaries & invalid selections produced most meaningful Mutation score improvement. Last but not least, we also learned that surviving mutants shouldn't automatically be treated as evidence of poor testing. Some of them must be inspected manually, because they may turn out to be equivalent mutants.

### 2nd Part................................................................................................................................................................................................
# Comments/feedback on the assignment itself
This assignment gave us a chance to improve quality of our test suite using Mutation Testing rather than relying only on ordinary code coverage. However, assignment workflow was useful because it connected test design, automated tool support & manual reasoning about mutants. In fact, most valuable part of assignment-4 was seeing exactly how Mutation Testing exposes weaknesses that would otherwise remain hidden. Moreover, assignment also showed that reporting & analysis are just as important as writing tests themselves, because Mutation results need interpretation, not just execution. Thus, this part of assignment - 4 was a practical way to understand how Mutation Testing can be used to assess & improve effectiveness of *JUnit* test suite.
### On the other hand,.............................................................................................................................................................................
