# go的工程开发|青训营笔记

**这是我参与「第五届青训营 」伴学笔记创作活动的第 3 天**

## 引言

用java的工程开发去介绍go的工程开发，Java是可以用maven来进行比较好的生态配置开发，而go就是用的GO111module 会在项目生成go.mod相当于java的pom.xml来完成工程开发。
下面如图所示java和go的工程编写

| <img align="center" src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/650fa40ef76a44258d0909f3bf46aaf4~tplv-k3u1fbpfcp-watermark.image?"> | <img align="center" src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7084153fb03e4174afa9b3fba4f550ec~tplv-k3u1fbpfcp-watermark.image?"/> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

就可以显然易懂工程开发的模式了下面就分成，为什么要使用工程开发，怎么配置这样的工程开发和进行单元测试

## 事故

所谓事故就是因为没有完美的工程开发模式，可能很容易导致一些问题的出现例如

1.营销配置错误，导致非预期用户享受权益，资金损失10w+

2.用户提现，幂等失效，短时间可以多次提现，资金损失20w+

3.代码逻辑错误，广告位被占，无法广告，收入损失500w+

4.代码指针使用错误，导致app瘫痪，损失上kw+

根据以上的经验总结我们就可以知道要进行过测试的重要性了。

## 测试

测试之前我们分为回归测试，集成测试，单元测试（顺序的覆盖率逐渐递增）这里我们重点介绍单元测试

### 单元测试-概况

单元测试概况如图所示

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/763c6b92d3b349879b30f7b43c349e07~tplv-k3u1fbpfcp-watermark.image?)

### 单元测试-规则

所有测试文件均以_test.go结尾、func TestXxx(*testing.T)、初始化逻辑放到TestMain中

举一个代码例子，例如

```go
import (
   "github.com/stretchr/testify/assert"
   "os"
   "testing"
)
func TestHelloTom(t *testing.T) {

   output := HelloTom("a")
   expectOutput := "tom"
   assert.Equal(t, expectOutput, output)
}
func TestMain(m *testing.M) {
   code := m.Run()
   os.Exit(code)
}
```

他就会进行比较测试如果不符合预期就会报错出来提醒测试人员这里应该进行一个修改。

### 单元测试-覆盖率

我们最理想的状态单元测试的覆盖率应该是要达到百分之90以上，当然这是很理想的状态实际上大概在百分之60到70就以及不错了，之后提上每一次的覆盖率所消耗的成本也是巨大的。

### 单元测试-依赖

如图所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5eaae4ac9614639a09d562c5fa99b0f~tplv-k3u1fbpfcp-watermark.image?)

单元测试覆盖到文件、数据库、缓存等。单元测试也是相互独立的可以任何时间、任何地点进行测试，所以单元测试一般会用到mock机制，mock机制可以为一个函数或者方法打桩（可以替换目标进行一个测试）不需要依赖本文件

## 基准测试

优化代码，需要对当前代码分析，内置的测试框架提供了基准测试的能力

```go
package day2_1

import (
   "math/rand"
   _ "math/rand"
)
var ServerIndex [10]int
func InitServerIndex() {
   for i := 0; i < 10; i++ {
      ServerIndex[i] = i + 100
   }
}
func Select() int {
   return ServerIndex[rand.Intn(10)]
}
```

这段代码大概意思就是随机的去选择服务器进行一个单元测试，[官方](https://baike.baidu.com/item/%E5%9F%BA%E5%87%86%E6%B5%8B%E8%AF%95/5876292?fr=aladdin)基准测试是指通过设计科学的测试方法、测试工具和测试系统，实现对一类测试对象的某项性能指标进行定量的和可对比的测试。

## 工程开发实战

### 需求分析

做一个社区话题的服务器功能

具体描述，展示话题，回帖列表，话题和回帖暂时用文件存储，设计ER图等，这里我不做过多的叙述重点是想讲解像java_springboot那样来进行一个配置工程

### Controller

控制层，也就是相当于接口例如如下的java_springboot代码：

```java
@RequestMapping(value = "/login",method = RequestMethod.POST)
@ApiOperation("登录的方法")
public String login(String username,String password){
    return null;
}
//alt+enter
@RequestMapping(value = "/getAddressByUser",method = RequestMethod.GET)
@ResponseBody
@ApiOperation("获取当前用户的地址")
public List<Address> getAddressByUser(String userid){
   return null;
}
```

就是对应着go控制去进行接口功能的实现这个部分就非常好理解了通过java来分析

### service

就是java的逻辑层，专门关心业务的逻辑实现，以及功能怎么具体的实现操作和一定的算法逻辑都在这个包下面进行实现，当然和接口相对应，例如如下Java代码：

```java
public User validateLogin(String name, String password) {
    List<User> list = userDao.findByName(name);
    if(list == null){
        //用户名不存在
    }else{
        for(User user:list){
            if(user.getPassword().equals(password)){
                //登录成功
                return user;
            }
        }
    }
    //密码不匹配
    return null;
}
```

对应go的业务逻辑，处理业务逻辑输出

### Dao

java的dao层也就是和数据库交互的层次例如如下代码：

```java
public interface UserDao {
    @Select("select * from user where name = #{name}")
    public List<User> findByName(String name);

    @Select("select * from address where userid = #{userId}")
    public List<Address> findByUser(int userId);
}
```

对应着go的数据控制，实现数据的增删改查

## 个人总结

此次学习，学习了如何使用go语言进行单元测试，和了解了go语言的工程开发模式，具体的用java的开发模式去深度理解了go的项目开发模式，还要继续熟练运用

## 引用

- [go官网之go语言介绍](https://link.juejin.cn/?target=https%3A%2F%2Fgolang.google.cn%2Fdoc%2Feffective_go%23introduction)
- [go语言的菜鸟教程](https://link.juejin.cn/?target=https%3A%2F%2Fwww.runoob.com%2Fgo%2Fgo-tutorial.html)

