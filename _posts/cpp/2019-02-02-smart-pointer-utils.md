---
layout: post
title: Smart pointers utilities
category: C++
permalink: /django/:year/:month/:day/:title/
---

# Smart pointers

C++ supports many types of smart pointers
    * auto_ptr
    * unique_ptr
    * shared_ptr
    * weak_ptr

### auto_ptr
    - is deprecated. don't recommend the use. (it replaced by 'unique_ptr')
    - takes a raw pointer, wraps it, and ensures the memory pointed by the raw pointer is released back whenever the 'auto_ptr' object goes out of scope.
    - At any time, only one 'auto_ptr' smart pointer can point to an object.
    - Whenever one 'auto_ptr' pointer is assigned to another 'auto_ptr' pointer, the ownership gets transferred to the 'auto_ptr' instance that has received the assignment; the same happens when an 'auto_ptr' is copied.

### unique_ptr
    - This smart pointer works in exactly the same way as 'auto_ptr', except that 'unique_ptr' address the issues introduced by 'auto_ptr'
    - 'unique_ptr' is a replacement of 'auto_ptr', starting from C++11
    - this allows only one smart pointer to exclusively own a heap-allocated object.
    - This ownership transfer from one 'unique_ptr' instance to another can be done only via the 'std::mvoe()' function.

### shared_ptr
    - This is used when a group of 'shared_ptr' objects shares the ownership of a heap allocated object.
    - The 'shared_ptr' pointer releases the shared object when all the 'shared_ptr' instances are done with the use of the shared object.
    - The 'shared_ptr' pointer uses the reference counting mechanism to check the total references to the shared object;
      whenever the reference count becomes zero, the last 'shared_ptr' instance deletes the shared object.

### weak_ptr
    - 'shared_ptr' fails to clean up the memory when there is a circular dependency in the application design.
      Either the application design must be refactored to avoid cyclic dependency, or we can maek use of 'weak_ptr' to resolve the cyclic dependency issue.


## Utilities
    * owner_less
    * enable_shared_from_this
    * bad_weak_ptr
    * default_delete

### owner_less
    - this helps compare two or more smart pointers if they share the same raw pointed object.

### enable_shared_from_this
    - this helps get a smart pointer of the this pointer.

### bad_weak_ptr
    - this is an exception class that implies that "shared_ptr" was created using an invalid smart pointer.

### default_delete
    - this refers to the default destruction policy used by 'unique_ptr', which invokes the 'delete' statement, while partial specialization for array types that use 'delete[]' is also supported.


## References
    * book: Mastering C++ programming, Packt, [URL](https://subscription.packtpub.com/book/application_development/9781786461629/4/ch04lvl1sec21/smart-pointers)

