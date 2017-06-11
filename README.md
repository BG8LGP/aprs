# APRS

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://choosealicense.com/licenses/mit/)
[![Build Status](https://travis-ci.org/ebarkie/aprs.svg?branch=master)](https://travis-ci.org/ebarkie/aprs)

Go package for manipulating APRS string and byte packets and uploading them via
APRS-IS or transmitting via TNC KISS.

It fully supports creating weather observations for [Citizen Weather Observer Program (CWOP)](http://wxqa.com).

## Installation

```
$ go get github.com/ebarkie/aprs
```

## Usage

See [USAGE](USAGE.md).

## Example

```go
package main

import (
	"log"

	"github.com/ebarkie/aprs"
)

func main() {
	w := Wx{
		Lat:     35.7,
		Lon:     -78.7,
		Station: "DvsVP2+",
	}
	w.Altimeter(29.70)
	w.Humidity(90)
	w.RainRate(0.00)
	w.RainLast24Hours(0.10)
	w.Temperature(85)
	w.WindDirection(180)
	w.WindSpeed(5)

	f := Frame{
		Dst: aprs.Address{Call: "APRS"},
		Src: aprs.Address{Call: "aWnnnn"},
		Path: aprs.Path{aprs.Address{Call: "TCPIP", Repeated: true}},
		Text: w.String(),
	}
	err := f.SendIS("cwop.aprs.net:14580", -1)
	if err != nil {
		log.Printf("Upload error: %s", err)
	}

	f = Frame{}
	f.Dst.FromString("APZ001") // Experimental v0.0.1
	f.Src.FromString("N0CALL-13")
	f.Path.FromString("WIDE1-1,WIDE2-1")
	f.Text = w.String()
	err = f.SendTNC("direwolf:8001")
	if err != nil {
		log.Printf("Network TNC transmit error: %s", err)
	}
}
```

## License

Copyright (c) 2016-2017 Eric Barkie.  All rights reserved.  
Use of this source code is governed by the MIT license
that can be found in the [LICENSE](LICENSE) file.
