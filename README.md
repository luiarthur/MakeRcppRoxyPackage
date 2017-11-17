# Steps for Creating Rcpp Package

```R
### Install the following libraries if needed
#install.packages("roxygen2")
#install.packages("devtools")
#install.packages("Rcpp")
#devtools::create("SomePackage") # an old alternative

Rcpp::Rcpp.package.skeleton("SomePackage")

### Check session info
library(Rcpp)
sessionInfo()
```

Let `SomePackage` be the root directory.

Don't touch the following files and directories:
- `NAMESPACE`
- `man/`

## Do the Following

```bash
#rm SomePackage.Rproj
#mkdir src
```

## Edit Description File

Add these two lines to the `DESCRIPTION` file:
You may vary the Rcpp / R versions to suit your needs.
The `sessionInfo()` command in R will provide the version numbers.

```
Imports: Rcpp (>= 0.11.0)
LinkingTo: Rcpp
```

## Create the following executable

Create `gen.R` with the following contents:

```R
#!/usr/bin/Rscript

# Generate the docs
Rcpp::compileAttributes()
roxygen2::roxygenise()
```

Make it executable by doing in a terminal:

```bash
chmod +x gen.R
```

## Add files to `src` and `R`

An example cpp file in `src` is

```cpp
#include <Rcpp.h>
#include "addOne.h"

using namespace Rcpp;

//' SomethingCool
//' @param a A double
//' @export
// [[Rcpp::export]]
double something(double a) {
  // something(a) returns a + a + 1 = 2a + 1.
  Rcout << "Input: " << a << std::endl;
  return a + addOne(a);
}
```

which depends on `addOne.h`

```cpp
double addOne(double a) {
  return a + 1;
}
```

Note the `//'` are for `roxygen` to know what functions to document and add to 
the package; while the `// [[Rcpp::export]]` is for Rcpp to recognize
what functions to export to R.

## Generate Docs

```bash
./gen.R
```

## Install package locally

Go back one directory, so the package dir is in your current dir.

```bash
R CMD REMOVE SomePackage
R CMD build SomePackage
R CMD INSTALL SomePackage*.tar.gz
```

