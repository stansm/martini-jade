# render
Martini middleware/handler for easily rendering serialized JSON and HTML template responses from Jade templates.

[API Reference](http://godoc.org/github.com/codegangsta/martini-contrib/render)

## Usage
render uses Jade implementation in Go [go-floki/jade](https://github.com/go-floki/jade) package to render Jade templates.

~~~ go
// main.go
package main

import (
  "github.com/codegangsta/martini"
  "github.com/frogprog/martini-jade"
)

func main() {
  m := martini.Classic()
  // render html templates from templates directory
  m.Use(render.Renderer())

  m.Get("/", func(r render.Render) {
    r.HTML(200, "hello", map[string]string {
       "foo": "bar",
    })
  })

  m.Run()
}

~~~

~~~ html
<!-- templates/hello.jade -->
<h2>Hello #{foo}!</h2>
~~~

### Options
`render.Renderer` comes with a variety of configuration options:

~~~ go
// ...
m.Use(render.Renderer(render.Options{
  Directory: "templates", // Specify what path to load the templates from.
  Layout: "layout", // Specify a layout template. Layouts can call {{ yield }} to render the current template.
  Extensions: []string{".jade"}, // Specify extensions to load for templates.
  Funcs: []template.FuncMap{AppHelpers}, // Specify helper function maps for templates to access.
  Delims: render.Delims{"{[{", "}]}"}, // Sets delimiters to the specified strings.
  Charset: "UTF-8", // Sets encoding for json and html content-types. Default is "UTF-8".
  IndentJSON: true, // Output human readable JSON
}))
// ...
~~~

### Loading Templates
By default the `render.Renderer` middleware will attempt to load templates with a '.jade' extension from the "templates" directory. Templates are found by traversing the templates directory and are named by path and basename. For instance, the following directory structure:

~~~
templates/
  |
  |__ admin/
  |      |
  |      |__ index.jade
  |      |
  |      |__ edit.jade
  |
  |__ home.jade
~~~

Will provide the following templates:
~~~
admin/index
admin/edit
home
~~~

### Character Encodings
The `render.Renderer` middleware will automatically set the proper Content-Type header based on which function you call. See below for an example of what the default settings would output (note that UTF-8 is the default):
~~~ go
// main.go
package main

import (
  "github.com/codegangsta/martini"
  "github.com/codegangsta/martini-contrib/render"
)

func main() {
  m := martini.Classic()
  m.Use(render.Renderer())

  // This will set the Content-Type header to "text/html; charset=UTF-8"
  m.Get("/", func(r render.Render) {
    r.HTML(200, "hello", "world")
  })

  // This will set the Content-Type header to "application/json; charset=UTF-8"
  m.Get("/api", func(r render.Render) {
    r.JSON(200, map[string]interface{}{"hello": "world"})
  })

  m.Run()
}

~~~

In order to change the charset, you can set the `Charset` within the `render.Options` to your encoding value:
~~~ go
// main.go
package main

import (
  "github.com/codegangsta/martini"
  "github.com/codegangsta/martini-contrib/render"
)

func main() {
  m := martini.Classic()
  m.Use(render.Renderer(render.Options{
    Charset: "ISO-8859-1",
  }))

  // This is set the Content-Type to "text/html; charset=ISO-8859-1"
  m.Get("/", func(r render.Render) {
    r.HTML(200, "hello", "world")
  })

  // This is set the Content-Type to "application/json; charset=ISO-8859-1"
  m.Get("/api", func(r render.Render) {
    r.JSON(200, map[string]interface{}{"hello": "world"})
  })

  m.Run()
}

~~~

## Authors
* [Jeremy Saenz](http://github.com/codegangsta)
* [Cory Jacobsen](http://github.com/cojac)
* [frogprog](http://github.com/frogprog)
