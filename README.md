# mqtt

[![CI](https://github.com/lucacasonato/mqtt/workflows/Tests/badge.svg)](https://github.com/lucacasonato/mqtt/actions?workflow=Tests)
[![Code Coverage](https://codecov.io/gh/lucacasonato/mqtt/branch/master/graph/badge.svg?token=QoETPezQp9)](https://codecov.io/gh/lucacasonato/mqtt)
[![GoDoc](https://godoc.org/github.com/lucacasonato/mqtt?status.svg)](http://godoc.org/github.com/lucacasonato/mqtt)

An mqtt client for Go that improves usability over the paho.mqtt.golang library it wraps. Made for 🤖.

## installation

```bash
go get github.com/lucacasonato/mqtt
```

```go
import "github.com/lucacasonato/mqtt"
// or
import (
    "github.com/lucacasonato/mqtt"
)
```

## usage

### creating a client & connecting

```go
client, err := mqtt.NewClient(mqtt.ClientOptions{
    // required
    Servers: []string{
        "tcp://test.mosquitto.org:1883",
    },

    // optional
    ClientID: "my-mqtt-client",
    Username: "admin",
    Password: "***",
    AutoReconnect: true,
})
if err != nil {
    panic(err)
}

err = client.Connect(context.WithTimeout(2 * time.Second))
if err != nil {
    panic(err)
}
```

### disconnecting from a client

```go
client.Disconnect()
```

### publishing a message

#### bytes

```go
err := client.Publish(context.WithTimeout(1 * time.Second), "api/v0/main/client1", []byte(0, 1 ,2, 3), mqtt.AtLeastOnce)
if err != nil {
    panic(err)
}
```

#### string

```go
err := client.PublishString(context.WithTimeout(1 * time.Second), "api/v0/main/client1", "hello world", mqtt.AtLeastOnce)
if err != nil {
    panic(err)
}
```

#### json

```go
err := client.Publish(context.WithTimeout(1 * time.Second), "api/v0/main/client1", []string("hello", "world"), mqtt.AtLeastOnce)
if err != nil {
    panic(err)
}
```

### subscribing

```go
err := client.Subscribe(context.WithTimeout(1 * time.Second), func(message mqtt.Message) {
    fmt.Printf("recieved a message with content %v\n", message.PayloadString())
}, "api/v0/main/client1", mqtt.AtLeastOnce)
if err != nil {
    panic(err)
}
```

### subscribing without listening

```go
err := client.Subscribe(context.WithTimeout(1 * time.Second), nil, "api/v0/main/client1", mqtt.AtLeastOnce)
if err != nil {
    panic(err)
}
```

### listening without subscribing

```go
err := client.Listen(func(message mqtt.Message) {
    v := interface{}{}
    err := message.PayloadJSON(&v)
    if err != nil {
        panic(err)
    }
    fmt.Printf("recieved a message with content %v\n", v)
}, "api/v0/main/client1")
if err != nil {
    panic(err)
}
```
