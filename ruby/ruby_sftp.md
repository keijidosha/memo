# Ruby: sftp

## 準備

`gem install net-sftp`

## 実装

```
Net::SFTP.start("192.168.1.1", "user", keys: ["/hoge/user/.ssh/privatekey"], passphrase: "password") { |sftp|
  # download
  sftp.download("/tmp/readme.txt", "/home/user/readme-local.txt")

  # rename
  ret = sftp.rename("/tmp/123.txt", "/tmp/456.txt")

  ret = sftp.remove("/tmp/456.txt")

  # files list(1)
  # glob は、第2パラメータに該当するファイルを、配下のディレクトリに渡ってサーチするため、
  # 対象ディレクトリ配下の階層が深い場合は時間がかかるので注意が必要
  # 後述の entries, foreach は、指定されたディレクトリ直下だけが対象となり、再帰的にサーチを行わない
  sftp.dir.glob("/tmp", "*.txt") { |entry|
    puts entry.name}
  }

  # files list(2)
  sftp.dir.entries("/tmp").map { |entry|
    puts entry.name}
    puts entry.longname
    puts "file" if entry.file?
    puts "directory" if entry.directory?
    puts "symlink" if entry.symlink?
    puts Time.at(entry.attributes.mtime)}
  }

  # files list(3)
  sftp.dir.foreach("/tmp") { |entry|
     puts entry.name}
    puts entry.longname
    puts "file" if entry.file?
    puts "directory" if entry.directory?
    puts "symlink" if entry.symlink?
    puts Time.at(entry.attributes.mtime)}
  }
}
```

