# ws-compare

Three, functionally similar web services, written in Java, Go, and Rust.

The main motivation for writing these three simple web services, is to compare the three languages, Java, Go, and Rust.

[blog](https://medium.com/@dexterdarwich/comparison-between-java-go-and-rust-fdb21bd5fb7c)

## Endpoints

| Route                      | Request example    | Response example                    |
|:---------------------------|:-------------------|:------------------------------------|
| `GET /hello`               | `/hello`           | `{"message":"Hello, World"}`        |
| `GET /greeting/{name}`     | `/greeting/Jane`   | `{"greeting":"Hello, Jane!"}`       |
| `GET /fibonacci/{number}`  | `/fibonacci/45`    | `{"input":45,"output":1134903170}`  |

## Build and Run

For each version of the program, issue the commands from the specific language folder.

Lang |  Dependencies | Build | Binary Target | Run
--- | --- | --- | --- | ---
Java | <ul><li>[Akka](https://akka.io/)</li></ul> | `mvn package` |  `ws-compare/java/target` | `java -jar target/webservice.jar`
Go | <ul><li>[Echo](https://echo.labstack.com/)</li></ul> | `go build -o webservice` | `ws-compare/go/webservice` | `./webservice`
Rust | <ul><li>[Actix](https://github.com/actix/actix)</li><li>[Actix web](https://github.com/actix/actix-web)</li><li>[Serde](https://serde.rs/)</li></ul>| `cargo build --release` | `ws-compare/rust/target/release/webservice` | `./webservice`

## Testing it

For example, using curl:

`curl http://localhost:8080/fibonacci/20`

The response should be:

`{"input":20,"output":75025}`

## Benchmark

### Using [wrk](https://github.com/wg/wrk)

Default timeout (2 seconds)

    wrk -t2 -c400 -d30s http://127.0.0.1:8080/fibonacci/2

Extended timeout (5 seconds)

    wrk -t2 -c400 -d30s http://127.0.0.1:8080/fibonacci/25 --timeout 5

### Using [vegeta](https://github.com/tsenart/vegeta)

Run for 30 seconds, 2 workers, no rate limiting

    echo "GET http://localhost:8080/fibonacci/25" | vegeta attack -duration=30s -rate=0 -max-workers=2 | tee results.bin | vegeta report

Generate graph

    vegeta plot > plot.html

### Docker

#### Build

Example: while in the java directory, run the command:

    docker build -t java/webservice:1.0 .

#### Run

    docker run -p 8080:8080 java/webservice:1.0

### Notes

- The code is not suitable for production use. And is not idiomatic to each language. Also, lacks tests and documentation.
- This project was written to compare some metrics using different programming languages. The metrics I was looking for:
  - Compiled artifact size
  - Size in memory while running idle
  - Size in memory while running at peak requests per second
  - Requests per second, latency, and timeouts
  - Packaged, deployment ready build with the relevant runtime. (Docker image)
  - Compile/build time
