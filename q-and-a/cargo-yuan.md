# cargo源

当前用户目录下 .cargo文件夹，建立config文件

```
cd ~/.cargo
touch config
vi config
```



```
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"

replace-with = 'tuna'
[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"


[net]
git-fetch-with-cli = true
```

