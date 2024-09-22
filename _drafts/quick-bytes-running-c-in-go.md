---
layout: post
title: Quick Bytes - Running C++ in Go
description: How to run C++ code in Go using swig
categories: [quick bytes, go]
tags: [go, c++, swig]
---

## Background

At work, I was tasked to integrate a C++ library into a Go project. The library was written in C++ and I had to evaluate some viable options. Of course, I could rewrite the library in Go, but that would be a lot of work. I could also use cgo, but I wanted to keep the Go code clean and separate from the C++ code. I decided to use [SWIG](https://www.swig.org/).

## Introduction

### What is SWIG?

SWIG stands for Simplified Wrapper and Interface Generator. It is a software development tool that connects programs written in C and C++ with a variety of high-level programming languages. SWIG is used with different types of languages including common scripting languages such as Perl, PHP, Python, Ruby, and PHP.

### How does SWIG work?

SWIG reads the header files of the C/C++ code and generates wrapper code that can be compiled and linked with the Go code. The wrapper code is a Go package that can be imported and used in the Go code.

## Installation

In MacOS, you can install SWIG using Homebrew:

```bash
brew install swig
```

In Ubuntu/Debain based systems, you can install SWIG using apt:

```bash
sudo apt-get update
sudo apt-get install swig
```

For other operating systems, you can download the SWIG binaries from the [official website](https://www.swig.org/download.html).

## Usage

Using SWIG in other languages is more complicated than using it in Go. In other languages, you need to write an interface file that tells SWIG directives on how to generate the wrapper code.

In Go, fortunately, there is native support for SWIG. We can create a `.swigcxx` file that contains the directives and the Go compiler will automatically generate the wrapper code behind the scenes and it will work seamlessly.

However, only caveat is that the code is generated in the Go complier's working directory which means code linting and formatting tools might not work as expected.

## Example

A sample project can be found [here](https://github.com/sivayogasubramanian/swig).

### Steps

1. Write the C++ code
1. Write the SWIG interface file (`.swigcxx`)
1. Write the package file that imports "C" which has the same name as the module name in the `.swigcxx` file
1. Create a `main.go` file that imports the package and uses the C++ functions. Note that linting and formatting tools might not work as expected because the generated code is in the Go compiler's working directory.
1. Run `go run main.go`

## References
