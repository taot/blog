---
layout: post
---
### HTML

We should be using HTML5 standards. IE brower compatibilities should
be considered, but not to an extreme extent. If it's too hard to
support old IE browers, then we should drop the support and notify the
users to get Firefox or Chrome.

### Javascript

We should following non-intrusive Javascript coding style, meaning the
page files should be plain HTML, not containing Javascript. Instead,
the scripts are placed in a central place and being included in the
pages. This helps make the page clean and maintainable, also it's a
well-established coding style in the industry.

We should use Javascrption modules, like RequireJS, or something
alike, for mainanence as well.

We can use Javascript libraries like jQuery to ease our coding. But
the usage should be restricted. An introduction of a library should be
discussed and justified, and the arguments should be supported by
enough experience or concrete project. Too many libraries will make
the pages too heavy and hard to read.

Newer scripting language like Coffeescript or Dart may be considered,
but again, they should be scrutinized and justified.

### CSS

External style sheet should be always used, which means css rules
should be defined in separate css files, and be linked from the web
pages. However there might be exceptions where inline style or
internal style sheet is appropriate.

### Formatting

HTML, Javascript, CSS code should be well indented. Regarding to the
indent size, we have not concluded yet.

My recommendation is two spaces for indent. Four spaces is a standard
in Java world, but it's too wide for web pages, especially in HTML
when there are many deep nesting elements.