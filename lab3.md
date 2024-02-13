# Bugs and the Find Command
In the following blogpost, you will learn how to identify bugs in amateur Java code and thoroughly learn about the utility of the `find` command.

Etiher section can be viewed in any order so feel free to skip to the part of your interest:
* Part 1: Bugs in Java
* Part 2: The `find` Command

---

# Part 1: Bugs in Java
Consider the following class with the static void method `reversed(int[] arr)` which returns a new array with the reverse values of an inputted array.
```
public class ArrayExamples {
  static int[] reversed(int[] arr) {
      int[] newArray = new int[arr.length];
      for(int i = 0; i < arr.length; i += 1) {
        arr[i] = newArray[arr.length - i - 1];
      }
      return arr;
  }
}
```
At first glance, the method looks like it could work.  Instead of analyzing the code line-by-line to ensure it works 100%, 
let's create a test to identify what the symptoms of the errors are.  For a method like this, a test should include an array 
with at least 2 values to see if it properly reverses the values.  We will be using the syntax according to the JUnit tester.
Test methods in JUnit are usually named like "test(Method being tested)(Description of how it is being tested)."
```
import static org.junit.Assert.*;
import org.junit.*;

public class ArrayTests {
  @Test
  public void testReverseInPlaceOneToTen() {
    int[] oneToTen = { 1,2,3,4,5,6,7,8,9,10 };
    ArrayExamples.reverseInPlace(oneToTen);
    assertArrayEquals(new int[]{ 10,9,8,7,6,5,4,3,2,1 }, oneToTen);
  }
}
```
This class contains a JUnit tester which creates an array `oneToTen` of ints 1-10 and processes it through the `reversed` method.  The last line returns a successful pass
of the test if the expected array of ints 10-1 is equal to the `oneToTen` array we reversed.  After running the JUnit test, it returns the following error message:

![Failed reversed() method](https://github.com/nericguyen/bugs-and-commands/assets/149546505/c130317e-1674-427a-8fee-9d9b8c69e090)

Instead of the element at index 0 being 10, it was found to be 0.  0 was not in the original array, so the bug is likely to be in either how the for loop was iterating through
the inputted array, or how the for loop was copying elements into the new array to be returned.  So are there any arrays in which there isn't an error-induced input?
```
public class ArrayTests {
  @Test
  public void testReversed() {
    int[] input1 = { };
    assertArrayEquals(new int[]{ }, ArrayExamples.reversed(input1));
  }
}
```
It appears so! An empty array reversed by the method still returns an empty array... so yippee!  JUnit indicates a passed test with a green check on the left of the test and
a red X is indicated on the left of all failed tests.

![Passed reversed() method](https://github.com/nericguyen/bugs-and-commands/assets/149546505/9a9d98b8-3b86-408f-a2d3-43ec9ffb30ad)

These two test should cover the scope of the usage for this method, so no further tests will be necessary.  Now let's analyze the code to identify and eliminate this bug.
In case you don't want to scroll up again to see the code, here is the original method:
```
public class ArrayExamples {
  static int[] reversed(int[] arr) {
      int[] newArray = new int[arr.length];
      for(int i = 0; i < arr.length; i += 1) {
        arr[i] = newArray[arr.length - i - 1];
      }
      return arr;
  }
}
```
Looking deeper, we can see that we create an array called `newArray` with a length of the original array.  The for loop properly iterates through the whole array,
but the problem is within the for loop.  It replaces the original values in the original array with the empty values of `newArray`.  We want the opposite of this.
We want to add the values of `arr` into the `newArray` backwards.  We also want to return `newArray`.
```
public class ArrayExamples {
  static int[] reversed(int[] arr) {
      int[] newArray = new int[arr.length];
      for(int i = 0; i < arr.length; i += 1) {
        newArray[i] = arr[arr.length - i - 1];
      }
      return newArray;
  }
}
```
Here is our fixed code!  As you can see, the fix was simnply to switch the names in a few lines.  The for loop iterates from the end of the original array and adds
it to the elements from the beginning of the new array.  Finally, it returns the new array.  In conclusion... the JUnit tester does its job.  It can run examples
and identify conflicts between expected and actual values, and solidifies that your code works for many scenarios.

# Part 2: The Find Command
Commands have many command-line options that expand the utility of the command.  You can find whole libraries of commands and their properties, but today, we will
only be going over 4 useful command-line options for the `find` command.  You can imagine `find` as a more refined version of the `ls` command, in that it outputs
files and directories within specific paths.  For the following examples, we will be referencing files within the directory `./technical`.  You can
clone the directory from the link below:

https://github.com/ucsd-cse15l-s23/docsearch.git

## 1. `find <path> -type d`
The `-type d` command-line option prints only directories, hence the d.  The find command not only prints the directories in a given path, but all the nested
directories as well.  For example, with the working directory as ~/GitHub/docsearchCSE15L/technical/, the command gives the following output:
```
neric_guyen@Erics-MacBook-Pro-2 technical % find . -type d
.
./government
./government/About_LSC
./government/Env_Prot_Agen
./government/Alcohol_Problems
./government/Gen_Account_Office
./government/Post_Rate_Comm
./government/Media
./plos
./biomed
./911report
```
For clarification, the `.` in the input refers to the current directory.  As you can see, it outputs all of the directories within ~/GitHub/docsearchCSE15L/technical/ and
any directories within those.  It even includes the current working directory as a `.`.  Let's try to use a sister directory to the current working directory as an argument
for the path of the command:
```
neric_guyen@Erics-MacBook-Pro-2 technical % find lib -type d       
find: lib: No such file or directory
```
~/GitHub/docsearchCSE15L/technical/ and ~/GitHub/docsearchCSE15L/lib/ are both contained in the /docsearchCSE15L directory, but they cannot directly access eachother.
Since there is no lib folder contained within /technical either, it returns that it cannot be found.  One way to fix that is by using `../` which sends the current working
directly "up" to /docsearchCSE15L and can then access /lib.
```
neric_guyen@Erics-MacBook-Pro-2 technical % find ../lib -type d
../lib
```
/lib is a directory so it is printed, and then there are no other directories in /lib.

*side note, -type f is also a very useful command-line argument wchich searches for files*

## 2. `find <path> -maxdepth <int>`
The amount of files and directories that the `find` command can print might be excessive, especially if you have a lot of nested directories.  This command-line
option allows you to limit the depth of the search within the directories.  Let's say that our working directory is ~/GitHub/docsearchCSE15L, then we type the following:
```
neric_guyen@Erics-MacBook-Pro-2 docsearchCSE15L % find . -maxdepth 1 -type d
.
./lib
./.git
./technical
```
Instead of ALL the directories getting printed, we only see the directories 1 path deep within /docsearchCSE15L.  This is more akin to the `ls` command.  In
conjunction with the `-type d` command-line option, this becomes a very powerful tool for keeping track of your directories.  Let's do another example:
```
neric_guyen@Erics-MacBook-Pro-2 docsearchCSE15L % find . -maxdepth 2 -type d 
.
./lib
./.git
./.git/objects
./.git/info
./.git/logs
./.git/hooks
./.git/refs
./.git/branches
./technical
./technical/government
./technical/plos
./technical/biomed
./technical/911report
```
Using an argument of 2 shows directories 2 paths deep, ie any directories within the directories within the working directory.  Notice how it still includes 
the relative paths to the directories 1 path deep, such as ./lib.

## 3. `find <path> -size <+/- int(M/k/c)>`
This command-line argument finds and prints the files that have more or less than a specified size.  The plus sign indicates more than, the minus sign is less than.
M is MB, K is KB, and c is in bytes. This example prints all the files in /technical that have a size greater than 100 KB of data.
```
neric_guyen@Erics-MacBook-Pro-2 technical % find . -size +100k
./government/About_LSC/commission_report.txt
./government/About_LSC/State_Planning_Report.txt
./government/Env_Prot_Agen/multi102902.txt
./government/Env_Prot_Agen/ctm4-10.txt
./government/Env_Prot_Agen/bill.txt
./government/Env_Prot_Agen/tech_adden.txt
./government/Gen_Account_Office/d0269g.txt
./government/Gen_Account_Office/GovernmentAuditingStandards_yb2002ed.txt
./government/Gen_Account_Office/Sept27-2002_d02966.txt
./government/Gen_Account_Office/d01376g.txt
./government/Gen_Account_Office/Statements_Feb28-1997_volume.txt
./government/Gen_Account_Office/pe1019.txt
./government/Gen_Account_Office/gg96118.txt
./government/Gen_Account_Office/d01591sp.txt
./government/Gen_Account_Office/im814.txt
./government/Gen_Account_Office/ai9868.txt
./government/Gen_Account_Office/May1998_ai98068.txt
./government/Gen_Account_Office/d02701.txt
./biomed/1471-2105-3-2.txt
./911report/chapter-13.4.txt
./911report/chapter-13.5.txt
./911report/chapter-13.2.txt
./911report/chapter-13.3.txt
./911report/chapter-3.txt
./911report/chapter-1.txt
./911report/chapter-6.txt
./911report/chapter-7.txt
./911report/chapter-9.txt
./911report/chapter-12.txt
```
This example prints all the files in /technical that have a size less than 1 KB of data.
```
neric_guyen@Erics-MacBook-Pro-2 technical % find . -size -1k  
.
./government
./government/About_LSC
./government/Env_Prot_Agen
./government/Alcohol_Problems
./government/Post_Rate_Comm
./plos/pmed.0020191.txt
./plos/pmed.0020226.txt
./911report
```

## 4. `find <path> -name "*<substring>*"`
This command-line option prints all relative paths to files or directories containing the substring you input in case you can't quite remember the full name of a file
or directory.  For example, let's say I forget how to spell the word government:
```
neric_guyen@Erics-MacBook-Pro-2 technical % find . -name "*govern*" 
./government
```
The command finds all the files and directories containing the substring "govern."  Let's do a broader example to see how the output usually is:
```
neric_guyen@Erics-MacBook-Pro-2 technical % find . -name "*go*"    
./government
./government/Media/Oregon_Poor.txt
./government/Media/Ginny_Kilgore.txt
```
The files Oregon_Poor.txt and Ginny_Kilgore.txt both contain the substring "go."  Other relative paths like ./government/Post_Rate_Comm technically contain the 
substring "go" in the /government directory, but the final path /Post_Rate_Comm does not contain the substring so it is not printed.

# Conclusion
Thank you for listening to my bloggy!
I used the following websites as a reference since I did not know anything about the `find` command prior to writing this blogpost:
https://www.redhat.com/sysadmin/linux-find-command
https://tecadmin.net/linux-find-command-with-examples/
Great websites 10/10
