# In the Clutches of Matlab

Field Manar

rev 3, 2022-08-04

Matlab was [originally created][horsemouth] in the early 80s by Clive
Moler as an interactive calculator interface on top of Fortran
libraries that enabled their use without learning Fortran.  Clive was
not an expert on programming language design, and in fact in the linked
video he proclaims to have only just read [a book][wirth76] on the
subject before creating the first incarnation of Matlab. The 'language'
was actually just an interactive shell and not what anyone would
consider an actual programming language.  It lacked control structures
or functions in any form.  The program did not garner much enthusiam
except by engineering students who loved not learning to program.  In
the ensuing years Matlab has not strayed far from these roots. While it
has certainly added many new features, at its core it is still an
interactive wrapper on other libraries and not designed for general
purpose programming.

To be fair, it turns out that Matlab is great at being an immensely
powerful desk calculator.  If the problem you need to solve can be
accomplished in a single script of reasonable length (e.g. most data
processing), Matlab is fantastic.  For interactive problem exploration
Mablab provides an easy and capable solution that maps very nicely from
on-paper equations.  This original aim of Matlab is still its strongest
feature.  The problem is that this enables you to do just about
anything required of an engineering homework assignment. Matlab
therefore gets used by engineering students everywhere who then confuse
it with actual programming.  It seems engineers never change.

The problems arise when you try to use Matlab to write programs larger
than a single script.  It is quite obviously not designed for this
task, but can still manage to accomplish it with addition functions,
classes, and packages all tossed in shiny new files.  Having the barest
level of this capability furthers the illusion that it is a general
purpose language and Mathworks actively seeks to promote this idea by
adding language features (guis! tables! classes! simulink! symbolic
math! parallel processing! matfiles! GPUs! the list goes on).  Adding
features doesn't fix the core issues though, and in the mean time other
languages have caught up to Matlab in ease of use for scientific
computing while also being excellent platforms for full scale library
and software development.(And they are free and open source.)



## Matlab Pros

Here are the reasons people give for using matlab.

1. **Rapid Protoyping** Your manager likes Matlab because it enables
rapid prototyping of complex scientific programs.  Out-of-the-box
Matlab supports a fleshed out scientific computing environment: matrix
operations, special functions, plotting, numerical integration,
least-squares, eigenvalues, fft, csd, and file read-write.  Matlab
contains other stuff too, like debugging, profiling, a text editor with
linting, gui generation, and code publishing. I haven't even gotten to
all the packages.  All this stuff (mostly) just works without any extra
fiddling.

1. **Monolithic** Your IT department likes Matlab because it is a single
piece of software for them to vet, install, license, and get support
from.  This lowers their workload.

1. **Education** Your alma mater likes Matlab because its cheap to get
an educational license and the rapid prototyping angle means less time
teaching engineering students how to code.  In fact, Matlab was
explicitly created [for this reason][wiki].

1. **Optimized Operations** You might like Matlab because it's matrix
math, fft, and ode libraries just fly.  Further, vectorization is the
primary method of speeding up code and its relatively simple to
understand.  You have been misled about who is responsible for that
speed, Matlab actually just rides on free open-source libraries (e.g
[lapack], [blas], and [fftw])

1. **Good Documentation** The Matlab help system is easily accessible
offline and covers most things quite well.



## Matlab Is Entrenched

Unfortunatly Matlab is entrenched:

- Everyone already knows Matlab, so sharing code is easier.
- Everything is already written in Matlab, so interfacing with old work
  is easier.

There are at least three costs associated with switching:

- You must invest time to learn an alternative.  Often this results in
  learning multiple tools to replace various Matlab features.
- You must re-write existing code which no one likes to do and can break
  already working functionality.
- You must get the alternative working on your computer.  Depending on your IT
  system, this isn't trivial.

These barriers keep the Matlab train going.  I strongly believe that the
benefits of breaking out of the Matlab cage far outweigh the costs.  After all,
I'm the one writing this rant.



## Matlab Cons

Here are what I consider the main cons of Matlab.  I attempt to avoid being
nitpicky about specific functions.


### Not Free

Matlab costs money.  This is absoulety ridiculous.  The only other language
I can think of that isn't free is Mathematica.  Both are targeted at
engineers/mathematicians and make money on our laziness.  This isn't a problem
with the language itself, but adds endless licensing overhead.


### One Function One File

Only the topmost function in a file is visible, and furthermore it shares its
name with the file.  [Documentation: function][doc_function].

- **Organization**  Writing projects that are larger than a couple
  functions quickly becomes cumbersome from an organizational standpoint.
  This usually results in projects/libraries existing as a folder that
  contains a surfeit of files and no sub-folders. The library is used by
  adding the folder to the matlab path with `addpath('yourfolder');`.  This
  compounds [namespace cluttering](#namespace) because any folders you've
  added before remain on the path.  Resetting the path with
  `restoredefaultpath` is rarely used (and slow!), and it takes too long to
  restart [the mothership](#mothership).  Organizing your library with
  subfolders creates a longer include statement:
  `addpath(genpath('yourfolder'));` and at any rate doesn't give you the
  ability to _exclude_ folders.  Matlab has introduced (in some edition) the
  idea of "packages."  This helps, but is a tacked on feature that doesn't get
  used much in my experience.

- **Packages** Matlab introduced the concept of a package folder to help with
  code organization.  The only thing this actually does it prepend the folder
  name to the functions automatically, and it is not better than doing that
  yourself.  There is no concept of inside for a package, even when refering to
  other items in the same package. (Does this go in namespaces?)

- **Readability**  Function handles are spread across all the files
  and can't be browsed.  Similarly, it is impossible to scroll through the
  source of multiple related functions because each is its own file.  This
  makes it harder to get an overview of the library.



### Namespaces {#namespace}

Function name resolution is handled by managing the search path.  Matlab is a
shell, remember?

- **Name Resolution Is Hard to Track**  Since the search path is
  consistent between script runs its easy to loose track of what is on the
  path.  Heaven forbid you include two folders that have the same function in
  them and end up with weird errors because they are slightly different
  versions of each other.

- **Name Resolution Quietly Fails**  Have multiple function with the same name
  on the path will be quietly resolved by Matlab in the order their folders
  are included in the path.  Which one you get depends on the order you ran
  your scripts that day.  This is fine for shells, but theres a reason its not
  done in programming languages.  It makes function call non-deterministic.

- **Polution**  The default namespace contains _all_ included functions.
  Depending on how many packages you have, that's hundreds of landmines.  I
  mean, 'flow' is a default function, but how many times have you overwritten
  that with a variable?  [This is bad practice.][namespace_pollution];

- **Overwritting Is Standard**  Probably because of the search path resolution
  and pollution, Matlab allows/requires you to overwrite function and variable
  names at will.  Sometimes you mess up and i is still sqrt(-1) and not the
  loop index.

- **Function Call Overhead** The equivalence of files and functions mean that
  name resolution requires searching file paths.  This is a major source of
  overhead for function calls.  While 'just-in-time' compilation alleviates
  this, it will always be there.  This is exacerbated when classes get
  involved.

These mean that libraries must use some other form of name collision
avoidance, such as [prefixing](https://en.wikipedia.org/wiki/Name_collision).
These work, but by modern standards are considered archaic.  Matlab has added
namespace-like behavior with '+pkg' folders, but the above issues largely
remain.  For historical reasons, Matlab doesn't partake in packages with its own
libraries, prefering to add them all to the global namespace.  A decision once
again motivated by the drive for interactive use.


### The Mothership {#mothership}
You are required to use the Matlab IDE because Matlab is the IDE.  The roots
of Matlab as a program (rather than a language) are apparent here.

- **Startup Time** Matlab takes forever to start.  What exactly is is doing
  during that time?  I honestly don't know, because I haven't even told it
  what script to run yet.

- **Shunned Tool Ecosystem**  It is hard to use Matlab in any other form
  than as a full IDE.  Would you like a different editor?  Prepare to be
  hamstrung.  Want a fuzzy file finder?  Nope.  A build system?  Get out of
  town.  A real shell?  You are shackled to the command prompt.

- **Small Community** Matlabs cost automatically shrinks the community.  This
  directly impacts the availability and quality of commuity extensions.  There
  are certainly some gems in the File Exchange, but you aren't going to find
  open source projects as you would with e.g. C++, Python, or JavaScript.  This
  is relevant to the Mathworks provided toolboxes as well.  Mathworks is only
  one company and not a very large one at the end of the day.  They manage to
  keep up with the shiniest fad developments, but will never have the manpower
  of many communities working together.

- **No Command Line**  Matlab really doesn't want to run from the command line.
  It can be, but your command has to be a monster like:

  `$ matlab -nosplash -nodesktop -r 'try; ScriptToRun; catch; end;quit();'`

  If that doesn't discourage command line use, I don't know what will.  This has
  gotten better recently with the advent of the `--batch` option, which
  accomplished the same as the above.  The shortcoming remains that you are
  forced to enter a matlab command prompt rather than specify a script to run
  and you cannot pass command line argument to that script as you would in any
  other language.


### A Semi-Procedural Language

Matlab tries to be a procedural programming language.  But Matlab also has
classes.  However because they were an additional feature their use is spotty in
Matlab's own libraries and the procedural paradigm persists.

  - **No Containers** It is fairly common now for languages to come packaged
    with a standard library that included basic container types (e.g. stack,
    queue, dictionary).  These containers are the foundation of many
    fundamental algorithms; but they are absent from Matlab.  We have cell
    arrays, and more recently string arrays but similarly these are late
    additions that don't quite integrate.  Why do I need `{}` instead of `()`
    to get values out of a cell array?

  - **No References** Matlab overarching paradigm is to always be call-by-value.
    Depending on your point of view, this could [be a strength](#byvalue).  But
    the fact is that indirection is necessary for dealing efficiently with large
    data or implementing graph/list algorithms.  To remedy this the `handle`
    class was added, but its a limited hack substitute for actual indirection
    and at any rate can only be used when you make your own class.

  - **No Iterators** Related to the lack of containers above, there is no
    support for iterating over lists or other collections outside of
    `for i = 1:length(thing)`.

  - **No Defaults** Its just syntactic sugar, but allowing default values to be
    put in the function signature and then substituted by the language is nice
    to have.  Its also more efficient than using the Matlab idiom:

    `if !exists('parameter', 'var'); parameter = default; end;`

  - **Class Clash**  Matlab is a dynamic [duck typed][duck] affair almost all
    the time.  Structures have fields dynamically added and deleted and type can
    change at will.  The glaring exception is the class system, where class
    members and function are rigidly defined in the class file.  Matlab also
    has a suspiciouly full set of access attributes for a mostly non-object
    oriented language.  They really threw in the kitchen sink.  It's not bad,
    per se, but it feels discontinuous with the rest of the language.


### Syntax Quibbles

Ok, so admittedly these are nitpicky.

1. The matrix transpose operator `'` naturally actually performs the
conjugate transpose.  The regular transpose is `.'` which doesn't fit
with the other matrix operators, whose dot-prefixed form operates
elementwise.

2. Ommitting the `;` prints output.  I can think of no clearer
indication that Matlab is supposed to be a calculator rather than a
programming language.  No one in their right mind would add 'print
result' as a primary side effect of every single line of code in a
non-interactive setting.

3. Arrays are indexed from 1.  Most people complain that they forget
when switching back and forth.  Thats true, but the real cost comes
when doing math on matrix indices.  It makes the `mod()` operator a
nightmare and you end up with `(i-1)` everywhere.

4. Matlab uses parentheses for indexing.  This makes it harder to tell an
indexing operation from a function call or grouping in an expression.

5. Matlab has no concept of a 1D array.  You might think your array is
1D, but its not.  It is either a row or column vector; a natural choice
for a MATrix LABoratory.  A really shitty choice for everything else.
This leads to untold hours debugging stupid problems when you are
passed a 1D array in the wrong shape.


## Bad Habits

You might think these cons don't affect you, but they do!  They subtly
encourage bad coding habits that make it harder to write and maintain code and
therefore end up wasting our time and making us unhappy.  WIP.

Can we talk about the scourge of the orange squiggle?  Why am I constantly
being pestered with dubious tips about how to make my code faster?

<!-- The [one function one file](#onefunction) and [function overhead](#overhead)
encourage writing few long functions in contradiction of acknowledged [best
practices][short_functions]. -->


## Alternatives

Matlab isn't the only option.  Here's how I propose to fix the cons while
supplying alternatives to the lost pros. WIP.  (Python!  No but really.)

<!-- ## My Pedigree
Not to toot my own horn, but here is my programming background.  I started
coding when I rented "C for Dummies" from the library in 5th grade so I could
make the computer games my parents would buy for me.  Since then I have used
C, C++, Java, Tcl, and Python but by far and away I have spent the most time
with Matlab.  I have used Matlab pretty much every working day since I started
undergrad in 2008, so there may be an element of "familiarity breeding
contempt" to all this.  I have also never participated in a large coding
project though I at least attempt to espouse CS best practices and knowledge.

Matlab is taught in college, its quick and dirty so kids don't learn common
programming practices, switching is extra hard because they must learn the new
system and how to program. -->

## Advert

This document generated with [pandoc](https://pandoc.org/) following the
[commonmark](https://commonmark.org/) spec.  Its great!  Join me in a
nerdist document utopia!


[doc_function]: https://www.mathworks.com/help/matlab/ref/function.html?searchHighlight=function&s_tid=doc_srchtitle
[doc_path]: https://www.mathworks.com/help/matlab/matlab_env/what-is-the-matlab-search-path.html
[google_short_functions]: https://google.github.io/styleguide/cppguide.html#Write_Short_Functions
[namespace_pollution]: https://softwareengineering.stackexchange.com/questions/236404/what-is-using-namespace-pollution
[wirth76]: https://www.amazon.com/Algorithms-Structures-Prentice-Hall-Automatic-Computation/dp/0130224189#customerReviews
[wiki]: https://en.wikipedia.org/wiki/MATLAB
[horsemouth]: https://www.mathworks.com/videos/origins-of-matlab-70332.html
[byvalue]: https://www.mathworks.com/matlabcentral/answers/15533-object-oriented-programming-performance-comparison-handle-class-vs-value-class-vs-no-oop#answer_21101
[lapack]: http://www.netlib.org/lapack/
[blas]: http://www.netlib.org/blas/
[fftw]: https://www.fftw.org/
[duck]: https://en.wikipedia.org/wiki/Duck_typing

<!-- pandoc -f markdown+header_attributes -t html5 -o rant.html rant.md -->
<!-- pandoc -f markdown+header_attributes -t context -o rant.pdf rant.md -V colorlinks=true  -V linkcolor=blue -->
