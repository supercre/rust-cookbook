# 연습문제 2

str , String 차이를 이해 해봅시다. 

```rust,editable

fn str_print( s: str ) {
    println!("{s}");
}

fn string_print( s: String ) {
    println!("{s}");
}

fn main() {
    let s1 = "Rust is great!";
    let s2 = s1.to_string();

    str_print(s1);
    string_print(&s2);
}
```