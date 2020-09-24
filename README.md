# gozabbix

## api 
```GO
package main
import (
    "fmt"
    "github.com/navysummer/gozabbix"
)

type Zabbix struct {
	url string
	username string
	password string
	
}

func (zbx *Zabbix) GetHost(api *gozabbix.API,host string)(gozabbix.JsonRPCResponse, error){
	params := make(map[string]interface{},0)
	params["output"] = []string{"hostid", "host", "name"}
	params["selectGroups"] = "extend"
	params["selectParentTemplates"] = []string{"templateid", "name"}
	filter := make(map[string]interface{},0)
	if host != ""{
		filter["host"] = host
	}
	if len(filter) != 0{
		params["filter"] = filter
	}
	method := "host.get"
	response, err := api.ZabbixRequest(method,params)
	if err != nil {
		fmt.Printf("get host(parmas=%v) fail,message:%v",params,err.Error())
	}
	return response, err
}

func main() {
    zbxUrl := "http:/127.0.0.1/zabbix"
	zbxUsername := "Admin"
	zbxPassword := "zabbix"
    zbx := Zabbix{}
	zapi, flag := zbx.Login(zbxUrl,zbxUsername,zbxPassword)
	if flag == false {
	    fmt.Println("zabbix login fail")
		os.Exit(500)
	}
    fmt.Println("Connected to API")
	zbx := Zabbix{"http:/ip:port/zabbix","username","password"}
	host := "Zabbix Server"
	ghost,err := zbx.GetHost(api,host)
	if err != nil {
        fmt.Println(err)
        return
    }
	fmt.Println(ghost)
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
