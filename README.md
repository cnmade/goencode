# goencode

加强官方encode类库功能，目前只有一个json库做了修改

## json

本项目在官方json类库的基础上，加强了处理json编码不支持的类型Field。
官方默认会报错，但是如果你想忽略报错的Field，继续编译其它Field。

你可以用这个库


### 使用

```golang
go get -u -v github.com/cnmade/goencode


```

下载goencode包

然后你代码里可以参考下面的示例来写

```golang

package main

import (
	"bytes"
	"fmt"
	"github.com/cnmade/goencode/json"
	"io/ioutil"
	"net/http"
)

func main() {
	hc, err := http.NewRequest("GET", "https://gitlab.com/api/v4/templates/gitignores", bytes.NewBuffer([]byte{}))
	if err != nil {
		panic(err)
	}

	clt := http.Client{}
	response, err := clt.Do(hc)
	json.UnsupportedBehaviour = json.UnsupportedBehaviourWithSprintf
	jsonStr, err := json.Marshal(response)
	if err != nil {
		fmt.Printf("error: %#v\n", err.Error())
		panic(err)
	}
	fmt.Printf("Response: '%+v' \n", string(jsonStr))

	fmt.Printf("Response raw: '%+v'\n", response)

	bodyAll, err := ioutil.ReadAll(response.Body)

	fmt.Println("response status: " + response.Status)
	fmt.Println("body " + string(bodyAll))
}
```

注意这里多了一个设置

```golang
	json.UnsupportedBehaviour = json.UnsupportedBehaviourWithSprintf

```

它表示，如果遇到不支持的Field 数据类型，我们用Sprintf 来格式化字段value

还有其它的几个设置可以用


* UnsupportedBehaviourWithError string = "error" //这个是跟官方行为一样，报错
* UnsupportedBehaviourWithNull string = "null" //这个是替换不支持的value 为null 值
* UnsupportedBehaviourWithNone string = ""  //这个是替换不支持的value 为空字符串
* UnsupportedBehaviourWithSprintf string = "sprintf" //这个是替换不支持的value 为 fmt.Sprintf("%v")