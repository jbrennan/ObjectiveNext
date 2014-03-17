Language Ideas
==============

These are some syntax ideas for a programming language bouncing around in my head which I'll never get around to making because honestly it's not really necessary but it's fun to think about nonetheless.

This language's syntax is based largely on Objective-C but would be designed in a more modern way. No primitives, everything is an Object.

General Syntax
--------------

Resembles Objective-C quite a bit, but tries to simplify things when possible. Semicolons aren't needed. Comments are the same as in Objective-C/C. Most control statements are the same, too (`if`, `for`, etc.).

Classes
-------

Classes are defined in a single file (no headers). An example class definition:

	@implementation Person : Object
	@property occupation
	@property (read) name // options are (read or write), default is write and can be omitted
	
	// private instance method called `sayHi` which takes no arguments and returns void
	- sayHi {
		[[self name] log]
	}
	
	
	// public instance method called `sayHiTo:` which takes 1 argument and returns void
	// methods can be (private, protected, or public), default is private which can be omitted 
	- (public)sayHiTo:otherPerson {
		["Hi to " + [otherPerson name] log]
	}
	
	
	// public class method called `betterNumberOne:orNumberTwo:` which takes 2 arguments 
	// and returns an object
	+ (public)betterNumberOne:one orNumberTwo:two {
		return two
	}
	@end

Classes start with the `@implementation` keyword followed by the class name to be implemented. It is then followed by a `: SuperClass`. `Object` is the default and therefor can be omitted. 

(**Note** the use of `@-keywords` might be dropped. For now I like them but they might end up being kind of silly in the long run. I'm not married to them)

Properties
----------

A `@property` is a declaration of an instance variable for a class and can have several options, as seen below:

	@property bias // read/write, private, instance property. Default, so no options specified.
	@property (public) name // read/write, public, instance property.
	@property (public, read) age // read-only, public, instance property.
	@property (class) stork // read/write, private, class property.

The options for a property are as follows:

* One of `private`, `public`, or `protected`. Defaults to `private`. (**Note**: not sure of `protected`)
* One of `write` or `read`. Defaults to `write`.
* One of `instance` or `class`. Defaults to `instance`.

A property has a matching *instance variable* (or *class variable*) that is the property name prefixed with an underscore. So, `@property name` has an ivar called `_name`.

A property automatically has methods generated for it, depending on its read/write settings. In the default case, for `@property name` this will generate `-name` and `-setName:`, two private, instance methods. For `@property (public, read) age` that would give `- (public)age`.

The reason why properties are `private` by default is the same as why methods are `private` as default: It's because you typically have a lot more internal details than external. That is, the language should encourage good interfaces. Put another way, be liberal in your private implementations and be stringent in your public API.


Interfaces
----------

An `@interface` is not the same as an Objective-C `@interface`, but instead a lot more like Go's `interface` keyword (or like Java's). It's a set of methods, either required or optional, that a class must implement to be conforming. This is like Objective-C's `@protocol`.

A class is said to implement an interface if it has an implementation for all its required methods. It does not need to declare that it implements the interface (just like in Go).

For example:

	@interface Writer
	- (public)writeToFile:file
	@optional
	- (public)writeToZipFile:zipFile
	@end

Interface methods default to being `@required`, which can be omitted, or they can be `@optional` which is explicitly shown. Methods can also be `public`, `protected` or `private`, with the default being `private`. Private interface methods typically don't make as much sense, so usually you'll want to explicitly state `public`. Maybe these ought to default to `protected` instead, so at least subclasses can override them. These only make sense if it would also be possible for a consuming class to be able to *tell* what the conforming class implements. Given the `interface`

	interface Writer
	- (private)doSomethingPrivate
	end
	
Would it make sense for that method to be `private`? If it were private, how does an expecting class know whether or not the implementor conforms? It feels like `interface` methods should just all be `public`.


Messages
--------

There are three kinds of messages: `unary`, `binary`, and `keyword` messages.

* `unary` messages are sent to the receiver with no arguments for messages like `++`. e.g., `index++`.
* `binary` messages are sent with one argument for messages like `+`. e.g., `index + 1`.
* `keyword` messages are sent with n >= 0 arguments for messages like `-add:`. e.g., `[index add:1]`.


Principles
----------

* The language should encourage good program design.
* The language should get out of the way for its defaults and not make the programmer type needlessly.
* The language should have sensible, opinionated defaults.
* The language should do the obvious thing.
* The language should make simple things easy and complex things possible.

* Identity is derived from ability. It's more important what you can do than who you are.

* Development of tools should be simpler.
* A programmer should see changes as quickly as possible. The language should encourage play. (REPL)


Projects
--------

A project is a directory that meets the following conditions:

* Is under source control (e.g., git, hg, etc.)
* Has a `project.json` file
* Has a `readme` file of some kind

`project.json` should be a standard json file which gives some info about the project. It shouldn't be like an Xcode project file, as IDEs should just mimic the directory structure of the project directory.

But it might look something like this:

	{
	project: {
				deps: [
						"image-tools", "http://github.com/i/image-tools.git",
						...],
			},
	custom: { ... }
	}


Binary messages
---------------

Binary (and unary) messages should allow for somewhat simplified syntax for common operations. One of the most common things a developer will do is manipulate strings, arrays, and dictionaries. By allowing some binary messages, this simplifies their usage.

	<< // Give method. e.g., Log << "some string to print out"
	>> // Get method. e.g., Console >> inputString

Examples

	Log << someString
	array << objectToAdd
	secondArray = Array << otherObject

Keys
----

For working with dictionaries or anything else requiring strings for keys, there is a special syntax for quickly declaring a key:

	dictionary[@key] = value
	[someObject setValue:value forKey:@key2]

This is a lot like Ruby's `:symbol` notation, except using the `@` symbol instead of `:`.


@-keywords
----------

The more I think about not using @-keywords the more it seems like a good idea. They were used in Objective C to avoid conflict with C. They were used in Objective J to avoid conflict with Javascript. In this language's case, there is no existing language which we're bolting on to, so it would be silly to use @-keywords. A revised look at what a class would look like:

	implementation Cat : Animal // implementation or "class"?
	property (public, read) name // read-only, public instance property
	
	- (public)newCatWithName:name {
		[self setName:name] // Should a private setter always exist? 
	}
	
	end


String interpolation
--------------------

The language should have string interpolation like as is done in Ruby. Allow expressions to be evaluated inside of string literals like such:

	myString = "My name is #{[self name]}."
	

Booleans
--------

A `Boolean` is what is used to represent truthiness in the language and can either have the value `yes` or `no`. Because everything, including numbers, are objects, the only values which represent false are `no` and `nil`. Everything else, including `0` is considered to be true.


Logical operators
-----------------

In C-like languages, the logical And/Or operators are `&&` and `||` respectively. But in this language, the operators will be known as `and` and `or` respectively. This just makes the language easier to read.

Collections
-----------

Arrays and Dictionaries have special syntax because they're so frequently used:

	myArray = [object1, someString, 19, yes]
	myDictionary = {
				@name: "Jason Brennan",
				@age: 24,
				someOtherKey: value
			}

Working with Libraries
----------------------

A fundamental feature of the language should allow for it to be composed of many smaller sub-projects or libraries. Many projects these days depend on open source libraries and the language should support and encourage this. It should encourage the use of self-contained groups of objects.

Related to Libraries: The `import` keyword
------------------------------------------

I was thinking of doing a better kind of symbol importing for this language, one that would facilitate tools like Eclipse's auto-import of required classes, or removal of them. But the more I think about this, if it's possible for an IDE to figure out which libraries are needed to be imported, then why bother writing import statements at all? If a tool can figure out which other classes are needed, then it should happen automatically without the need for the user to do it. So maybe there should be **no `import` statements** at all.
