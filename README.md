# SGQR payload builder written in Go

## VietQR specification

### Data Objects Under the Root of a QR Code

| Name | ID | Format | Length | Presence | Comment |
| --- | --- | --- | --- | --- | --- |
| Payload Format Indicator | "00" | N | "02" | M
| Point of Initiation Method | "01" | N | "02" | O | "11" for static, "22" for dynamic
| Merchant Account Information | "02"-"51" | ans | Each var. up to "99" | M | At least one Merchant Account Information data object shall be present.
| Transaction Currency | "53" | N | "03" | M | Fixed to "704"
| Country Code | "58" | ans | "02" | M | Fixed to "VN"
| Additional Data Field Template | "62" | S | var. up to "99" | O 
| CRC | "63" | ans | "04" | M

### Data Objects for Additional Data Field Template (ID "62")

| Name | ID | Format | Length | Presence |
| --- | --- | --- | --- | --- |
| Additional Info | "08" | ans | var. up to "25" | O

**VietQR - Merchant Account Information (ID "38")**

| Data Object | Input Characters | Remarks|
| --- | --- | --- |
| Merchant Account Information | "3856" | Floating ID "38". This ID is allocated for this QR only
| - Global Unique Identifier<br/>- Bank Info<br/>	* Acquirer ID<br/>	* Account No/Card No<br/> - Destination Type | "0010A000000727"<br/>"012600069704150112113366668888"<br/><br/><br/>"0208QRIBFTTA" | Reversed domain<br/><br/>970415 - [NAPAS Bin](https://www.sbv.gov.vn/webcenter/ShowProperty?nodeId=/UCMServer/SBV399939//idcPrimaryFile&revision=latestreleased)<br/>113366668888<br/>QRIBFTTA / QRIBFTTC


**Sample code**

```go
package main

import (
	"fmt"

	"giautm.dev/sgqr"
	qrcode2 "github.com/lizebang/qrcode-terminal"
	"github.com/skip2/go-qrcode"
)

func main() {
	i := &sgqr.VietQRInput{
		AcqID:          "970415",
		AccountNo:      "113366668888",
		Amount:         79000,
		AdditionalInfo: "Ung Ho Quy Vac Xin",
	}

	payload, err := i.BuildPayload()
	if err != nil {
		panic(err)
	}
	fmt.Println("Data", payload)

	qrcode2.QRCode(payload, qrcode2.BrightWhite, qrcode2.NormalBlack, qrcode.Medium)
}
```

Performance
```
goos: darwin
goarch: amd64
pkg: giautm.dev/sgqr
cpu: Intel(R) Core(TM) i5-8279U CPU @ 2.40GHz
Benchmark_BuildPayload
Benchmark_BuildPayload-8   	 1558408	       771.8 ns/op	     594 B/op	       8 allocs/op
PASS
ok  	giautm.dev/sgqr	2.447s
```