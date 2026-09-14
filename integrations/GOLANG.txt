# Go / Golang Integration — Netfie Bulk SMS Gateway

## Overview
High-concurrency, memory-efficient SMS dispatching using Go's built-in `net/http` package.

```go
package main

import (
	"fmt"
	"io"
	"net/http"
	"net/url"
	"strings"
)

func SendSMS(numbers []string, message string, simSlot string, delay string) (string, error) {
	apiURL := "https://your-domain.com/api/sms/gateway"
	apiKey := "YOUR_SECRET_API_KEY"

	data := url.Values{}
	data.Set("auth_key", apiKey)
	data.Set("action", "push")
	data.Set("numbers", strings.Join(numbers, ","))
	data.Set("message", message)
	data.Set("sim_slot", simSlot)
	data.Set("delay", delay)

	resp, err := http.PostForm(apiURL, data)
	if err != nil {
		return "", err
	}
	defer resp.Body.Close()

	body, err := io.ReadAll(resp.Body)
	if err != nil {
		return "", err
	}

	return string(body), nil
}

func main() {
	res, _ := SendSMS([]string{"01711111111", "01822222222"}, "Hello from Golang!", "0", "2")
	fmt.Println(res)
}
```