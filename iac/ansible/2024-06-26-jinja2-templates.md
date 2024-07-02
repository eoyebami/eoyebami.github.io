<h1>Jinja2 Templates</h1>

* As far as I'm concerned `Jinja2 Templating` is very similar to the `Go Templating` that `Helm` uses, but rather than being called functions, you can manipulate vars in `Jinja2` with what is called `filters`
* Vars can be called in this templates by denoting the key within brackets like such: `{{ foo.bar }}` or `{{ foo['bar'] }}`

<h2>Filters</h2>

* Vars can be modified by using `filters` which are separated from the key by using a `|`
* `String Filters`
- `Capitalize`: capitalizes first character
  * `{{ foo.bar | capitalize }}`
- `Center`: centers text with specified width
  * `{{ foo.bar | center(30) }}`
- `Default`: base default value
  * `{{ foo.bar | default('Hello World') }}`
- `DictSort`: sorts dictionary
  * `{{ for key, value in mydict | dictsort }}` sorts by key
  * `{{ for key, value in mydict | dictsort(reverse=true) }}` sorts in reverse order
  * `{{ for key, value in mydict | dictsort(true) }}` case sensitive
  * `{{ for key, value in mydict | dictsort(false, 'value') }}` sort dict by value
- `Escape`: use to replace `&, <, >, ', "` with HTML safe sequences
  * `{{ foo.bar | escape }}`
- `First`: returns first item in a sequence
  * `{{ myList | first }}`
- `Float`: converts value into a floating point number, will return 0.0 if it fails
  * `{{ foo.bar | float }}`
- `Indent`: indentation
  * `{{ foo.bar | indent(4, False, False) }}` how many lines to indent, should skip indenting first and empty lines
- `Int`: converts value to int
  * `{{ foo.bar | int }}` 
- `Join`: concats multiple str into one
  * `{{ [1, 2, 3] | join }}` can join with Field separators by defining join('-')
- `Last`: returns last item item in sequence
  * `{{ foo.bar | last }}`
- `Length`: returns length of items in sequence
  * `{{ foo.bar | length }}`
- `Lower`: coverts to lower case
  * `{{ foo.bar| lower }}`
- `Upper`: converts to upper
  * `{{ foo.bar | upper }}`
- `List`: converts to list, if string, will return list of individual characters
  * `{{ foo.bar | list }}`
- `Replace`: Replace values
  * `{{ foo.bar | replace ("hello", "goodbye", 1) }}` what should be replace and by what and how many occurrences should be replaced
- `String`: convert to String
  * `{{ foo.bar | string }}`
- `Trim`: trim whitespaces 
  * `{{ foo.bar | trim }}`
- `Unique`: returns unique items from list
  * `{{ ['foo', 'bar', 'foo'] | unique | list }}`
- `Min & Max`: get min and max number in array
  * `{{ [1, 2] | min }}`
  * `{{ [1, 2] | max }}`

* All other filters can be found [here](https://tedboy.github.io/jinja2/templ14.html)

<h1>Loops & Conditionals</h1>

* [Loops and Conditionals](https://ttl255.com/jinja2-tutorial-part-2-loops-and-conditionals/) can also be used within `Jinja2 Templates` as well
* Loops

  ```yml
  {% raw %}
  {% for number in [0,1,2,3] %}
  {{ number }}
  {% endfor %}
  {% endraw %}
  ```

* Looping over Dictionaries

  ```yml
  {% raw %}
  {% for intf in myDict %} # defined outside of Jinja2
  interface {{ intf }}
    desc {{ interface[intf].desc }}
  {% endfor %}
  {% endraw %}
  ```

* Looping over Dictionary Values

  ```yml
  {% raw %}
  {% for key, value in myDict.items() %} # defined outside of Jinja2
  interface {{ key }}
    desc {{ value.desc }}
  {% endfor %}
  {% endraw %}
  ```

* Nested Iteration over Array

  ```yml
  {% raw %}
  {% for key, value in myDict.items() %} # defined outside of Jinja2
  interface {{ key }}
    desc {{ value.desc }}
    array:
  {% for line in value.array %}
    - {{ line }}
  {% endfor %}
  {% endfor %}
  {% endraw %}
  ```

* If Statement

  ```yml
  {% raw %}
  {% for number in [0,1,2,3] %}
    {% if number >= 2 %}
      highNum: {{ number }}
    {% elif number =< 1 %}
      lowNum: {{ number }}
    {% endif %}
  {% endfor %}
  {% endraw %}
  ```

* Logical Operators: `and` `or` `not`

   ```yml
  {% raw %}
  {% for number in [0,1,2,3] %}
    {% if number >= 2 and number is not False %}
      highNum: {{ number }}
    {% elif number =< 1 or number is not False  %}
      lowNum: {{ number }}
    {% endif %}
  {% endfor %}
  {% endraw %}
  ```
