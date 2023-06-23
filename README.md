# goaccessor

[![Go Reference](https://pkg.go.dev/badge/github.com/yujiachen-y/goaccessor.svg)](https://pkg.go.dev/github.com/yujiachen-y/goaccessor)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[中文介绍](README-zh.md) 

`goaccessor` is a Go tool designed to automate the generation of `getter` and `setter` boilerplate code for your types and variables.

## Usage

```bash
go install github.com/yujiachen-y/goaccessor@latest
```

After installing `goaccessor`, you can use it in the command line interface (CLI) or with `//go:generate` directives. Check out the [Examples section](#examples) for more information.

## Examples

*You can find a runnable 'book' example in the [example folder](./example/).*

Consider the file `book.go`:

``` go
package main

//go:generate goaccessor --target Book --getter --setter
type Book struct {
    Title  string
    Author string
}
```

When we run `go generate`, it creates a new file `book_goaccessor.go`:

``` go
// Code generated by "goaccessor --target Book --getter --setter". DO NOT EDIT.

package main

func (b *Book) GetTitle() string {
    return b.Title
}

func (b *Book) SetTitle(title string) {
    b.Title = title
}

func (b *Book) GetAuthor() string {
    return b.Author
}

func (b *Book) SetAuthor(author string) {
    b.Author = author
}
```

### Top-level variables

`goaccessor` isn't just for struct types; it can also handle top-level constants and variables. For instance:

``` go
//go:generate goaccessor --target books --getter --setter
var books = map[string]*Book {
    ...
}
```

After executing `go generate`, we get:

``` go
func GetBooks() map[string]*Book {
    return books
}

func SetBooks(newBooks map[string]*Book) {
    books = newBooks
}
```

### Complex cases

In certain cases, you might want to export specific fields of a top-level variable with a prefix:

``` go
//go:generate goaccessor --target bestSellingBook --field --getter --include Author --prefix BestSelling
var bestSellingBook = &Book{ ... }
```

This directive will generate:

```go
func GetBestSellingAuthor() string {
    return bestSellingBook.Author
}
```

## Options

Here are the available options for `goaccessor`:

| Option | Short option | Description |
| ------ | ------------ | ----------- |
| --target | -t | Specify the target to be handled. |
| --getter | -g | Generate `getter` for the target. |
| --setter | -s | Generate `setter` for the target. |
| --accessor | -a | Generate both `getter` and `setter` for the target. |
| --prefix | -p | Add a prefix to the generated methods/functions. |
| --field | -f | Apply the flag (`getter`, `setter`, `accessor`) to each field of the target (only applicable for struct type variables). |
| --include | -i | Generate methods only for the specified fields (fields should be comma-separated). |
| --exclude | -e | Exclude specified fields from method generation (fields should be comma-separated). |

## Dependency Management

If you do not want to install `goaccessor` and want to use it as a dependency for your project, follow these steps:

1. Go to your project directory and add the `goaccessor` dependency via `go mod`:

``` bash
go get github.com/yujiachen-y/goaccessor@latest
```

2. Create a new file named `tools.go` (or any other name you like) with the following code:

``` go
//go:build tools

package main

import (
	_ "github.com/yujiachen-y/goaccessor"
)
```

3. If you want to use the `goaccessor` CLI command, use `go run github.com/yujiachen-y/goaccessor@latest` instead.

4. If you use `go:generate` directives to generate code, change the directives like this:

``` go
//go:generate go run github.com/yujiachen-y/goaccessor@latest -target book
var book Book
```

5. Note: even though `goaccessor` is a dependency of your project, it will not—and should not—be a part of your project's build result. We use a build flag in `tools.go` to ensure `goaccessor` is ignored during build.
