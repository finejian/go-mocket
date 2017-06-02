
### Go-Mocket

Go-Mocket is library inspired by [DATA-DOG/go-sqlmock](https://github.com/DATA-DOG/go-sqlmock)
As inspiration library it is implementation of [sql/driver](https://godoc.org/database/sql/driver) interface but at the same time follows different approaches and has only similar API.
This library helps to mock any DB connection also with [jinzhu/gorm](https://github.com/jinzhu/gorm) and it was main goal to create it

List of features in the library:

* Mock `INSERT`, `UPDATE`, `SELECT`, `DELETE`
* Support of transactions
* 2 API's to use - `chaining` and via specifying whole mock object
* Matching by prepared statements arguments
* You will not require to change anything inside you code to start using this library
* Ability to trigger exceptions
* Attach callbacks to mocked response to add additional check or modify response

**NOTE** Please be aware that driver catches SQL without DB specifics. Generating of queries is done by *sql* package

#### Install

```
go get github.com/selvatico/go-mocket
```

#### Usage

There are two possible ways to use `mocket`:

* Chaining API
* Specifying `FakeResponse` object with all fields manually. Could be useful for

##### Enabling driver

Somewhere in you code to setup a tests

```go
import (
    "database/sql"
    mocket "github.com/selvatico/go-mocket"
    "github.com/jinzhu/gorm"
)

func SetupTests() {
    sql.Register("fake_test", mocket.FakeDriver{})
    // GORM
    db, err := gorm.Open("fake_test", "connection_string") // Could be any connection string
    app.DB = db

    // Regular sql package usage
    db, err := sql.Open(driver, source)
}
```

Now if use singleton instance of DB it will use everywhere mocked connection.

##### Chain usage

###### Example of mocking by pattern

```go
import mocket "github.com/selvatico/go-mocket"
import "net/http/httptest"

func TestHandler(t *testing.T) {
    request := httptest.NewRequest("POST", "/application", postBody)
    recorder := httptest.NewRecorder()

    GlobalMock := mocket.Catcher
    GlobalMock.Logging = true // log mocket behavior

    commonReply := []map[string]interface{}{{"id": "2", "field": "value"}}
    // Mock only by query pattern
    GlobalMock.NewMock().WithQuery(`"campaigns".name IS NULL AND (("uuid" = test_uuid))`).WithReply(commonReply)
    Post(recorder, request) // call handler

    r := recorder.Result()
    body, _ := ioutil.ReadAll(r.Body)

    // some assertion about results
    //...

}

```


Documentation in progress....


