# RUST QUICK REFERENCE / RUST CHEATSHEET

## Hello, Rust!

```rs
fn main() {
    println!("hello, rust");
}
```

### Data Structures

Data types and memory locations defined via keywords.

```rs
struct S {}  // Define a struct with named fields.
    struct S { x: T }  // Define struct with named field x of type T.
    struct S (T);      // Define "tupled" struct with numbered field .0 of type T.
    struct S;          // Define zero sized unit struct. Occupies no space, optimized away.
enum E {}    // Define an enum, c.algebraic data types, tagged unions.
    enum E { A, B(), C {} }   // Define variants of enum; can be unit-A, tuple-B() and struct-like C{}.
    enum E { A = 1 }          // If variants are only unit-like, allow discriminant values, e.g., for FFI.
    enum E {}                 // Enum w/o variants is uninhabited, can't be instantiated, c. 'never'
union U {}   // Unsafe C-like union for FFI compatibility.
static X: T = T();     // Global variable with 'static' lifetime, single memory location.
const X: T = T();      // Defines constant, copied into a temporary when used.
let x: T;              // Allocate T bytes on stack bound as x. Assignable once, not mutable.
let mut x: T;          // Like let, but allow for mutability and mutable borrow.
    x = y;             // Moves y to x, invalidating y if T is not Copy, and copying y otherwise.
```

Creating and accessing data structures; and some more _sigilic_ types.

```rs
S { x: y }      // Create struct S {} or use'ed enum E::S {} with field x set to y.
S { x }         // Same, but use local variable x for field x.
S { ..s }       // Fill remaining fields from s, esp. useful with Default.
S { 0: x }      // Like S(x) below, but set field .0 with struct syntax.
S(x)            // Create struct S(T) or use'ed enum E::S() with field .0 set to x.
S               // If S is unit struct S; or used'ed enum E::S create value of S.
E::C { x: y }   // Create enum variant C. Other methods above also work.
()              // Empty tupe, both literal and type, aka unit.
(x)             // Parenthesized expression.
(x,)            // Single-element tuple expression.
(S,)            // Single-element tuple type.
[S]             // Array type of unspecified length, i.e., slice. Can't live on stack.
[S; n]          // Array type of fixed length n holding elements of type s.
[x; n]          // Array instance (expression) with n copies of x.
[x, y]          // Array instance with given elements x and y.
x[0]            // Collection indexing, here w. usize. Implementable with Index, IndexMut.
    x[..]       // Same, via range (here full range), also x[a..b], x[a..=b], ..c. below.
a..b            // Right-exclusive range creation, e.g., 1..3 means 1, 2.
..b             // Right-exclusive range to without starting point.
..=b            // Inclusive range to without starting point.
a..=b           // Inclusive range, 1..=3 means 1, 2, 3.
a..             // Range from without ending point.
..              // Full range, usually means the whole collection.
s.x             // Named field access, might try to Deref if x not part of type S.
s.0             // Numbered field access, used for tuple types S(T).
```

### References & Pointers

Granting access to un-owned memory. Also see section on **Generics & Constraints**.
```rs
&S              // Shared reference (type; space for holding any &s).
    &[S]        // Special slice reference that contains (address, length).
    &str        // Special string slice reference that contains (address, length).
    &mut S      // Exclusive reference to allow mutability (also &mut [S], &mut dyn S, ...).
    &dyn T      // Special trait object reference that contains (address, vtable).
&s              // Shared borrow (e.g., address, len, vtable, ... of this s, like 0x1234).
    &mut s      // Exclusive borrow that allows mutability.
*const S        // Immutable raw pointer type w/o memory safety.
    *mut S      // Mutable raw pointer type w/o memory safety.
    &raw const s    // Create raw pointer w/o going through reference; c.ptr:addr_of!()
    &raw mut s      // Same, but mutable. Raw ptrs. are needed for unaligned, packed fields.
ref s           // Bind by reference, makes binding reference type.
    let ref r = s;      // Equivalent to let r = &s.
    let S { ref mut x } = s;    // Mutable ref binding (let x = &mut s.x), shorthand destructuring version.
*r              // Dereference a reference r to access what it points to.
    *r = s;     // If r is a mutable reference, move or copy s to target memory.
    s = *r;     // Make s a copy of whatever r references, if that is Copy.
    s = *r;     // Won't work if *r is not Copy, as that would move and leave empty place.
    s = *my_box;    // Special case for Box that can also move out b'ed content that isn't Copy.
'a              // A lifetime parameter, duration of a flow in static analysis.
    &'a S       // Only accepts address of some s; address existing 'a or longer.
    &'a mut S   // Same, but allow address content to be changed.
    struct S<'a> {} // Signals this S will contain address with lifetime 'a. Creator of S decides 'a.
    trait T<'a> {}  // Signals any S, which impl T for S, might contain address.
    fn f<'a>(t: &'a T)  // Signals this function handles some address. Caller decieds 'a.
'static         // Special lifetime lasting the entire program execution.
```

### Functions & Behavior

Define uints of code and their abstractions.

```rs
trait T {}          // Define a trait; common behavior types can adhere to.
trait T : R {}      // T is subtrait of supertrait R. Any s must impl R before it can impl T.
impl S {}           // Implementation of functionality for a type S, e.g., methods.
impl T for S {}     // Implement trait T for type S; specifies how exactly S acts like T.
```
