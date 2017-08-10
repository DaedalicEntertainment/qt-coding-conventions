# Qt Coding Conventions

This document summarizes the high-level coding conventions for writing Qt client code at Daedalic Entertainment. They are based on the official Qt Coding Conventions:

* https://wiki.qt.io/Qt_Coding_Style
* https://wiki.qt.io/Coding_Conventions
* https://wiki.qt.io/Qt_Contribution_Guidelines
* https://community.kde.org/Policies/Library_Code_Policy

The goal is to make it easier to work in similar teams inside and outside the company, as well as have client code blend in with other code of the Qt API. We are providing a complete summary here in order to allow people to understand the conventions at a glance, instead of having to open multiple documents. Our coding conventions are numbered, which makes it easier to refer to them in code reviews.

You can facilitate using the below ruleset by importing [DaedalicQtCreatorCodeStyle.xml](https://github.com/DaedalicEntertainment/qt-coding-conventions/blob/master/DaedalicQtCreatorCodeStyle.xml) in QtCreator: Tools > Options > C++ > Code Style > Import...

In case we've missed recent changes to the official Qt coding conventions, or you can spot any other issue, please [create a pull request](https://help.github.com/articles/creating-a-pull-request/).


## 1. Namespaces

1.1. __DO__ use PascalCase for namespace names.

1.2. __DO__ wrap all code in a matching namespace, e.g. with the name of the application or library.

1.3. __AVOID__ namespace pollution by only using specific types, never whole namespaces.

      // Right:
      using Daedalic::AboutModel;

      // Wrong: Might clash with other symbols imported from different namespaces.
      using namespace Daedalic;


## 2. Files

2.1. __DO__ use all lower-case file names, without any other symbols, as suggested by QtCreator.

2.2. __DO__ write header files with the following structure:

* `#pragma once`
* `#includes`
* forward declarations
* type definition

2.3. __DO__ define classes with the following structure:

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

Within each of these groups, order members by name or logical groups.

2.4. __DO NOT__ cover more than a single feature in each file. Don't define more than one public type per file.

2.5. __AVOID__ files with more than 1000 lines.

2.6. __DO__ leave a blank line at the end of the file to play nice with gcc. 


## 3. Includes

3.1. __DO__ include headers from external libraries using angle brackets.

      #include <QDate>

3.2. __DO__ include headers from your own project using double quotes.

      #include "myclass.h"

3.3. __DO__ not include unused headers. This will generally help reduce the compilation time, especially for developers when just one header has been modified. It may also avoid errors that can be caused by conflicts between headers. If an object in the class is only used by pointer or by reference, it is not required to include the header for that object. Instead, just add a forward declaration before the class. 

3.4. __DO__ use `#pragma once` in header files to protect against possible multiple inclusion. This also reduces the risk of copy & paste errors.

3.5. __DO NOT__ rely on a header that is included indirectly by another header you include.


## 4. Classes & Structs

4.1. __DO__ use PascalCase for class names, e.g. `BankAccount`.

4.2. __DO__ uppercase the first letter of acronyms, only, e.g. `QXmlStreamReader`, not `QXMLStreamReader`.

4.3. __DO__ add the `Q_OBJECT` macro to every `QObject` subclass, even if it doesn't have signals or slots, otherwise `qobject_cast` will fail.

4.4. __DO__ add a virtual destructor to classes with virtual member functions.

4.5. __DO__ marks classes that are not meant to be derived from as `final`. This should be the default for non-interface classes. Care has to be taken when removing the `final` keyword from a class when inheritance is required. Classes that are already derived don't need to be marked as `final` by default: In the most common case there is no reason to prevent further inheritance.

4.6. __DO__ use a non-virtual destructor in `final` classes have unless they are already derived.

4.7. __DO__ mark destructors in derived classes as `Q_DECL_OVERRIDE`. This detects a missing virtual destructor in the base class.

4.8. __DO__ use `struct`s for data containers, only. They shouldn't contain any business logic beyond simple validation or need any destructors.


## 5. Constructors

5.1. __DO__ mark each constructor that takes exactly one argument as `explicit`, unless it's a copy constructor or the whole point of the constructor is to allow implicit casting. This minimizes wrong use of the constructor.

5.2. __DO__ initialize all fields of a C++ class either in the header, or all of them in the initialization list.

5.3. __DO__ begin initialization lists in the first line after the constructor signature.

      // Right:
      AboutDialog::AboutDialog(QWidget* parent)
      : QDialog(parent),
        ui(new Ui::AboutDialog)

      // Wrong:
      AboutDialog::AboutDialog(QWidget* parent) : QDialog(parent), ui(new Ui::AboutDialog)


## 6. Functions

6.1. __DO__ use camelCase for functions names.

6.2. __DO__ use the prefix `set` for setters, but don't use the prefix `get` for getters. Getters are simply named with the name of the property they access. The exception is for accessors of a boolean which may start with the prefix `is`.

      public:
          void setColor(const QColor& c);
          QColor color() const;
          void setDirty(bool b);
          bool isDirty() const;

6.3. __DO__ begin names of slots that are connected to signals with `on`.

6.4. __DO NOT__ add a space between function name and parameter list:

      // Right:
      void setColor(const QColor& c);

      // Wrong:
      void setColor (const QColor& c);

6.5. __DO__ pass each object parameter that is not a basic type (int, float, bool, enum, or pointers) by reference-to-const. This is faster, because it is not required to do a copy of the object. Do that even for object that are already implicitly shared, like `QString`: 

      QString myMethod(const QString& foo,
                       const QPixmap& bar,
                       int number);

6.6. __CONSIDER__ writing functions with six parameters or less. For passing more arguments, try and use `structs` instead, and/or refactor your function.

6.7. __CONSIDER__ using enum values instead of boolean function parameters.

      // Right:
      MessageBox::show("Nice Title", "Nice Text", MessageBox::MESSAGEBOX_BUTTONS_OK)

      // Wrong: Meaning of third parameter is not immediately obvious.
      MessageBox::show("Nice Title", "Nice Text", false)

6.8. __DO__ annotate reimplemented virtual methods with the `Q_DECL_OVERRIDE` macro after the function declaration, just before the `;`. Do not put the `virtual` keyword in the header file.

6.9. __DO NOT__ provide function implementations in header files.


## 7. Variables

7.1. __DO__ use camelCase for variable names. Field names start with an underscore before the first lower-case latter. This way, we can ensure the same public API as Qt.

7.2. __AVOID__ short or meaningless names (e.g. "a", "rbarr", "nughdeget"). Single character variable names are only okay for counters and temporaries, where the purpose of the variable is obvious.

7.3. __DO NOT__ use negative names for boolean variables.

    // Right:
    if (_visible)

    // Wrong: Double negation is hard to read.
    if (!_invisible)

7.4. __DO__ declare each variable on a separate line.

7.5. __DO__ put a single space between the `*` or `&` and the variable name for pointers or references, and don't put a space between the type and `*` or `&`. For us, the fact that we are declaring a pointer or reference variable here much more belongs to the type of the variable than to its name:

      char* x;
      const QString& myString;
      const char* const y = "hello";

7.6. __DO__ test whether a pointer is valid before dereferencing it.


## 8. Enums & Constants

8.1. __CONSIDER__ using `enum class` over `static constexpr` over `static const` variables over `#define` when defining constants.

8.2. __DO__ use ALL_CAPS with underscores between words for constant names.


## 9. Indentation & Whitespaces

9.1. __DO__ use four spaces for indentation.

9.2. __DO__ use a single space after a keyword and before a curly brace.

      // Right:
      if (foo) {
      }

      // Wrong:
      if(foo){
      }

9.3. __DO__ surround binary operators with spaces.

9.4. __DO NOT__ not put multiple statements on one line.


## 10. Line Breaks

10.1. __CONSIDER__ keeping lines shorter than 100 characters; wrap if necessary.

10.2. __DO__ use a new line for the body of a control flow statement:

      // Right:
      if (foo) {
          bar();
      }

      // Wrong:
      if (foo) bar();

10.3. __DO__ start operators at the beginning of the new lines.

      // Right:
      if (longExpression
          + otherLongExpression
          + otherOtherLongExpression) {
      }

      // Wrong: Operator at the end of the line is easy to miss if the editor is too narrow.
      if (longExpression +
          otherLongExpression +
          otherOtherLongExpression) {
      }


## 11. Braces

11.1. __DO__ use attached braces for statements: The opening brace goes on the same line as the start of the statement. If the closing brace is followed by another keyword, it goes into the same line as well:

      // Right:
      if (codec) {
      } else {
      }

      // Wrong:
      if (codec)
      {
      }
      else
      {
      }

11.2. __DO__ have the left brace on the start of a line for class declarations and function definitions:

      static void foo(int g)
      {
          qDebug("foo: %i", g);
      }

      class Moo
      {
      };

11.3. __DO__ use curly braces, even if the body of a conditional statement contains just one line:

      // Right:
      if (address.isEmpty()) {
          return false;
      }

      for (int i = 0; i < 10; +''i) {
          qDebug("%i", i);
      }


      // Wrong: Can lead to subtle bugs in the future, if the body is extended to span multiple statements.
      if (address.isEmpty())
          return false;

      for (int i = 0; i < 10;i)
          qDebug("%i", i);


## 12. Parentheses

12.1. __DO__ use parentheses to group expressions:

      // Right:
      if ((a && b) || c)

      // Wrong: Operator precedence is not immediately clear.
      if (a && b || c)

12.3. __DO NOT__ use spaces after parentheses:

      // Right:
      if ((a && b) || c)

      // Wrong:
      if ( ( a && b ) || c )


## 13. Control Flow

13.1. __DO__ add a `break` (or `return`) statement at the end of every `case`, or a comment to indicate that there's intentionally no `break`, unless another `case` follows immediately within switch statements

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

13.2. __DO NOT__ put `else` after jump statements:

      // Right:
      if (thisOrThat) {
          return;
      }

      somethingElse();


      // Wrong: Causes unnecessary indentation of the whole else block.
      if (thisOrThat) {
          return;
      } else {
          somethingElse();
      }


13.3. __DO NOT__ mix const and non-const iterators.

      // Right:
      for (Container::const_iterator it = c.cbegin(); it != c.cend(); ++it)

      // Wrong: Crashes on some compilers.
      for (Container::const_iterator it = c.begin(); it != c.end(); ++it)


## 14. Language Features

14.1. __CONSIDER__ using the `auto` keyword when it avoids repetition of a type in the same statement, or when assigning iterator types. If in doubt, for example if using `auto` could make the code less readable, do not use `auto`.

      auto* something = new MyCustomType();
      auto* keyEvent = static_cast<QKeyEvent*>(event);
      auto it = myList.const_iterator();

14.2. __DO__ use `auto*` for auto pointers, to be consistent with references, and to add additional guidance for the reader.

14.3. __DO__ use proprietary types, such as `QString` or `QList` where possible. This avoids unnecessary and repeated type conversion while interacting with the Qt framework APIs.


## 15. Exceptions

15.1. __DO__ throw exceptions from model or controller classes to propagate errors to the UI. This is much safer than returning error codes and relying on the caller not to forget to check for them.

15.2. __DO NOT__ throw exceptions from a slot invoked by Qt's signal-slot connection mechanism, as this is considered [undefined behaviour](http://doc.qt.io/qt-5/exceptionsafety.html).


## 16. Comments

16.1. __DO__ add a space after `//`.

16.2. __DO__ place the comment on a separate line, not at the end of a line of code.

16.3. __DO__ write API documentation with [QDoc comments](http://doc.qt.io/qt-5/01-qdoc-manual.html), wrapped in `/*! ... */`.


## 17. Additional Naming Conventions

17.1. __DO NOT__ use any swearing in symbol names, comments or log output.
