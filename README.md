# Confrnce Proto Contracts

This repository contains the shared Protocol Buffer (gRPC) contracts for the Confrnce backend architecture (specifically `iris-backend` and `D2CSFU`). 

By keeping these contracts in a centralized, separate repository, both microservices can remain fully decoupled in production while ensuring they always communicate using the exact same strongly-typed data structures.

## Installation

To import these contracts into your Go microservice, run:

```bash
go get github.com/D2CFoundry/proto-contracts
```

## Usage

In your Go files, import the package and use the generated protobuf structs:

```go
import control "github.com/D2CFoundry/proto-contracts"

func handleMessage(msg *control.Message) {
    // Access your strongly typed protobuf fields
}
```

## Local Development (go.work)

If you are developing locally and making changes to these contracts alongside the microservices, you do not need to push to GitHub for every small change.

Instead, utilize Go Workspaces (`go.work`) at the root of your local development folder to override the GitHub package with your local folder:

```go
// go.work
go 1.26.1

use (
    ./D2CSFU/backend
    ./iris-backend
    ./proto-contracts
)
```

With `go.work` active, your local microservices will automatically resolve `github.com/D2CFoundry/proto-contracts` to your local `proto-contracts` folder. 

When you build Docker images for production, Docker will ignore `go.work` and pull the latest committed version directly from this repository.

## Updating Contracts

1. Modify the `.proto` files in this repository.
2. Re-generate the Go code using `protoc` (ensure you have the `protoc-gen-go` and `protoc-gen-go-grpc` plugins installed):
   ```bash
   protoc --go_out=. --go_opt=paths=source_relative \
          --go-grpc_out=. --go-grpc_opt=paths=source_relative \
          *.proto
   ```
3. Commit and push the changes to the `main` branch.
4. In your consuming microservices, update to the latest version by running:

```bash
go get github.com/D2CFoundry/proto-contracts@latest
```
