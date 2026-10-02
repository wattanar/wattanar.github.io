---
title: Building Custom Middleware in Go's Standard Library
tags:
  - Go
---

You don't need a framework to do middleware in Go. The standard library's
`net/http` gives you everything — middleware is just a function that wraps
another handler. That's it. Let's build three real ones: logging, panic
recovery, and authentication.

## The core idea

Every HTTP handler in Go has the same shape:

```go
type Handler interface {
    ServeHTTP(w http.ResponseWriter, r *http.Request)
}
```

So a **middleware is a function that takes a handler and returns a new
handler** that does something extra before or after calling the original:

```go
type Middleware func(http.Handler) http.Handler
```

Think of it like wrapping a gift. The original handler is the gift.
Each middleware is a layer of wrapping paper around it:

```
Request → [Log] → [Recover] → [Auth] → YourHandler → Response
```

Each layer sees the request coming in and the response going out.

## 1. Logging middleware

The most common middleware: log every request with its method, path,
status code, and how long it took.

```go
type statusRecorder struct {
    http.ResponseWriter
    status int
}

func (r *statusRecorder) WriteHeader(code int) {
    r.status = code
    r.ResponseWriter.WriteHeader(code)
}

func RequestLog(logger *slog.Logger) Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            start := time.Now()
            rec := &statusRecorder{ResponseWriter: w, status: http.StatusOK}

            next.ServeHTTP(rec, r)

            logger.Info("request",
                "method", r.Method,
                "path", r.URL.Path,
                "status", rec.status,
                "latency", time.Since(start),
            )
        })
    }
}
```

**Why the `statusRecorder`?** The default `ResponseWriter` doesn't tell you
what status code you wrote. By wrapping it and overriding `WriteHeader`, we
capture the code so we can log it. A small trick, but essential.

## 2. Panic recovery middleware

One panicking handler shouldn't crash the whole server. This middleware
catches panics and turns them into a clean 500:

```go
func Recover(logger *slog.Logger) Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            defer func() {
                if err := recover(); err != nil {
                    logger.Error("panic", "err", err)
                    http.Error(w, "internal error", http.StatusInternalServerError)
                }
            }()
            next.ServeHTTP(w, r)
        })
    }
}
```

The key part is `defer` + `recover()`. If anything deeper in the chain
panics, this layer catches it, logs it, and returns a friendly error —
the server keeps running.

## 3. Auth middleware

This one verifies the token and, if valid, stores the user in the request's
`context.Context` so handlers can access it:

```go
// Unexported struct key — avoids collisions with other context values
type principalKey struct{}

func Auth(verifier TokenVerifier) Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            sub, err := verifier.Verify(r.Context(), r.Header.Get("Authorization"))
            if err != nil {
                http.Error(w, "unauthorized", http.StatusUnauthorized)
                return
            }
            ctx := context.WithValue(r.Context(), principalKey{}, sub)
            next.ServeHTTP(w, r.WithContext(ctx))
        })
    }
}

func Principal(ctx context.Context) (string, bool) {
    s, ok := ctx.Value(principalKey{}).(string)
    return s, ok
}
```

A handler just reads the value when it needs it:

```go
func (h *Handler) Create(w http.ResponseWriter, r *http.Request) {
    user, _ := httpmiddleware.Principal(r.Context())
    // ...create order for user
}
```

Two details worth knowing:

- **Use an unexported struct as the context key.** String keys like
  `"user"` can collide with keys from another library. A private struct
  type is unique by definition.
- **`r.WithContext` returns a new request** — it never mutates the original.

## Wiring it all up

In `main.go`, define a tiny `chain` helper that wraps handlers in order:

```go
func chain(h http.Handler, mws ...Middleware) http.Handler {
    for i := len(mws) - 1; i >= 0; i-- { // reversed: first listed = outermost
        h = mws[i](h)
    }
    return h
}

mux := http.NewServeMux()
mux.HandleFunc("POST /orders", h.Create)

srv := &http.Server{
    Handler: chain(mux, RequestLog(logger), Recover(logger), Auth(verifier)),
}
```

**Order matters.** The first middleware listed is the outermost layer — it
runs first on the way in and last on the way out. With the order above:

1. `RequestLog` times the whole request, including auth
2. `Recover` catches panics from auth *and* handlers
3. `Auth` rejects bad tokens before they ever reach your code

## When to reach for a router library

The standard library is enough for most services (Go 1.22+ `ServeMux`
supports method + path patterns like `POST /orders/{id}`). Move to
`go-chi/chi` only when you want:

- A native middleware chain API (`r.Use(...)` instead of a `chain` helper)
- Route grouping (`r.Route("/orders", ...)`)

Chi has the same wrapping idea and the same middleware signature, so the
three middlewares above work with it unchanged.

## Key takeaways

- Middleware = a function that wraps a handler. Nothing more.
- Wrap `ResponseWriter` when you need to observe the status code.
- Pass data to handlers via `context.Context`, with unexported struct keys.
- List middleware outermost-first; `Recover` near the top, auth below it.
- No framework needed — and if you adopt one later, your middleware
  moves with you.
