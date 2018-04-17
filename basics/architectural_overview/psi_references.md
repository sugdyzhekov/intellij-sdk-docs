---
title: PSI References
---

A *reference* in a PSI tree is an object that represents a link from a *usage* of a certain element in the code
to the corresponding *declaration*. *Resolving* a reference means locating the declaration to which a specific usage
refers.

The most common type of references is defined by language semantics. For example, consider a simple Java method:

```java
public void hello(String message) {
    System.out.println(message);
}
```

This simple code fragment contains four references. The references created by the identifiers `System`, `out` and
`println` can be resolved to the corresponding declarations in the JDK: the `System` class, the `out` field and the
`printn` method. The reference created by the second occurrence of the `message` identifier can be resolved to the
`message` parameter, declared by `String message` in the method header.

Note that `String message` is not a reference, and cannot be resolved. Instead, it's a _declaration_. It does not
refer to any name defined elsewhere; instead, it defines a name by itself.

A reference is an instance a class implementing the [`PsiReference`](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java) interface.
Note that references are distinct from PSI elements. You can obtain the references created by a PSI element by calling
`PsiElement.getReferences()`, and can go back from a reference to an element by calling `PsiReference.getElement()`.

To *resolve* the reference - to locate the declaration being referenced - you call `PsiReference.resolve()`. It's very
important to understand the difference between the `getElement()` and `resolve()`. The former method returns the _source_
of a reference, while the second one returns its _target_. In the example above, for the `message` reference, `getElement()`
will return the `message` identifier on the second line of the snippet, and `resolve()` will return the `message` identifier
on the first line (inside the parameter list).

The process of resolving references is distinct from parsing, and is not performed at the same time. Moreover, it is
not always successful. If the code currently open in the IDE does not compile, or in other situations, it's normal
for `PsiReference.resolve()` to return `null`, and if you work with references, you need to be able to handle that in your code.


## Contributed References

In addition to references defined by the semantics of the programming language, IntelliJ IDEA recognizes many references
which are determined by the semantics of the APIs and frameworks used in your code. Consider the following example:

```java
File f = new File("foo.txt");
```

Here, "foo.txt" has no special meaning from the point of view of the Java syntax - it's just a string literal. However,
if you open this example in IntelliJ IDEA, and if you have a file called "foo.txt" in the same directory, you'll notice
that you're able to Ctrl-click on "foo.txt" and navigate to the file. This works because IntelliJ IDEA recognizes the
semantics of `new File(...)` and _contributes a reference_ into the string literal passed as a parameter to the method.

Normally, references can be contributed into elements which don't have their own references, such as string literals
and comments. References are also often contributed into non-code files, such as XML or JSON.

Contributing references is one of the most common ways to extend an existing language. F


A key capability of references is that they can *cross language boundaries*.  