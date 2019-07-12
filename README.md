# parquet_derive

Writing Parquet files in Rust sucks a lot right now. There are some PRs on the apache/arrow repository which would improve things but unfortunately things aren't moving too fast..

This repository contains none of my own work, it's just an implementation of https://github.com/apache/arrow/pull/4140 which can be used with the latest `parquet-rs` release.

### Usage

```rust
// This trait must exist in your crate
use parquet::file::writer::RowGroupWriter;
trait RecordWriter<T> {
    fn write_to_row_group(&self, row_group_writer: &mut Box<dyn RowGroupWriter>);
}

use parquet_derive::ParquetRecordWriter;

#[derive(ParquetRecordWriter)]
struct MyStruct {
    name: String,
    age: u8
}

// Initialize your parquet file
let mut writer = SerializedFileWriter::new(file, schema, props).unwrap();
let mut row_group = writer.next_row_group().unwrap();

// Build up your records
let chunks = vec![MyStruct{...}];

// The derived `RecordWriter` takes over here
(&chunks[..]).write_to_row_group(&mut row_group);

writer.close_row_group(row_group).unwrap();
writer.close().unwrap();
```

### License

All work is under the same license as the Apache Arrow repository.