---
url: "https://dict.thinktrader.net/nativeApi/question_function.html"
title: "常见问题 | 迅投知识库"
---

### [\#](https://dict.thinktrader.net/nativeApi/question_function.html\#%E5%AF%BC%E5%85%A5xtquant%E5%BA%93%E6%97%B6%E6%8F%90%E7%A4%BA-no-module-named-xtquant-ipythonapiclient) 导入xtquant库时提示 `NO module named 'xtquant.IPythonAPiClient'`

1、目前xtquant支持的python版本为 64位python3.6----3.11，请使用支持的python版本重试

### [\#](https://dict.thinktrader.net/nativeApi/question_function.html\#%E8%BF%9E%E6%8E%A5-xtquant-%E6%97%B6%E5%A4%B1%E8%B4%A5-%E8%BF%94%E5%9B%9E-1%E5%8F%8A%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95) 连接 xtquant 时失败，返回-1及解决方法

1. 客户端是否以极简模式登录（登录qmt系统时需要勾选极简模式）
2. 检查路径是否正确

- `miniqmt`：路径指定到安装目录下`\userdata_mini`文件夹
- `投研端`：路径指定到安装目录下`\userdata`文件夹

3. 客户端安装在C盘的话，每次都需要用管理员权限运行策略，才能正常连接，否则有权限问题。

提示

不建议安装在C盘。

可以通过以下测试来验证是否有写入权限

```

file_path = r"d:\qmt\userdata_mini\example.txt"  # 设置文件路径和名称

# 使用open函数创建文件，并指定写入模式("w"表示写入模式)
with open(file_path, "w") as file:
    file.write("123")  # 向文件写入内容
```

如果出现`PermissionError`，则说明存在文件权限问题

4. 路径正确时换个`session`（任意整数即可）

提示

由于机制限制，同一个session的两次python进程 connect之间必须超过3秒钟

5. 如果miniqmt开启后, userdata\_mini文件夹内没有`up_queue_xtquant`文件，说明没有用户没有对应函数下单的权限，需要联系券商开启

### [\#](https://dict.thinktrader.net/nativeApi/question_function.html\#%E6%89%A7%E8%A1%8Cxtdatacenter-init%E6%97%B6%E6%8F%90%E7%A4%BA%E7%9B%91%E5%90%AC58609%E7%AB%AF%E5%8F%A3%E5%A4%B1%E8%B4%A5) 执行xtdatacenter.init时提示监听58609端口失败

说明当前环境的58609端口被其他程序占用,通常是启动了两个xtdc服务导致的

**方法1.** 可用通过指定`xtdc.init(False)`后,使用`xtdc.listen(port)`指定自己需要的端口

```
from xtquant import xtdatacenter as xtdc
xtdc.set_token("这里输入token")
xtdc.init(False)
port = 58601
xtdc.listen(port=port)
print(f"服务启动,开放端口：{port}")
```

**方法2.** 关闭所有py程序，或重启电脑，再执行xtdc.init

### [\#](https://dict.thinktrader.net/nativeApi/question_function.html\#%E4%B8%8B%E5%8D%95%E5%90%8E-%E6%9F%A5%E8%AF%A2%E5%A7%94%E6%89%98%E7%9A%84%E6%8A%95%E8%B5%84%E5%A4%87%E6%B3%A8%E5%8F%AA%E6%9C%89%E5%89%8D%E5%8D%8A%E9%83%A8%E5%88%86) 下单后，查询委托的投资备注只有前半部分

极简客户端的`order_remark`字段有长度限制，最大 24 个英文字符(一个中文占3个)， 超出的部分会丢弃。大qmt 没有长度限制。

### [\#](https://dict.thinktrader.net/nativeApi/question_function.html\#userdata-mini%E7%9B%AE%E5%BD%95%E4%B8%8B-%E7%94%9F%E6%88%90%E5%A4%A7%E9%87%8Fdown-queue%E6%96%87%E4%BB%B6) userdata\_mini目录下，生成大量down\_queue文件

该文件是xttrade指定新的session产生的文件，可以参考下方示例来控制session产生的范围，避免该文件大量产生， **该文件可以删除**

[指定session id范围连接交易在新窗口打开](https://dict.thinktrader.net/nativeApi/code_examples.html?id=7zqjlm#%E6%8C%87%E5%AE%9Asession-id%E8%8C%83%E5%9B%B4%E8%BF%9E%E6%8E%A5%E4%BA%A4%E6%98%93)