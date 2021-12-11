![badge](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/glebarez/fb4d23f63d866b3e1e58b26d2f5ed01f/raw/badge-gorm-tests.json)
![badge](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/glebarez/fb4d23f63d866b3e1e58b26d2f5ed01f/raw/badge-sqlite-version.json)

# Pure-Go GORM Sqlite driver
Pure-go (without cgo) implementation of SQLite driver for [GORM](https://gorm.io)<br>

## How is this better than standard GORM SQLite driver?
The [standard GORM driver for SQLite](gorm.io/driver/sqlite) has one major drawback: it is based on a [Go-bindings of SQLite C-source](https://github.com/mattn/go-sqlite3) (this is called [cgo](https://go.dev/blog/cgo])). That fact imposes following restrictions on Go developers:
- to build and run your code, you will need a C compiler installed on a machine
- SQLite has many features that need to be enabled at compile time (e.g. [json support](https://www.sqlite.org/json1.html)). If you are using those features, you will need to include proper go build tags for every go command to work properly (go run, go test, etc.). Such tweaks may be easy to forget / hard to achieve (e.g. in automated environments like universal CI pipelines for Go)
- Because of C-compiler requirement, you can't build your Go code inside tiny stripped containers like (golang-alpine)


# FAQ
### Is this tested good ?
Yes, The CI pipeline of this driver employs [whole test base](https://github.com/go-gorm/gorm/tree/master/tests) of GORM, which includes more than **12k** tests (see badge on the page-top). Tests are conducted with latest major release of Go (1.17) in following environments:
- Linux
- Windows
- MacOS

### SQLite is written in C, why don't we need a cgo ?
This driver is based on pure-Go implementation of SQLite (https://gitlab.com/cznic/sqlite), which is basically an original SQLite C-source AST, translated into Go! So, you may be sure you're using the original SQLite implementation under the hood.

### Is JSON feature of SQLite enabled?
Yes!


# Usage

```go
import (
  "github.com/glebarez/sqlite"
  "gorm.io/gorm"
)

db, err := gorm.Open(sqlite.Open("sqlite.db"), &gorm.Config{})
```

### In-memory DB example
```go
db, err := gorm.Open(sqlite.Open(":memory:"), &gorm.Config{})
```

### Foreign-key constraint activation
Foreign-key constraint is disabled by default in SQLite. To activate it, use connection URL parameter:
```go
db, err := gorm.Open(sqlite.Open(":memory:?_pragma=foreign_keys(1)"), &gorm.Config{})
```
More info: [https://www.sqlite.org/foreignkeys.html](https://www.sqlite.org/foreignkeys.html)

