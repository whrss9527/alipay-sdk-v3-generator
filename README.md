# alipay-sdk-v3-generator
用于生成[支付宝开放平台 V3 SDK ](https://opendocs.alipay.com/open-v3/065bsc)的代码生成模板
目前仅支持生成 Go 语言的 SDK 代码，会逐步支持其他语言，欢迎贡献代码

## 项目介绍
OpenAPI 规范（OAS），是定义一个标准的、与具体编程语言无关的 RESTful API 的规范，支付宝提供了遵循OpenAPI 规范的 OpenAPI 描述文件，可以根据 OpenAPI 描述文件来生成对应的 OpenAPI 文档、SDK 和自动测试工具等等。

目前，支付宝仅提供了 Java、PHP、Node.js 版本的 SDK，其他语言则需要开发者自行生成，生成方式使用 openapi 提供的代码生成工具 OpenAPI Generator。

生成代码需要对 mustache 模板进行修改，以适配支付宝的加验签、加解密等的规则，本项目提供了一个简单的模板，可以生成 Go 语言的 SDK 代码。

对应生成的 golang SDK 代码，可以直接使用 [go-alipay-sdk-v3](https://github.com/whrss9527/go-alipay-sdk-v3)

## 使用方法
1. 安装 [openapi-generator](https://openapi-generator.tech/docs/installation)

2. 下载 [支付宝 V3 协议的 openapi 描述文件](https://github.com/alipay/alipay-sdk-java-all/blob/master/v3/api/openapi.yaml)

3. 生成代码
```sh
openapi-generator generate -i ./resources/openapi.yaml -g go -t ./go -o ./golang-sdk/ --package-name alipay
```

> -i 参数指定 openapi 描述文件的路径

> -g 参数指定生成代码的语言

> -t 参数指定代码生成模板的路径

> -o 参数指定生成代码的输出路径

> --package-name 参数指定生成代码的包名
## SDK 使用示例
### golang
```go
package alipay

import (
	`bytes`
	`context`
	`fmt`
	`testing`

	sdk "github.com/whrss9527/go-alipay-sdk-v3"
)

const (
	AppID = ""
	PrivateKey = ``
	PublicKey= ``
)

func Test_RequestPayment(t *testing.T) {
	cfg := sdk.NewConfiguration()
	cfg.Debug = true
	cfg.AppID = AppID
	cfg.PrivateKey = FormatPrivateKey(PrivateKey)
	cfg.PublicKey = FormatPublicKey(PublicKey)
	cfg.AppCertSN = ""
	client := sdk.NewAPIClient(cfg)

	ctx := context.Background()
	result, resp, err := client.AlipayTradeAPI.AlipayTradePrecreate(ctx).AlipayTradePrecreateModel(sdk.AlipayTradePrecreateModel{
		OutTradeNo:  sdk.PtrString("20150320010101002"),
		TotalAmount: sdk.PtrString("0.1"),
		Subject:     sdk.PtrString("Iphone6 16G"),
		StoreId:     sdk.PtrString("NJ_001"),
		ProductCode: sdk.PtrString("FACE_TO_FACE_PAYMENT"),
	}).Execute()
	if err != nil {
		panic(err)
	}
	fmt.Printf("response: %+v\n", resp)
	fmt.Printf("result: %+v\n", result)
}

func Test_QueryPayment(t *testing.T) {
	cfg := sdk.NewConfiguration()
	cfg.Debug = true
	cfg.AppID = AppID
	cfg.PrivateKey = FormatPrivateKey(PrivateKey)
	cfg.PublicKey = FormatPublicKey(PublicKey)
	cfg.AppCertSN = ""
	client := sdk.NewAPIClient(cfg)

	ctx := context.Background()
	execute, response, err := client.AlipayTradeAPI.AlipayTradeQuery(ctx).AlipayTradeQueryModel(sdk.AlipayTradeQueryModel{
		OutTradeNo: sdk.PtrString("20150320010101002"),
	}).Execute()

	if err != nil {
		fmt.Printf("error: %v\n", err)
	}
	fmt.Printf("response: %+v\n", response)
	fmt.Printf("result: %+v\n", execute)
}

func FormatPrivateKey(privateKey string) string {
	var buffer bytes.Buffer
	buffer.WriteString("-----BEGIN PRIVATE KEY-----\n")
	buffer.WriteString(privateKey)
	buffer.WriteString("\n-----END PRIVATE KEY-----")
	return buffer.String()
}

func FormatPublicKey(publicKey string) string {
	var buffer bytes.Buffer
	buffer.WriteString("-----BEGIN PUBLIC KEY-----\n")
	buffer.WriteString(publicKey)
	buffer.WriteString("\n-----END PUBLIC KEY-----")
	return buffer.String()
}

```
