# Function Options

**[design pattern]**

## Problem:

- multiple similar constructor functions
- lack of default values

## Goal:

- one constructor function
- multiple options but only few heavily used
- default values are well known and safe

## Example:

```go
package main

import "fmt"

type (
	Option func(cf *Config) error
	Config struct {
		Module  string
		TraceId string
	}
)

func ModuleName(name string) Option {
	return func(cf *Config) error {
		if name == "" {
			return fmt.Errorf("name must not be empty")
		}
		cf.Module = name
		return nil
	}
}

func TraceId(uuid string) Option {
	return func(cf *Config) error {
		if uuid == "" {
            return fmt.Errorf("uuid must not be empty")
        }
		cf.TraceId = uuid
		return nil
	}
}

func NewConfig(opts... Option) *Config {
	conf := &Config{ // config with default values
		Module: "main",
		TraceId: "000",
	}
	for _, opt := range opts {
		if err := opt(conf); err != nil {
			fmt.Printf("Option error: %v", err)
		}
	}
	return conf
}

func main() {
	// without options (only default values)
	NewConfig()

    // with one option
	NewConfig(ModuleName("test module"))
	NewConfig(TraceId("67d483df-8770-4a06-9a93-371c787e5b5a"))

    // multiple options
	NewConfig(
		ModuleName("test module"),
		TraceId("67d483df-8770-4a06-9a93-371c787e5b5a"),
	)
}
```

## Side notes

### Packages

- gomock

### Patterns

- Builder
- Null-object
