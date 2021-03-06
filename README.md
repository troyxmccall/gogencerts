# gogencerts
A simple library to generate server certs and keys for HTTPS support directly within your Go program.

The code is modified from http://golang.org/src/crypto/tls/generate_cert.go.

Use this library for testing purposes only, e.g. to experiment with the built-in Go HTTPS server. Do NOT use in production!

Production: use letsencypt & nginx on port 443, then reverse proxy to your go webserver. 

# Usage


    package main

    import (
        "fmt"
        "github.com/troyxmccall/gogencerts"
        "log"
        "net/http"
    )

    func handler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hi there!")
    }

    func main() {
        // Check if the cert files are available.
        err := gogencerts.Check("cert.pem", "key.pem")
        // If they are not available, generate new ones.
        if err != nil {
            err = gogencerts.Generate("cert.pem", "key.pem", "127.0.0.1:8081")
            if err != nil {
                log.Fatal("Error: Couldn't create https certs.")
            }
        }
        http.HandleFunc("/", handler)
        http.ListenAndServeTLS(":8081", "cert.pem", "key.pem", nil)
    }
