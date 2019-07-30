# tee_readwrite

## tee_readwrite
This module allows you to mirror read/writes to another read/write (like POSIX tee)

## Examples
### TeeReader
```rust
use tee_readwrite::{TeeReader, TeeWriter};
// make a new reader
let reader = std::io::Cursor::new(vec![1,2,3]);
let mut tee = TeeReader::new(
    reader,
    vec![], // vec implements write
    false   // we don't care about flushing here
);

// read all of the elements from the cursor into this vec
// each 'read' call will be written to the wrapped writer
let mut results = vec![];
assert_eq!(tee.read_to_end(&mut results).expect("read"), 3);

// consume the tee, returning the reader and the mirroring writer
let (_read, output) = tee.into_inner();
assert_eq!(results, output);
```

### TeeWriter
```rust
use tee_readwrite::{TeeReader, TeeWriter};
let writer = vec![];
let mut tee = TeeWriter::new(writer, vec![]);
for i in 1..=3 {
    let _ = tee.write_all(&[i]);
}
// we can borrow the output writer
assert_eq!(tee.borrow_output(), &[1,2,3]);

// consume the tee, returning the writer and its tee output
let (left, output) = tee.into_inner();
assert_eq!(left, output);
assert_eq!(output, &[1,2,3]);
```

License: 0BSD
