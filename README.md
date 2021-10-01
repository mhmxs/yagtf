# yagtf
Yet Another Go Test Framework

## Project goal

This Go testing framework aims to help you reduce code smell in any Go code base.
Sounds magic? Exactly not, and here is the reason. If you feel pain in your back
during test writing by using this framework, your code may doesn't follow some of the SOLID
principles for example: `Single Responsibility Principle`, `Interface Segregation Principle` or `Dependency Inversion Principle`.

## How to use the frmework

The only thing you have to do after you have removed all other testing framework, except assertion libraries, because Yagtf works well with them, is import `Yet Another Go Test Framework`.

```
import _ "github.com/mhmxs/yagtf/pkg/framework"
```

### Examples

Here is a piece of code to test.
```
type someSideEffect interface {
    Get(string) (string, error)
}

func someFunction(se someSideEffect) string {
    data, err := se("foo")
    // Business logic
    return "bar"
}
```

#### Example of mocking

Testing of `someFunction` is really easy with Yagt.

```
import (
    "testing"
    _ "github.com/mhmxs/yagtf/pkg/framework"
)

type mockSideEffect struct{}

func (ms mockSideEffect) Get(string) (string, error) {
    return "some data", nil
}

func TestSomeFunction(t *testing.T) {
    if "bar" != someFunction(mockSideEffect{}) {
        t.Error("Return value is not expected")
    }
}

```

#### Example of spying

Testing of `Get` function parameter is really easy with Yagt.

```
import (
    "testing"
    _ "github.com/mhmxs/yagtf/pkg/framework"
)

type mockSideEffect struct{
    param chan string
}

func (ms mockSideEffect) Get(param string) (string, error) {
    ms.param <- param
    return "some data", nil
}

func TestSomeFunction(t *testing.T) {
    mock := mockSideEffect{param: make(chan string, 1)}
    defer close(mock.param)

    if "bar" != someFunction(mockSideEffect{}) {
        t.Error("Return value is not expected")
    }
    if "foo" != <-ms.param {
        t.Error("Parameter value is not expected")
    }
}

```