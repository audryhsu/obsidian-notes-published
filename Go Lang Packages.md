---
creation date: 2022-03-18 11:17
aliases: 
tags: 🖥️
---

# [Go Lang Packages](Go%20Lang%20Packages.md)
---
 A **package** is a collection of source files in the same directory that are compiled together. Functions, types, variables, and constants defined in one source file are visible to all other source files within the same package. This is the lowest level unit.

A **repository** contains one or more modules. A **module** is a collection of related Go packages that are released together. Repositories typically contain one module at the root of the repository. `go.mod` (like a `package.json`) declares the module path (the import path prefix for all packages within the module). 

Import path is the string used to import a package at the top of a go file. This path is the module path + subdirectory within the module.

### Local Packages
Below is the `shopping` package, created under `$GOPATH/src/shopping`

The *name of the package* must be the same *name as the folder* (shopping package in shopping folder, db package in db folder, etc.)

```js
 src
└── shopping
	├── db
	│   └── db.go
	├── main
	│   └── main.go
	└── pricecheck.go
```

When you name a package, via the `package` keyword, you provide a single value (e.g., “shopping” or “db”), not a complete hierarchy.
```go
package db //must reside in the db folder

type Item struct {
	Price float64
}

func LoadItem(id int) *Item {
	return &Item{
		Price: 9.001,
	}
}
```

However when you import a package, you specify the complete path. This is saying "import package from the db folder >shopping folder > inside of `src` folder"
```go
package shopping

import (
	"shopping/db"
)

func PriceCheck(itemId int) (float64, bool) {
	item := db.LoadItem(itemId)
	if item == nil {
		return 0, false
	}

	return item.Price, true
}
```

##### Cyclical Imports
To avoid cylical imports, it's common convention to place shared packages in a `utilities` folder. These shared packages shouldn't import anything from the main app package or sub-packages to avoid tangling dependencies.

##### Visibility
Note that there is no "export" syntax at the bottom of packages. 
- Functions or structs that start with uppercase letter are visible outside of the package, and can be called using method syntax e.g. `package.Function()`
	- this INCLUDES the property names inside of structs!
- lowercased functions/structs are local only to the package
```go
package models

type Item struct {
	price float64 // unknown field 'Price' in struct literal of type models.Item
	SKU int // this is visible outside of models
}
```

### Importing External Packages
- We run  `go get github.com/mattn/go-sqlite3` in bash to download latest version of a third party libraries. It will create a `github.com` folder within `src` containing `mattn/go-sqlite` repository.

```go
src
├── github.com
│   └── mattn
│       └── go-sqlite3
│           ├── //... tons of more files
└── shopping
    ├── db
    │   └── db.go
    ├── main
    │   └── main.go
    ├── models
    │   └── item.go
    └── pricecheck.go

```

Running `go get` within a project will scan all of the files, looking for `imports` to third party libraries, and download them for you, like a handy `package.json` file! 
- Note that `go get` fetches the master/default version, so you can't specify a particular version yet. Use a third party package manager like goop or godep.
- `go get <url_path_to_package/@v1` - download latest available version with major relase number 1
- `go.sum` contains checksums representing content of required packages; `go mod verify` verifies that checksums of downloaded packages match entries in `go.sum`


> [!NOTE]  `go.mod` makes it easier to create reproducible builds for Go apps
> - `go mod download` can be run to download exact versions of all packages for project
> - `go mod verify` ensures nothing in downloaded packages has been changed unexpectedly
> - Whenever `go run`, `go build`, `go test` are run, the exact package versions lists in `go.mod` will be used.
> - `go mod tidy -v` removes unused packages from `go.mod` and `go.sum` if all references are removed from code


---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) 

Reference: https://github.com/golang/go/wiki/Modules

Related: 