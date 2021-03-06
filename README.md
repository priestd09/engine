# The Engine: A Web Theme Engine for Go
[![Stability:
Experimental](https://masterminds.github.io/stability/experimental.svg)](https://masterminds.github.io/stability/experimental.html)
[![Build Status](https://travis-ci.org/Masterminds/engine.svg)](https://travis-ci.org/Masterminds/engine) [![GoDoc](https://godoc.org/github.com/Masterminds/engine?status.png)](https://godoc.org/github.com/Masterminds/engine) [![Go Report Card](http://goreportcard.com/badge/Masterminds/engine)](http://goreportcard.com/report/Masterminds/engine)

This library provides tools for managing themes (a collection of
templates and supporting files) for Go. It allows theme chaining
and overriding.

This library is oriented toward Web theming using `html/template`
and `text/template`.

With this library, you can:

- Support numerous themes in one app
- Cascade and override themes
- Provide easy support for re-theming an existing app


## Usage

Say you have the following theme directories, with the following files:

```
themes/
    |
    |- pretty/
    |    |
    |    |- main.tpl
    |
    |- ugly/
    |    |
    |    |- duckling.tpl
    |    |
    |    |- main.css
    |
    |- default/
         |
         |- main.tpl
         |
         |- duckling.tpl
         |
         |- main.css
```

There are three themes here: `themes/pretty`, `themes/ugly`, and
`themes/default`.

There are two types of file: templates (end in `.tpl`) and assets (don't
end in `.tpl`).

Say we want to apply the pretty theme, but with a backup to the default
theme. We never want any ugly theme content.

```go
package main

import "github.com/Masterminds/engine"

func main() {
    engine, err := New("themes/pretty", "themes/default")
    if err != nil {
        // ... handle the error
    }

    // Render the main.tpl, passing the template the data 42.
    // Because of the order of directories passed into New, this will
    // render to `pretty/main.tpl`.
    out, err := e.Render("main.tpl", 42)
    // ... out will have the rendered data.

    // This will use `themes/default/duckling.tpl` because there is no
    // duckling.tpl in the `themes/pretty` directory.
    out, err = e.Render("duckling.tpl", 42)

    // This will find the `main.css` in the `themes/default` folder,
    // since none exists in `themes/pretty`
    path, err := e.Asset("main.css")
}
```

In the example above, not that we load two of the three available
templates. Engine's primary roll, then, is to negotiate which theme
should be used for each individual render call.
