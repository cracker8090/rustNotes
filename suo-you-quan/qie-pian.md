# 切片

\&str

字符串切片的边界必须位于有效的UTF-8字符边界内

```rust
fn first_world_slice(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..i];
        }
    }
    &s[..]
}

    let my_string = String::from("Hello world");
    let word_index = first_world_slice(&my_string);

    let my_string_literal = "hello world";
    let word_index = first_world_slice(my_string_literal);
```
