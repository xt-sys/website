+++
title = 'ExectOS Coding Style'
date = 2024-06-18T00:20:07+02:00
+++
This document outlines our coding style guidelines. If you have suggestions for things that should be clarified better,
[contact with us](/contact-us/). With a continuous stream of contributions and patches we would
like the our code base to remain clean and easy to read and maintain.

## General
Each source file should contain a header:
```
/**
 * PROJECT:         ExectOS
 * COPYRIGHT:       See COPYING.md in the top level directory
 * FILE:            path/to/source/file.c
 * DESCRIPTION:     Description of the purpose of the code in this file
 * DEVELOPERS:      Your Name <name@codingworkshop.eu.org>
 */
```
You should only add yourself to the developer list if you are making a significant contribution, which means you
understand the purpose of the code in this file and you can provide support to anyone.

## Indentation and whitespace
 * Set your editor to __4 spaces per tab; never use tabs__.
 * Do not add a space or tab at the end of any line.
 * Indent both a case label and the case statement of the switch statement.
 * When wrapping a too long line, add indentation to match the function call parameter list or expression.
 * Always separate operators with a space on both sides, except after C-style cast operator.
 * Always use a space after comma.
 * A line must not have more than __120 characters__.
```
switch(Condition)
{
    case 1:
        CallFunction();
        break;
    default:
        CallAnotherFunction();
        break;
}

CallOtherFunction(Argument1, Argument2, Argument3,
                  Argument4, Argument5);

Pointer = (PVOID)Address;
```

## Miscellaneous formatting
 * Include exactly one empty line between header and `#include` directives.
 * Put exactly two empty lines after `#include` directives.
 * Include single newline at every file end.

## Identifiers
 * Always use self-describing well chosen identifiers.
 * Use full names such as `Message` over abbreviations such as `Mesg`.
 * Function names and variables start with uppercase letters and use CamelCaseFormatting.
 * Classes, structures, unions and type definitions are UPPERCASE and use underscore.
```
typedef char BOOL;

typedef struct _NEW_STRUCTURE
{
    BOOL Enabled;
    PVOID Pointer;
} NEW_STRUCTURE, *PNEW_STRUCTURE;

XTAPI
VOID
KeStartKernel();
```

## Variable declarations
 * Always declare all variables at the beginning of the function.
 * Use descriptive names, avoid reusing a single variable for different purposes.
 * Always use XT API types over defined in C standard.

## Comments
 * Always comment your code properly.
 * Use only C-style comments.
 * Comment over and under the commented line to avoid producing long lines.
 * Do not annotate your comments with your name or initials.
 * Avoid expressing sentiments, instead explain why you consider the respective code a hack.
```
/* This is a C-style comment */

/* This is a valid
 * multi-line comment */
```

## Documentation
 * We use JavaDoc style comments for API documentation.
 * The comment separators `/**` and `*/` must be present.
 * Every line must be indented.
```
/**
 * Multi-line routine description.
 *
 * @param Parameter1
 *        Description of the first parameter goes here.
 *
 * @param Parameter2
 *        Description of the seconds parameter goes here.
 *
 * @return Short description about return values.
 *
 * @since Information from which version a given function is available.
 *
 * @see Optional link to documentation or reference implementation.
 *
 * @todo Optional description, telling what is missing in the function implementation.
 */
```

## Miscellaneous
 * Use NULLPTR instead of 0 for pointers.
 * Avoid using assignments in while loops.
 * Never use goto statements.
 * Never use spaces around unary operators.
 * Always put braces (`{` and `}`) on their own lines.
 * One-line control clauses should use braces as well.
 * Do not use reverse logic in control clauses.
 * Do not use spaces around unary operators, before comma and semicolon, or between control statements and their
   parentheses.
