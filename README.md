# trivy-snyk-cli-compared

The `main.go` file contains just a single import:

```go
package main

import (
	"fmt"
	_ "github.com/cilium/cilium/proxylib/proxylib"
)

func main() {
	fmt.Println("hello world")
}
```

Test it with `snyk test`, which gives a high risk vulnerability to only this single package:

```
Info: https://security.snyk.io/vuln/SNYK-GOLANG-GOLANGORGXNETHTTP2-3323837
  Introduced through: github.com/cilium/cilium/proxylib/proxylib@1.13.0
```

.. as this is the only package actually used, despite the `go.mod` file actually has two imports:


```go
module github.com/dotkas/trivy-snyk-cli-compared

go 1.19

require (
	github.com/cilium/cilium v1.13.0
	github.com/emicklei/go-restful v2.15.0+incompatible
)
```

Run a `trivy fs` test by staying in the root of this repo and running:

```bash
docker run -it --entrypoint /bin/sh -v `pwd`:/app/go aquasec/trivy:0.33.0
```

This will give you a trivy-based prompt, where you can run:

```bash
/ # trivy fs /app/go

go.mod (gomod)

Total: 2 (UNKNOWN: 0, LOW: 1, MEDIUM: 0, HIGH: 0, CRITICAL: 1)

┌────────────────────────────────┬────────────────┬──────────┬─────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│            Library             │ Vulnerability  │ Severity │  Installed Version  │ Fixed Version │                            Title                             │
├────────────────────────────────┼────────────────┼──────────┼─────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ github.com/emicklei/go-restful │ CVE-2022-1996  │ CRITICAL │ 2.15.0+incompatible │ 2.16.0        │ go-restful: Authorization Bypass Through User-Controlled Key │
│                                │                │          │                     │               │ https://avd.aquasec.com/nvd/cve-2022-1996                    │
├────────────────────────────────┼────────────────┼──────────┼─────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤

```

