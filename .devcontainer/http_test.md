在 Go 语言中，你可以使用 `http.Hijacker` 接口来实现 HTTP hijacking。通过这个接口，你可以获得底层的网络连接，以便直接读取和写入原始的 HTTP 报文数据，实现一些高级的网络操作。

下面是一个简单的示例代码，演示了如何在 Go 中使用 `http.Hijacker` 接口来实现 HTTP hijacking：

```go
package main

import (
	"fmt"
	"net"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	hj, ok := w.(http.Hijacker)
	if !ok {
		http.Error(w, "Hijacking not supported", http.StatusInternalServerError)
		return
	}
	conn, _, err := hj.Hijack()
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}
	defer conn.Close()

	// 在这里你可以直接操作 conn，进行自定义的数据读写操作
	conn.Write([]byte("HTTP/1.1 200 OK\r\nContent-Length: 5\r\n\r\nHello")) // 发送自定义的响应
}

func main() {
	http.HandleFunc("/", handler)
	fmt.Println("Server is running on http://localhost:8080")
	http.ListenAndServe(":8080", nil)
}
```

在这个例子中，`handler` 函数通过类型断言将 `http.ResponseWriter` 转换为 `http.Hijacker` 接口，然后调用 `Hijack` 方法获取底层的网络连接 `conn`。之后你可以在 `conn` 上执行自定义的读写操作，如发送自定义的 HTTP 响应。

请注意，HTTP hijacking 是一个高级的网络操作，需要小心处理，确保不会破坏 HTTP 协议规范和应用程序的安全性。