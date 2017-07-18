# Qt Coding Conventions

This document summarizes the high-level coding conventions for writing Qt client code at Daedalic Entertainment. They are based on the official Qt Coding Conventions:

* https://wiki.qt.io/Qt_Coding_Style
* https://wiki.qt.io/Coding_Conventions
* https://wiki.qt.io/Qt_Contribution_Guidelines
* https://community.kde.org/Policies/Library_Code_Policy

The goal is to make it easier to work in similar teams inside and outside the company, as well as have client code blend in with other code of the Qt API. We are providing a complete summary here in order to allow people to understand the conventions at a glance, instead of having to open multiple documents. Our coding conventions are numbered, which makes it easier to refer to them in code reviews.

Additions are _highlighted in italic_. These have been made where the official coding standard doesn't explictily include a rule, but we felt that further clarification is required.

Exceptions are __highlighted in bold__. You'll always find the justification of the exception right next to the rule.

You can facilitate using the below ruleset by importing [DaedalicQtCreatorCodeStyle.xml](https://github.com/DaedalicEntertainment/qt-coding-conventions/blob/master/DaedalicQtCreatorCodeStyle.xml) in QtCreator: Tools > Options > C++ > Code Style > Import...

In case we've missed recent changes to the official Qt coding conventions, or you can spot any other issue, please [create a pull request](https://help.github.com/articles/creating-a-pull-request/).


## 1. Includes

1.1. Include headers from external libraries using angle brackets.

      #include <QDate>

1.2. Include headers from your own project using double quotes.

      #include "myclass.h"

1.3. Try to reduce as much as possible the number of includes in header files. This will generally help reduce the compilation time, especially for developers when just one header has been modified. It may also avoid errors that can be caused by conflicts between headers. 
If an object in the class is only used by pointer or by reference, it is not required to include the header for that object. Instead, just add a forward declaration before the class. 

1.4. __Header files should use `#pragma once` to protect against possible multiple inclusion. This reduces the risk of copy & paste errors.__

## 2. Classes & Structs

2.1. Class names always start with an upper-case letter.

2.2. Acronyms are camel-cased (e.g. `QXmlStreamReader`, not `QXMLStreamReader`).

2.3. Every QObject subclass must have a `Q_OBJECT` macro, even if it doesn't have signals or slots, otherwise `qobject_cast` will fail.

2.4. Removed.

2.5. Removed.

2.6. _Classes with virtual member functions must have a virtual destructor._

2.7. _Classes that are not meant to be derived from have to be marked as `final`. This should be the default for non-interface classes. Care has to be taken when removing the `final` keyword from a class when inheritance is required. Classes that are already derived don't need to be marked as `final` by default: In the most common case there is no reason to prevent further inheritance._

2.8. _`final` classes have a non-virtual destructor unless they are already derived._

2.9. _Destructors in derived classes have to be marked with `Q_DECL_OVERRIDE`. This detects a missing virtual destructor in the base class._

2.10. _Use `struct`s for data containers, only. They shouldn't contain any business logic beyond simple validation or need any destructors._

## 3. Functions

3.1. Function names start with a lower-case letter. Each consecutive word in a function name starts with an upper-case letter.

3.2. When reimplementing a virtual method, do not put the `virtual` keyword in the header file. 
Annotate them with the `Q_DECL_OVERRIDE` macro after the function declaration, just before the `;`.

3.3. The prefix `set` is used for setters, but the prefix `get` is not used for accessors. Accessors are simply named with the name of the property they access. The exception is for accessors of a boolean which may start with the prefix `is`. 

      public:
          void setColor(const QColor& c);
          QColor color() const;
          void setDirty(bool b);
          bool isDirty() const;

3.4. Each object parameter that is not a basic type (int, float, bool, enum, or pointers) should be passed by reference-to-const. This is faster, because it is not required to do a copy of the object. Do that even for object that are already implicitly shared, like `QString`: 

      QString myMethod(const QString& foo,
                       const QPixmap& bar,
                       int number);

3.5. _Do not provide function implementations in header files._

3.6. _Don't add a space between function name and parameter list:_

      // Wrong:
      void setColor (const QColor& c);

      // Right:
      void setColor(const QColor& c);

## 4. Constructors

4.1. For each constructor (other than the copy constructor), check if you should make the constructor `explicit` in order to minimize wrong use of the constructor. Basically, each constructor that may take only one argument should be marked `explicit` unless the whole point of the constructor is to allow implicit casting.

4.2. _Either initialize all fields of a C++ class in the header, or all of them in the initialization list._

4.3. _Always begin initialization lists in the first line after the constructor signature._

      // Wrong
      AboutDialog::AboutDialog(QWidget* parent) : QDialog(parent), ui(new Ui::AboutDialog)

      // Right
      AboutDialog::AboutDialog(QWidget* parent)
      : QDialog(parent),
        ui(new Ui::AboutDialog)

## 5. Variables

5.1. Variable names start with a lower-case letter. Each consecutive word in a variable name starts with an upper-case letter. _Field names start with an underscore before the first lower-case latter. This way, we can ensure the same public API as Qt (see 3.3)._

5.2. Declare each variable on a separate line.

5.3. Avoid short or meaningless names (e.g. "a", "rbarr", "nughdeget"). Single character variable names are only okay for counters and temporaries, where the purpose of the variable is obvious.

5.4. __For pointers or references, never use a single space between the type and `*` or `&`, but a single space between the `*` or `&` and the variable name. For us, the fact that we are declaring a pointer or reference variable here much more belongs to the type of the variable than to its name:__

      char* x;
      const QString& myString;
      const char* const y = "hello";

## 6. Enums & Constants

6.1. When defining constants, prefer enums over `static const` variables over defines. 

6.2. _Other constant names, such as for QString constants, are ALL_CAPS with underscores between words._

## 7. Indentation & Whitespaces

7.1. 4 spaces are used for indentation. Spaces, not tabs!

7.2. Use blank lines to group statements together where suited.

7.3. Always use a single space after a keyword and before a curly brace.

      // Wrong
      if(foo){
      }

      // Correct
      if (foo) {
      }
7.4. Surround binary operators with spaces.

7.5. Do not put multiple statements on one line.

7.6. By extension, use a new line for the body of a control flow statement:

      // Wrong
      if (foo) bar();

      // Correct
      if (foo) {
          bar();
      }

## 8. Line Breaks

8.1. Keep lines shorter than 100 characters; wrap if necessary.

8.2. Operators start at the beginning of the new lines. An operator at the end of the line is easy to miss if the editor is too narrow.

      // Wrong
      if (longExpression +
          otherLongExpression +
          otherOtherLongExpression) {
      }

      // Correct
      if (longExpression
          + otherLongExpression
          + otherOtherLongExpression) {
      }

## 9. Braces

9.1. For statements, use attached braces: The opening brace goes on the same line as the start of the statement. If the closing brace is followed by another keyword, it goes into the same line as well:

      // Wrong
      if (codec)
      {
      }
      else
      {
      }

      // Correct
      if (codec) {
      } else {
      }

9.2. For class declarations and function definitions, always have the left brace on the start of a line:

      static void foo(int g)
      {
          qDebug("foo: %i", g);
      }

      class Moo
      {
      };

9.3. __Always use curly braces, even if the body of a conditional statement contains just one line. This avoids subtle bugs in the future, if the body is extended to span multiple statements:__

      // Wrong
      if (address.isEmpty())
          return false;

      for (int i = 0; i < 10;i)
          qDebug("%i", i);

      // Correct
      if (address.isEmpty()) {
          return false;
      }

      for (int i = 0; i < 10; +''i) {
          qDebug("%i", i);
      }

9.4. _Don't use spaces after braces:_

      // Wrong
      if ( ( a && b ) || c )

      // Correct
      if ((a && b) || c)

## 10. Parentheses

10.1. Use parentheses to group expressions:

      // Wrong
      if (a && b || c)

      // Correct
      if ((a && b) || c)

      // Wrong
      a + b & c

      // Correct
      (a + b) & c

## 11. Control Flow

11.1. Within switch statements, every `case` must have a `break` (or `return`) statement at the end or a comment to indicate that there's intentionally no `break`, unless another `case` follows immediately.

      switch (myEnum) {
        case Value1:
            doSomething();
            break;

        case Value2:
        case Value3:
            doSomethingElse();
            // fall through

        default:
            defaultHandling();
            break;
      }

11.2. Do not put `else` after jump statements:

      // Wrong
      if (thisOrThat) {
          return;
      } else {
          somethingElse();
      }

      // Correct
      if (thisOrThat) {
          return;
      }

      somethingElse();

11.3. Don't mix const and non-const iterators. This will silently crash on broken compilers.

      // Wrong
      for (Container::const_iterator it = c.begin(); it != c.end(); ++it)

      // Correct
      for (Container::const_iterator it = c.cbegin(); it != c.cend(); ++it)

## 12. Language Features

12.1. Use the `auto` keyword when it avoids repetition of a type in the same statement, or when assigning iterator types. If in doubt, for example if using `auto` could make the code less readable, do not use `auto`.

      auto* something = new MyCustomType();
      auto* keyEvent = static_cast<QKeyEvent*>(event);
      auto it = myList.const_iterator();

12.2. _Use `auto*` for pointers, to be consistent with references, and to add additional guidance for the reader._

12.3. _Use proprietary types, such as `QString` or `QList` where possible. This avoids unnecessary and repeated type conversion while interacting with the Qt framework APIs._

12.4. _Test whether a pointer is valid before dereferencing it._

## 13. Exceptions

13.1. _Throw exceptions from model or controller classes to propagate errors to the UI. This is much safer than returning error codes and relying on the caller not to forget to check for them._

13.2. Don't throw exceptions from a slot invoked by Qt's signal-slot connection mechanism, as this is considered [undefined behaviour](http://doc.qt.io/qt-5/exceptionsafety.html).

## 14. Comments

14.1. _Use a space after `//`._

14.2. _Place the comment on a separate line, not at the end of a line of code._

14.3. _Write comments as full sentences._

14.4. _Write API documentation with [QDoc comments](http://doc.qt.io/qt-5/01-qdoc-manual.html), wrapped in `/*! ... */`._

## 15. Namespaces

15.1. _Wrap all code in a matching namespace, e.g. with the name of the application or library._

15.2. _Avoid namespace pollution by only using specific types, never whole namespaces._

      // Right
      using Daedalic::AboutModel;

      // Wrong
      using namespace Daedalic;


## 16. Files

16.1. _Files should not be longer than 1000 lines._

16.2. _Each file should contain a single feature. Don't define more than one public type per file._

16.3. _Header files should adhere to the following structure:_

* `#pragma once`
* `#includes`
* forward declarations
* type definition

16.4. _Class definitions should adhere to the following structure:_

* `Q_OBJECT` macro if the class inherits from `QObject`
* constructors
* destructor
* public functions
* public slots
* operators
* public constants
* signals
* protected functions
* protected slots
* protected constants
* protected fields
* private functions
* private slots
* private constants
* private fields

_Within each of these groups, order members by name or logical groups._

16.5. _File names should be all lower-case, without any other symbols, as suggested by QtCreator._
