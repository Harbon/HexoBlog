---
  title: Rust OwnerShip
  tags: notes
  categories:
  - Rust
---

###  如果一个变量有被引用，那么在引用被释放之前，变量不可释放，示例如下：
```
fn eat_box(boxed_int: Box<i32>) {
    println!("Destroying box that contains {}", boxed_int);
}
fn main() {
    // Create a boxed integer
    let boxed_int = Box::new(5);

    {
        // Take a reference to the data contained inside the box
        let _ref_to_int: &i32 = &boxed_int;

        // Error!
        // Can't destroy `boxed_int` while the inner value is borrowed.
        eat_box(boxed_int);
        // FIXME ^ Comment out this line

        // `_ref_to_int` goes out of scope and is no longer borrowed.
    }

    // Box can now give up ownership to `eat_box` and be destroyed
    eat_box(boxed_int);
}
```

###  如果一个 mutable 的变量被一个不可 mutable 的引用引用，那么在引用被释放之前，变量不可更改。示例代码如下：
```
fn main() {
    let mut _mutable_integer = 7i32;

    {
        // Borrow `_mutable_integer`
        let _large_integer = &_mutable_integer;

        // Error! `_mutable_integer` is frozen in this scope
        _mutable_integer = 50;
        // FIXME ^ Comment out this line

        // `_large_integer` goes out of scope
    }

    // Ok! `_mutable_integer` is not frozen in this scope
    _mutable_integer = 3;
}
```


###  immutable 引用和 mutable引用不可同时存在，mutable 引用只可存在一次，immutable 引用可多次存在，示例代码如下：
```
struct Point { x: i32, y: i32, z: i32 }

fn main() {
    let mut point = Point { x: 0, y: 0, z: 0 };

    {
        let borrowed_point = &point;
        let another_borrow = &point;

        // Data can be accessed via the references and the original owner
        println!("Point has coordinates: ({}, {}, {})",
                 borrowed_point.x, another_borrow.y, point.z);

        // Error! Can't borrow point as mutable because it's currently
        // borrowed as immutable.
        //let mutable_borrow = &mut point;
        // TODO ^ Try uncommenting this line

        // Immutable references go out of scope
    }

    {
        let mutable_borrow = &mut point;

        // Change data via mutable reference
        mutable_borrow.x = 5;
        mutable_borrow.y = 2;
        mutable_borrow.z = 1;

        // Error! Can't borrow `point` as immutable because it's currently
        // borrowed as mutable.
        //let y = &point.y;
        // TODO ^ Try uncommenting this line

        // Error! Can't print because `println!` takes an immutable reference.
        //println!("Point Z coordinate is {}", point.z);
        // TODO ^ Try uncommenting this line

        // Mutable reference goes out of scope
    }

    // Immutable references to point are allowed again
    println!("Point now has coordinates: ({}, {}, {})",
             point.x, point.y, point.z);
}
```
