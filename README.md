0. [Source Code Layout](#1-source-code-layout)
0. [Comments](#2-comments)
0. [Naming](#3-naming)
0. [General Syntax](#4-general-syntax)
0. [Strings](#5-strings)
0. [Conditionals](#6-conditionals)
0. [Collections](#7-collections)
0. [Iteration](#8-iteration)
0. [Methods](#9-methods)
0. [Exceptions](#10-exceptions)
0. [Regular Expressions](#11-regular-expressions)
0. [Blocks / Procs](#12-blocks--procs)
0. [Classes](#13-classes)
0. [Misc](#14-misc)
0. [Rails](#15-misc)
0. [References](#16-references)

##1. Source Code Layout

0. Use `UTF-8` as the source file encoding.

0. Use two **spaces** per indentation level, no tabs.

0. Leave no trailing whitespace of any kind.

0. Do not leave a blank line at the bottom of the file.

0. Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)

0. Keep lines 90 characters wide or less.

0. Only use a single blank line to separate methods or other source code. If absolutely necessary in the short-term, use code banners; but do so only sparingly!

##2. Comments

0. Write self-documenting code. If feel you need a comment, refactor the code until a comment feels like overkill. Then, ignore the rest of this section.

0. Comments longer than a word are capitalized and use punctuation.

0. Avoid superfluous comments.

    ```Ruby
    # bad
    counter += 1 # increments counter by one
    ```

0. Keep existing comments up-to-date. An outdated is worse than no comment
at all.

0. Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory.

0. Avoid code banners. Your class is probably too big if you need them. Refactor!

    ```Ruby
    ################################################
    # Code banners are a code smell
    ################################################
    ```

0. Parameter explanations, examples, return descriptions, etc., are often overkill, but you should consider adding them to public API methods. If you do add them, follow [tomdoc's conventions](http://tomdoc.org/).

0. Write inline comments like this:

    ```Ruby
    # This is a description of the line.
    @pages.each { |p| puts p.name }
    ```

0. Write comments for methods, classes, modules, etc., like this:

    ```Ruby
    # Print a log line to STDOUT. You can customize the output by specifying
    # a block.
    #
    # msgs  - Zero or more String messages that will be printed to the log
    #         separated by spaces.
    # block - An optional block that can be used to customize the date format.
    #         If it is present, it will be sent a Time object representing
    #         the current time. Your block should return a String version of
    #         the time, formatted however you please.
    #
    # Examples
    #
    #   log("An error occurred.")
    #
    #   log("No such file", "/var/log/server.log") do |time|
    #     time.strftime("%Y-%m-%d %H:%M:%S")
    #   end
    #
    # Returns nothing.
    #
    def log(*msgs, &block)
      ...
    end
    ```

##3. Naming

0. Use `snake_case` for methods and variables.

0. Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)

0. Use `SCREAMING_SNAKE_CASE` for other constants.

0. The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).

0. The names of potentially "dangerous" methods (i.e. methods that modify `self` or the
  arguments, `exit!` (doesn't run the finalizers like `exit` does), etc.) should end with an exclamation mark if and only if
  there exists a safe version of that *dangerous* method.

    ```Ruby
    # bad - there is not matching 'safe' method
    class Person
      def update!
      end
    end

    # good
    class Person
      def update
      end
    end

    # good
    class Person
      def update!
      end

      def update
      end
    end
    ```

0. Define the non-bang (safe) method in terms of the bang (dangerous)
  one if possible.

    ```Ruby
    class Array
      def flatten_once!
        res = []

        each do |e|
          [*e].each { |f| res << f }
        end

        replace(res)
      end

      def flatten_once
        dup.flatten_once!
      end
    end
    ```

0. When defining binary operators, name the argument `other`.

    ```Ruby
    def +(other)
      # body omitted
    end
    ```

##4. General Syntax

0. Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    ```Ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    ```

    The only exception is when using the exponent operator:

    ```Ruby
    # bad
    e = M * c ** 2

    # good
    e = M * c**2
    ```

0. No spaces after `(`, `[` or before `]`, `)`.

    ```Ruby
    some(arg).other
    [1, 2, 3].length
    ```

##5. Strings

0. Prefer string interpolation instead of string concatenation:

    ```Ruby
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

0. Prefer single-quoted strings when you don't need string interpolation or
  special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

0. Don't use `{}` around instance variables being interpolated into a
  string.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      # bad
      def to_s
        "#{@first_name} #{@last_name}"
      end

      # good
      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

0. Use `%()` for single-line strings which require both interpolation
  and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```Ruby
    # bad (no interpolation needed)
    %(<div class="text">Some text</div>)
    # should be '<div class="text">Some text</div>'

    # bad (no double-quotes)
    %(This is #{quality} style)
    # should be "This is #{quality} style"

    # bad (multiple lines)
    %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    # should be a heredoc.

    # good (requires interpolation, has quotes, single line)
    %(<tr><td class="name">#{name}</td>)
    ```

0. Avoid `%q` and `%Q`.

0. Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```Ruby
    # good and also fast
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

##6. Conditionals

0. Indent `when` as deep as `case`.

    ```Ruby
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end

    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
    ```

0. Use `when x then ...` for one-line conditions in case blocks.

0. Never use `then` for multi-line `if/unless`.

    ```Ruby
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

0. Favour the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

    ```Ruby
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

0. Avoid multi-line `?:` (the ternary operator), use `if/unless` instead.

0. Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```Ruby
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

0. Never use `if x; ...`. Use the ternary operator instead.

0. Never use `when x; ...`. See the previous rule.

0. Use `&&/||` for boolean expressions, `and/or` for control flow.  (Rule
  of thumb: If you have to use outer parentheses, you are using the
  wrong operators.)

    ```Ruby
    # boolean expression
    if some_condition && some_other_condition
      do_something
    end

    # control flow
    document.saved? or document.save!
    ```

0. Favour modifier `if/unless` usage when you have a single-line
  body.

    ```Ruby
    # bad
    if some_condition
      do_something
    end

    # good
    do_something if some_condition
    ```

0. Favour `unless` over `if` for negative conditions (or control
  flow `or`).

    ```Ruby
    # bad
    do_something if !some_condition

    # good
    do_something unless some_condition

    # another good option
    some_condition or do_something
    ```

0. Never use `unless` with `else`. Rewrite these with the positive case first.

    ```Ruby
    # bad
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # good
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

0. Don't use parentheses around the condition of an `if/unless/while`,
  unless the condition contains an assignment (see "Using the return
  value of `=`" below).

    ```Ruby
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end

    # ok
    if (x = self.next_value)
      # body omitted
    end
    ```

0. Favour `!` over `not`. The latter binds more loosely, and can lead to confusing results.

##7. Collections

0. Prefer literal array and hash creation notation (unless you need to
pass parameters to their constructors, that is).

    ```Ruby
    # bad
    arr = Array.new
    hash = Hash.new

    # good
    arr = []
    hash = {}
    ```

0. Prefer `%w` to the literal array syntax when you need an array of single-word
strings.

    ```Ruby
    # bad
    STATES = ['draft', 'open', 'closed']

    # good
    STATES = %w(draft open closed)
    ```

0. Use parentheses are the delimeters for `%w`.

    ```Ruby
    # bad
    STATES = %w[draft open closed]

    # good
    STATES = %w(draft open closed)
    ```

0. Avoid `%W`.

0. Avoid the creation of huge gaps in arrays.

    ```Ruby
    arr = []
    arr[100] = 1 # now you have an array with lots of nils
    ```

0. Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.

0. Use symbols instead of strings as hash keys.

    ```Ruby
    # bad
    hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

    # good
    hash = { one: 1, two: 2, three: 3 }
    ```

0. Avoid the use of mutable objects as hash keys.

0. Use the new Ruby 1.9 has syntax whenever possible. For cases in which
   the hash keys cannot be symbolized, use the old syntax.

    ```Ruby
    # good
    hash = { one: 1, two: 2, three: 3 }

    # deprecated
    hash = { :one => 1, :two => 2, :three => 3 }
    ```

0. Rely on the fact that hashes in 1.9 are ordered.

0. Leave a single space padding inside the braces of a hash.

    ```Ruby
    # bad
    hash = {one: 1, two: 2}

    # good
    hash = { one: 1, two: 2 }
    ```

0. Leave no padding inside the brackets of an array.

    ```Ruby
    # bad
    array = [ 1, 2, 3 ]

    # good
    array = [1, 2, 3]
    ```

0. Use a single line for arrays and hashes if they will fit.

0. Format multiline hashes and arrays by indenting two-spaces.

    ```Ruby
    hash = {
      name:             'peter',
      professional:     'teacher',
      favourite_color:  'green'
    }

    array = [
      'Happy Sauce',
      'Awesome people eating computers'
    ]
    ```

0. You may optionally align hash values if it improves readability. Especially
    when there are many keys.

    ```Ruby
    hash = {
      name:             'peter',
      professional:     'teacher',
      favourite_color:  'green'
    }
    ```

0. Prefer `size` over `length` for getting the number of elements.

##8. Iteration

0. Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```Ruby
    arr = [1, 2, 3]

    # bad
    for elem in arr do
      puts elem
    end

    # good
    arr.each { |elem| puts elem }
    ```

0. Favour modifier `while/until` usage when you have a single-line
  body.

    ```Ruby
    # bad
    while some_condition
      do_something
    end

    # good
    do_something while some_condition
    ```

0. Favour `until` over `while` for negative conditions.

    ```Ruby
    # bad
    do_something while !some_condition

    # good
    do_something until some_condition
    ```

0. Never modify a collection while traversing it.

0. Know the iterators provided to you by Hash, Array, and enumerable. Don't re-invent the wheel.

0. Prefer `map` over `collect`, `find` over `detect`, `select` over
  `find_all`, `reduce` over `inject`.

0. When using `reduce` with short blocks, name the arguments `|a, e|`
  (accumulator, element).

##9. Methods

0. Use empty lines between `def`s and to break up a method into logical
  paragraphs.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

0. Indent the parameters of a method call if they span over multiple lines.

    ```Ruby
    # starting point (line is too long)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end

    # bad
    Mailer.deliver(to: 'bob@example.com',
                   from: 'us@example.com',
                   subject: 'Important message',
                   body: source.text)

    # good
    Mailer.deliver(
      to:         'bob@example.com',
      from:       'us@example.com',
      subject:    'Important message',
      body:       source.text
    )

    # also good
    create(
      :invitation,
      email:      'bob@example.com',
      role:       :author
    )
    ```

0. Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.

    ```Ruby
    def some_method
      # body omitted
    end

    def some_method_with_arguments(arg1, arg2)
      # body omitted
    end
    ```

0. Omit parentheses around parameters for methods that are part of an
  internal DSL (e.g. Rake, Rails, RSpec), methods that are with
  "keyword" status in Ruby (e.g. `attr_reader`, `puts`) and attribute
  access methods. Use parentheses around the arguments of all other
  method invocations.

    ```Ruby
    class Person
      attr_reader :name, :age

      # omitted
    end

    temperance = Person.new('Temperance', 30)
    temperance.name

    puts temperance.age

    x = Math.sin(y)
    array.delete(e)
    ```

0. Avoid `return` where not required.

    ```Ruby
    # bad
    def some_method(some_arr)
      return some_arr.size
    end

    # good
    def some_method(some_arr)
      some_arr.size
    end
    ```

0. Use spaces around the `=` operator when assigning default values to method parameters:

    ```Ruby
    # bad
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # good
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

0. Never put a space between a method name and the opening parenthesis.

    ```Ruby
    # bad
    f (3 + 2) + 1

    # good
    f(3 + 2) + 1
    ```

0. If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example, write
`f((3 + 2) + 1)`.

0. If the last parameter is a hash, don't use curly brackets around the arguments.

    ```Ruby
    # bad
    do_something({
      param1: value1,
      param2: value2
    })

    # good
    do_something(
      param1: value1,
      param2: value2
    )
    ```

##10. Exceptions

0. Use `e` as the rescued variable:

    ```Ruby
    rescue StandardError => e
    ```

0. Signal exceptions using the `fail` keyword. Use `raise` only when
  catching an exception and re-raising it (because here you're not failing, but explicitly and purposefully raising an exception).

    ```Ruby
    begin
      fail 'Oops';
    rescue => e
      raise if e.message != 'Oops'
    end
    ```

0. Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

    ```Ruby
    def foo
      begin
        fail
      ensure
        return 'very bad idea'
      end
    end
    ```

0. Use *implicit begin blocks* when possible.

    ```Ruby
    # bad
    def foo
      begin
        # main logic goes here
      rescue
        # failure handling goes here
      end
    end

    # good
    def foo
      # main logic goes here
    rescue
      # failure handling goes here
    end
    ```

0. Don't suppress exceptions.

    ```Ruby
    # bad
    begin
      # an exception occurs here
    rescue SomeError
      # the rescue clause does absolutely nothing
    end

    # bad
    do_something rescue nil
    ```

0. Don't use exceptions for flow of control.

    ```Ruby
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts 'Cannot divide by 0!'
    end

    # good
    if d.zero?
      puts 'Cannot divide by 0!'
    else
      n / d
    end
    ```

0. Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

    ```Ruby
    # bad
    begin
      # calls to exit and kill signals will be caught (except kill -9)
      exit
    rescue Exception
      puts "you didn't really want to exit, right?"
      # exception handling
    end

    # good
    begin
      # a blind rescue rescues from StandardError, not Exception as many
      # programmers assume.
    rescue => e
      # exception handling
    end

    # also good
    begin
      # an exception occurs here

    rescue StandardError => e
      # exception handling
    end
    ```

0. Put more specific exceptions higher up the rescue chain, otherwise
  they'll never be rescued from.

    ```Ruby
    # bad
    begin
      # some code
    rescue Exception => e
      # some handling
    rescue StandardError => e
      # some handling
    end

    # good
    begin
      # some code
    rescue StandardError => e
      # some handling
    rescue Exception => e
      # some handling
    end
    ```

0. Release external resources obtained by your program in an ensure
block.

    ```Ruby
    f = File.open('testfile')
    begin
      # .. process
    rescue
      # .. handle error
    ensure
      f.close unless f.nil?
    end
    ```

0. Favour the use of exceptions for the standard library over
introducing new exception classes.

##11. Regular Expressions

0. Don't use regular expressions if you just need plain text search in string:
  `string['text']`
0. For simple constructions you can use regexp directly through string index.

    ```Ruby
    match = string[/regexp/]             # get content of matched regexp
    first_group = string[/text(grp)/, 1] # get content of captured group
    string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
    ```

0. Use non capturing groups when you don't use captured result of parenthesis.

    ```Ruby
    /(first|second)/   # bad
    /(?:first|second)/ # good
    ```

0. Avoid using $1-9 as it can be hard to track what they contain. Named groups
  can be used instead.

    ```Ruby
    # bad
    /(regexp)/ =~ string
    ...
    process $1

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

0. Character classes have only few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.

0. Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

    ```Ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

0. Use `%r` only for regular expressions matching *more than* one '/' character.

    ```Ruby
    # bad
    %r(\s+)

    # still bad
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

0. Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```Ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

0. For complex replacements `sub`/`gsub` can be used with block or hash.

##12. Blocks / Procs

0. Use `{...}` over `do...end` for single-line blocks.

    ```Ruby
    # good
    names.each { |name| puts name }

    # bad
    names.each do |name|
      puts name
    end
    ```

0. Use `{...}` over `do...end` when chaining.

    ```Ruby
    # good
    names.select { |name| name.start_with?('S') }.map { |name| name.upcase }

    # good
    expect {
      page.publish
    }.to raise_error(StandardError)

    # bad
    names.select do |name|
      name.start_with?('S')
    end.map { |name| name.upcase }
    ```

0. Use the old lambda syntax over the new literal syntax. NOTE: We prefer the new syntax, but
  are staying with the old for consistency with the existing codebase.

    ```Ruby
    # bad
    lambda = ->(a, b) { a + b }
    lambda.(1, 2)

    # good
    lambda = lambda { |a, b| a + b }
    lambda.call(1, 2)
    ```

0. Use `_` for unused block parameters.

    ```Ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_, v| v + 1 }
    ```

##13. Classes

0. Avoid `self` where not required.

    ```Ruby
    # bad
    def ready?
      if self.last_reviewed_at > self.last_updated_at
        self.worker.update(self.content, self.options)
        self.status = :in_progress
      end
      self.status == :verified
    end

    # good
    def ready?
      if last_reviewed_at > last_updated_at
        worker.update(content, options)
        self.status = :in_progress
      end
      status == :verified
    end
    ```

0. As a corollary, avoid shadowing methods with local variables unless they are both equivalent

    ```Ruby
    class Foo
      attr_accessor :options

      # ok
      def initialize(options)
        self.options = options
        # both options and self.options are equivalent here
      end

      # bad
      def do_something(options = {})
        unless options[:when] == :later
          output(self.options[:message])
        end
      end

      # good
      def do_something(params = {})
        unless params[:when] == :later
          output(options[:message])
        end
      end
    end
    ```

0. Use the `attr` family of functions to define trivial accessors or
mutators.

    ```Ruby
    # bad
    class Person
      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def first_name
        @first_name
      end

      def last_name
        @last_name
      end
    end

    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ```

0. Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.

    ```Ruby
    class Parent
      @@class_var = 'parent'

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = 'child'
    end

    Parent.print_class_var # => will print "child"
    ```

  As you can see all the classes in a class hierarchy actually share one
  class variable. Class instance variables should usually be preferred
  over class variables.

0. Assign proper visibility levels to methods (`private`, `protected`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default).

0. Prefer `private` visibility over `protected` unless you truly know you need the latter.

0. Indent `private` and `protected` methods.

    ```Ruby
    class SomeClass

      def public_method
        # ...
      end

      private

        def private_method
          # ...
        end

    end
    ```

0. Order methods in your classes by visibility: `public`, then `protected`, and `private` at the bottom.

0. Order class methods before instance methods.

0. Use `def self.method` to define singleton methods. This makes the methods
  more resistant to refactoring changes.

    ```Ruby
    class TestClass
      # bad
      def TestClass.some_method
        # body omitted
      end

      # good
      def self.some_other_method
        # body omitted
      end

      # Also possible and convenient when you
      # have to define many singleton methods.
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end
    end
    ```

##14. Misc

0. Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be shorter than
  5 LOC. Empty lines do not contribute to the relevant LOC.
0. Avoid parameter lists longer than three or four parameters.
0. If you really have to, add "global" methods to Kernel and make them private.
0. Use class instance variables instead of global variables.

    ```Ruby
    #bad
    $foo_bar = 1

    #good
    class Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

0. Avoid `alias` when `alias_method` will do.
0. Avoid needless metaprogramming.
0. Do not mutate arguments unless that is the purpose of the method.
0. Avoid more than three levels of block nesting.

0. Avoid line continuation (\\) where not required. In practice, avoid using
  line continuations at all.

    ```Ruby
    # bad
    result = 1 - \
             2

    # good (but still ugly as hell)
    result = 1 \
             - 2
    ```

0. Using the return value of `=` (an assignment) is ok, but surround the
  assignment with parenthesis.

    ```Ruby
    # good - shows intended use of assignment
    if (v = array.grep(/foo/)) ...

    # bad
    if v = array.grep(/foo/) ...

    # also good - shows intended use of assignment and has correct precedence.
    if (v = self.next_value) == 'hello' ...
    ```

0. Use `||=` freely to initialize variables.

    ```Ruby
    # set name to Bozhidar, only if it's nil or false
    name ||= 'Bozhidar'
    ```

0. Don't use `||=` to initialize boolean variables. (Consider what
would happen if the current value happened to be `false`.)

    ```Ruby
    # bad - would set enabled to true even if it was false
    enabled ||= true

    # good
    enabled = true if enabled.nil?
    ```

0. Avoid using Perl-style special variables (like `$0-9`, `$``,
  etc. ). They are quite cryptic and their use in anything but
  one-liner scripts is discouraged.

0. Prefer `()` as delimiters for all `%` literals.

##15. Rails

See the examples in `model.rb`, `model_spec.rb`, `factory.rb`, and `view.html.erb` for canonical examples.

##16. References

Read these books and guides:

- Eloquent Ruby

- The Well-grounded Rubyist

- Rails guides

- Clean Code (Robert C. Martin)
