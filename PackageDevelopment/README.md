# Package development

Packages are central to R development. 

In this article, a trivial example package is created.

# Create a new package

Start R Studio.

![R Studio](Rstudio.png)

Choose `File | New Project`

![](FileNewProject.png)

Choose `New Directory`

![](FileNewProjectNewDirectory.png)

Choose `R Package`

![](FileNewProjectNewDirectoryRpackage.png)

Name the package, e.g. `magicr` and click 'Create Project'

![](FileNewProjectNewDirectoryRpackageCreateProject.png)

You just created your first package!

Here is how it looks like:

![](magicr.png)

As you can see, there have been some folders and files created.
To file you are looking at now is called `hello.R` and is in 
the `R` (the naming is not very creative here) folder. You can
see that there is already put a function there, called `hello`.

Creating such default example code is great for beginners to 
get started. In this article, however, we will soon replace everything :sunglasses:.

Additional files created are:

 * a `.RBuildignore` file, that tells the package which files to ignore. A good example of an ignored file is `README.md`, which
   is the front page of your package its GitHub, but not used by the package itself
 * a `DESCRIPTION` file, that summarizes your package
 * a `NAMESPACE` file, that, among other, contains all your package its functions that are exported
 * a folder called `man`, with the file `man/hello.Rd`, that contains the manual/documation of your package.

In this article, we will:
 
 * Write the `DESCRIPTION` file
 * Write a function
 * Create a vignette
 * Create a test

## Write the `DESCRIPTION` file

The `DESCRIPTION` file summarizes your package. It contains the name, description, dependencies
and many more options.

The `DESCRIPTION` file has been default-created by RStudio and looks like this:

![](Description.png)

As you can see, the file contains hints how to create a working `DESCRIPTION` file.

To test our package now, we press CTRL-SHIFT-E or click 'Check' at the 'Build' tab at the top right:

![](PackageCheckFirst.png)

Our default `DESCRIPTION` file will give errors, alongside helful suggestions to fix these.
I suggest to simply replace the code by this:

```
Package: magicr
Type: Package
Title: This Package Does Magic
Version: 0.1.0
Author: richel@richelbilderbeek.nl
Maintainer: Richel Bilderbeek <richel@richelbilderbeek.nl>
Description: This package does magic. Doing magic, in detail,
    is multiplying all values by two, except 42, which should remain 42.
    Doing magic includes checking if the input is a numeric.
License: GPL-3
LazyData: TRUE
RoxygenNote: 5.0.1
```

When we now press CTRL-SHIFT-E or click 'Check' at the 'Build' tab at the top right,
there will be no errors:

![](PackageCheckAfterFirstFix.png)

This is how we like it:

```
0 errors | 0 warnings | 0 notes
```

Before submitting your package to CRAN, it is a good
idea to have exactly zero errors, warnings and notes.

# Write a function 

In this example, we will write a function
that has a non-standard style. Later, we will
use tools to tell us what could be improved. 

Go to the correct file: click on the 'R' folder:

![](MagicrR.png)

Click the filename `hello.R`:

![](MagicrRcontent.png)

Change the content to e.g. this:

```
do_magic <- function(x)
{
  if (!is.numeric(x)) {
    stop("x must be numeric");
  }
  out = x * 2;
  out = replace(out, out == 84, 42);
  out;
}
```

Again, this function has a non-standard style.

Also, we will rename this file to `do_magic.R`:

![](RenameBefore.png)

![](Rename.png)

If it states that the file `hello.R` has been moved or
renamed (and it has indeed), follow RStudio's suggestion
to close it.

And it worked:

![](RenameAfter.png)

When we now press CTRL-SHIFT-E or click 'Check' at the 'Build' tab at the top right,
there will be errors:

![](PackageCheckSecond.png)

The errors state that:

 * `do_magic` lacks documentation
 * the documentation about `hello` cannot find the `hello` function

The errors are correct: we will have to add documentation now!

## Add documentation

For documentation, we will use the `roxygen2` package.

First, we will install the `roxygen2` package.

Click 'Tools | Install packages':

[](InstallPackage.png)

Then type `roxygen2`:

![](InstallRoxygen2.png)

While `roxygen2` is installed, you'll see a lot of information in the Console window:

![](InstallRoxygen2Console.png)

Here, I assume `roxygen2` gets installed without problems, as shown in the image. 

After installing `roxygen2`, you may also want to install `knitr`, `rmarkdown` and `testthat`,
which we will need later in this article.

Now `roxygen2` is installed, our package must use it as a documentation engine.
To let this package use `roxygen2` for its documentation, click 'Project Options | Build Tools':

![](Roxygen.png)

Click 'Use Roxygen'.

I like to let Roxygen rebuild everything at all times, but this is just personal:

![](RoxygenWhat.png)

Add the documentation like this:

```
#' Multiples all values by two, 
#'   except 42, which stays 42
#' @param x input, must be numeric
#' @return magicified output
#' @export
do_magic <- function(x)
{
  if (!is.numeric(x)) {
    stop("x must be numeric");
  }
  out = x * 2;
  out = replace(out, out == 84, 42);
  out;
}
```

Click 'Document' or `CTRL + SHIFT + D`:

![](Document.png)

You will see the first results:

![](DocumentFirst.png)

It appears that `roxygen2` modifies your `DESCRIPTION` file, creates a file `do_magic.Rd` and
changes `NAMESPACE`. 

Click 'Clean and Rebuild'

![](CleanAndRebuild.png)

The take a look at the documentation of `do_magic`,
by typing this in the console:

```
?do_magic
```

The help will be displayed at the bottom-right of the screen:

![](HelpDo_magic.png)

When we now press CTRL-SHIFT-E or click 'Check' at the 'Build' tab at the top right,
there will be errors:

![](PackageCheckThird.png)

This error is caused, because `hello` is documented, but does not exist anymore.

The non-existing function `hello` is located in the folder `man`. In the `Files` tab,
go to the `man` folder. You see the two documentation files: `do_magic.Rd` for the existing `do_magic` 
function, and `hello.Rd` for the absent `hello` function. Delete `hello.Rd` by checking
the checkbox left of `hello.Rd` and click `Delete`: 

![](DeleteHelloRd.png)

When we now press CTRL-SHIFT-E or click 'Check' at the 'Build' tab at the top right,
there will be no errors!

# Write a vignette that describes and plots the function

With our `do_magic` function created and documented, it is time
to demonstrate it with a vignette.

Creating a vignette is easy: in the Console, type:

```
devtools::use_vignette("do_magic")
```
A default vignette will be created:

![](VignetteCreated.png)

You can see that:

 * There is a folder created, called `vignettes`
 * There is a file created, called `do_magic.Rmd` (in the folder `vignettes`)
 * The folder name `inst/doc` is added to `.gitignore`, which means that `git` will ignore that folder 

In this article, we will ruthlessly convert it to:

[](VignetteAdapted.png)

(note that I cannot add the code in plaintext here).

Press 'Knit' or CTRL-SHIFT-K to 'knit' the document:

![](Knit.png)

This will create a page like this:

![](Knitted.png)

# Add testthat to the package

```
A program that has not been tested does not work.

Bjarne Stroustrup. The C++ Programming Language. pp. 712.
```

As a testing framework, we will use the `testthat` package.
If `testthat` has not been installed yet, install it 
in a similar way to the `roxygen2` package. 

If `testthat` is installed, you can start using it by calling: 

```
devtools::use_test("do_magic")
```

After running this, RStudio will look like this:

![](TestthatFirst.png)

What has happened, is:

 * a folder called `tests` has been created, In it, there is a folder created called `testthat`
 * a file has been created `test-do_magic.R` with some default text. It is located in the `tests/testthat` folder
 * `testthat` has been added to the `Suggests` section of `DESCRIPTION`

You can now test you package by clicking 'Test Package' or using CTRL-SHIFT-T:

![](TestPackage.png)

Because currently our code only tests if two plus two equals four, all (single one) tests pass:

![](TestthatFirstTest.png) 

# Write proper tests

Change the code in *tests/testthat/test-do_magic.R* to:

```
context("do_magic")

test_that("do_magic: use", {
  expect_equal(do_magic(1), 2)
  expect_equal(do_magic(42), 42)
  expect_error(
    do_magic("Not numeric"),
    "x must be numeric"
  )
})
```

The tests check if everything in the documentation of `do_magic` is lived up to.
Also the exception thrown by non-numeric input is checked. 

Testing you package by clicking 'Test Package' or using CTRL-SHIFT-T results in a happy message:

![](TestthatSecondTest.png)

# Conclusion

Well done! You've created your first package!

Extra steps are:

 * Put it on GitHub, see 
 * Add Travis CI, see 'Professional R development: being a good boy/girl'

These are described in a shorter form below. 

# Extra: put it on GitHub

Create a GitHub with the same name as your package. In this case, it is called `magicr`.

Clone the GitHub to your local computer in a folder that will also have the name `magicr`, but
resides somewhere else on your harddrive.

```
git clone https://github.com/richelbilderbeek/magicr
```

Combine the two `magicr` folders' content to created one single `magicr` folder that has both 
a hidden `.git` folder and all the R package folders.

Push it back to GitHub:

```
git add --all :/
git commit -m "My first commit"
git push
```

Well done, your package is now on GitHub!

From now on, your package can be installed using

```
devtools::install_github("richelbilderbeek/magicr")
```

Everybody can now easily use the `do_magic` function!

# Extra: add Travis CI

To add the Travis CI continuous integration service, you'll need to:
 
 * Activate Travis CI
 * Activate Codecov
 * Create a file called `.travis.yml` 

Go to the Travis CI website, _www.travis-ci.org_, and sign in with your GitHub account.

The sliders indicate the Travis CI activation state. 
Go find your R package its GitHub and activate it.

Go to the Codecov website, *https://codecov.io*, and sign in with your GitHub account.

In the root folder of your package at a file called `.travis.yml` with the following content:

```
language: r
cache: packages

r_github_packages:
  - jimhester/lintr
  - jimhester/covr
  - MangoTheCat/goodpractice

after_success:
  - Rscript -e "lintr::lint_package()"
  - Rscript -e "covr::codecov()"
  - Rscript -e "goodpractice::gp()"
```

Push *.travis.yml* to GitHub, and at the Travis CI site (with some delay) you can see
the suggestions from the expert tools. 


