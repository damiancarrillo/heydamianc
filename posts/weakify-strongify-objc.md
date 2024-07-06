---
title: Weakifying and strongifying block variables in Objective-C
date: '2024-07-06'
draft: false
tags:
  - objective-c
comments: {}
---
When using blocks (or closures) in Objective-C, it is often necessary to make a variable reference `weak` so as not to introduce a memory cycle with the variables that are used in the lexical scope of the block. As far as I know, [libextobjc](https://github.com/jspahrsummers/libextobjc) (which is now archived) is the library that popularized the terms "weakify" and "strongify" for referencing a weakly-held pointer to an object outside of the block, then capturing it strongly again inside.

Here is how you do it without using `libextobjc`:

```
typeof(self) __weak weakSelf = self;
someClosure = ^(NSString * _Nonnull someVariable) {
    typeof(weakSelf) __strong strongSelf = weakSelf;
    [strongSelf doSomethingWith:someVariable];
};
```

`__weak` and `__strong` are called [Ownership Qualifiers](https://clang.llvm.org/docs/AutomaticReferenceCounting.html#ownership-qualification). These tell [ARC](https://clang.llvm.org/docs/AutomaticReferenceCounting.html) how to treat the variables to not introduce a reference cycle. This can't be automated, thus the need to specify ownership based on the usage.

`typeof(self)` produces the type of `self` but is a nonstandard C extension. You may also see `__typeof(self)` or even `__typeof__(self)`. The extension is discussed in the GCC documentation [here](https://gcc.gnu.org/onlinedocs/gcc/Typeof.html).

Hopefully this small tip helps you write better code!
