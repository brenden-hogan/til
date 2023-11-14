Assertables has everything you need: https://docs.rs/assertables/latest/assertables/

Basic examples:
```rust
assert_eq!(value1, value2); // value1 = value2
assert_gt!(value1, value2); // value1 ≥ value2
assert_starts_with!(string1, string2); // string1 starts with string2
assert_is_match!(regex, string); // regex is match of string
assert_set_subset!(vector1, vector2); // vector1 as set ⊆ vector2 as set
assert_fn_ok_eq!(function1, function2); // function1 ok = function2 ok
assert_read_to_string_eq!(reader1, reader2); // reader1 as string = reader2 as string
assert_command_stdout_eq!(command1, command2); // command1 standard output = command2 standard output
```