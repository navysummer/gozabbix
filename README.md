# gozabbix

## api 
```GO
package main

import (
	"fmt"

	"github.com/navysummer/gozabbix"
)

func GetHost(api *gozabbix.API, host string) (gozabbix.JsonRPCResponse, error) {
	params := make(map[string]interface{}, 0)
	filter := make(map[string]string, 0)
	filter["host"] = host
	params["filter"] = filter
	params["output"] = "extend"
	method := "host.get"
	fmt.Println(params)
	response, err := api.ZabbixRequest(method, params)
	return response, err
}

func main() {
	api, err := gozabbix.NewAPI("http://ip:port/zabbix/api_jsonrpc.php", "Admin", "zabbix")
	if err != nil {
		fmt.Println(err)
		return
	}
	_, err = api.Login()
	if err != nil {
		fmt.Println(err)
		return
	}
	host := "Zabbix server"
	ghost, err := GetHost(api, host)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(ghost.Result)
}

```

## sender
```GO
package main

import (
    "time"
    "github.com/navysummer/gozabbix"
)

const (
    defaultHost  = `localhost`
    defaultPort  = 10051
)

func main() {
    var metrics []*gozabbix.Metric
    metrics = append(metrics, gozabbix.NewMetric("localhost", "cpu", "1.22", time.Now().Unix()))
    metrics = append(metrics, gozabbix.NewMetric("localhost", "status", "OK"))

    // Create instance of Packet class
    packet := gozabbix.NewPacket(metrics)

    // Send packet to zabbix
    z := gozabbix.NewSender(defaultHost, defaultPort)
    z.Send(packet)
}
```
