	---
tags: golang
aliases: 
---
Tags: [Go Lang](./Go%20Lang.md) [Book Let's Go](./Book%20Let's%20Go.md)
References:

---
# controlling which tests run

All tests in current project using wildcard
```bash
go test ./.. 
```

By specific package and verbose flag
```bash
go test -v ./cmd/web // path to package
```

Specific tests using `-run` flag + regex
```bash
# pass test function name to run flag, followed by package location
go test -v -run="^TestPing$" ./cmd/web/

# skip a specific test
go test -v -skip="^TestPing$" ./cmd/web
```

Stop tests in package after a failure
```bash
go test -failfast {dir path to package}
```

Mark a test to run in parallel **with and only with** other parallel tests.
```go
func TestPing(t *testing.T) {
	t.Parallel()
	//...
}
```
You can override the setting that sets the max number of tests to run in parallel (value of `GOMAXPROCS` variable) using `parallel flag`
```bash
$ go test -parallel 4 ./...
```

Enable race detector when running tests to help flag race conditions that exist in app
```bash
go test -race ./...
```

## skip long-running tests

Skip long running tests by using the `testing.Short()` function to check for presence of `-short` flag

```go
TestThatRunsLong(t *testing.T) {
	if testing.Short() {
		t.Skip("skipping this long running test") // tell test runner to skip the test
	}
}
```
```shell
$ go test -v -short
```

## profiling test coverage
Shows the % of statements covered by tests within *each package*
```shell
# summary of coverage
$ go test -cover ./...

# test coverage by method and function saved to a temp location
$ go test -coverprofile=/tmp/profile.out ./...
# get even crazier and record number of times each statement is executed!
$ go test -covermode=count -coverprofile=/tmp/profile.out ./...

# view the coverage profile in terminal OR in html
$ go tool cover -func=/tmp/profile.out
$ go tool cover -html=/tmp/profile.out


```

### Mock app dependencies
Goal:
- mock the `models.SnippetModel` and `models.UserModel` database models to test handlers without setting up a test mySQL instance
