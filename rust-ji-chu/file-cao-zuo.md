# File操作

File::create：等价于 OpenOptions::new().write(true).create(true).truncate(true).open(path.as\_ref())

File::open：只读打开

fs::create\_dir\_all：递归创建

fs::remove\_dir\_all：删除目录下所有

fs::remove\_dir：删除空目录

read：读取流

read\_to\_string：读取成字符串

```rust
use std::{fs::{File, self, OpenOptions}, io::{Read, Seek, SeekFrom, Write}};

fn main() -> std::io::Result<()>{
    let _f = File::create("test666.txt")?;
    fs::remove_file("test666.txt")?;
    // fs::create_dir("tmp1/abc")?;
    fs::create_dir_all("tmp1/aaa/bbb/ccc")?;
    // fs::remove_dir("tmp1")?;
    // fs::remove_dir("tmp")?;
    // fs::remove_dir_all("tmp")?;
    // fs::remove_file("test.txt")?;

    // only read, error when file no exist
    // let _file = File::open("test.txt")?;

    // creat new file,open when file exist
    // let mut _f = OpenOptions::new().read(true).write(true).create(true).open("test.txt")?;

    // append
    // let _file = OpenOptions::new().append(true).open("test.txt")?;

    // truncate clear file
    // let _file = OpenOptions::new().write(true).truncate(true).open("test.txt")?;

    // let mut f = File::open("test.txt")?;
    let mut f = OpenOptions::new().read(true).write(true).create(true).open("test.txt")?;
    // let mut buffer = [0;10];
    // let n = f.read(&mut buffer[..])?;
    // println!("The bytes: {:?}",&buffer[..n]);

    // f.seek(SeekFrom::Start(10))?;
    // f.seek(SeekFrom::End(-10))?;
    // f.seek(SeekFrom::Current(-10))?;
    let mut buf = String::new();
    // error when no utf-8
    f.read_to_string(&mut buf)?;
    println!("The buffer string: {:?}",buf);

    f.write(b"00write bytes")?;
    // f.write_all(buf)
    f.flush()?;

    Ok(())
}

```
