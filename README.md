httpzaplog
=======

A fork of [go-chi/httplog](https://github.com/go-chi/httplog) that uses [uber-go/zap](https://github.com/uber-go/zap)
for logging instead of `zerolog`.

## Example

(see [_example/](./_example/main.go))

```go
package main

import (
	"net/http"

	httpzaplog "github.com/illumitacit/httpzaplog"
	"github.com/go-chi/chi/v5"
	"github.com/go-chi/chi/v5/middleware"
	"go.uber.org/zap"
)

func main() {
	// Logger
	opts := &httpzaplog.Options{
		Logger:  zap.Must(zap.NewProduction()),
		Concise: true,
	}

	// Service
	r := chi.NewRouter()
	r.Use(httpzaplog.RequestLogger(opts))
	r.Use(middleware.Heartbeat("/ping"))

	r.Get("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("hello world"))
	})

	r.Get("/panic", func(w http.ResponseWriter, r *http.Request) {
		panic("oh no")
	})

	r.Get("/info", func(w http.ResponseWriter, r *http.Request) {
		oplog := httpzaplog.LogEntry(r.Context())
		w.Header().Add("Content-Type", "text/plain")
		oplog.Info("info here")
		w.Write([]byte("info here"))
	})

	r.Get("/warn", func(w http.ResponseWriter, r *http.Request) {
		oplog := httpzaplog.LogEntry(r.Context())
		oplog.Warn("warn here")
		w.WriteHeader(400)
		w.Write([]byte("warn here"))
	})

	r.Get("/err", func(w http.ResponseWriter, r *http.Request) {
		oplog := httpzaplog.LogEntry(r.Context())
		oplog.Error("err here")
		w.WriteHeader(500)
		w.Write([]byte("err here"))
	})

	http.ListenAndServe(":5555", r)
}
```

## Credits

This package is a modified version of [go-chi/httplog](https://github.com/go-chi/httplog).


## License

[MIT](/LICENSE)

NOTE: This is a derivative package of `go-chi/httplog`. Refer to the [NOTICE file](/NOTICE) for the license of the
original source material.
