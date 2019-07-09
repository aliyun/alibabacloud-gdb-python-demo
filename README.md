本文介绍如何基于Python编程环境连接和操作图数据库GDB。

进行以下操作时，请确保图数据库GDB的实例与您的ECS虚拟机处于同一个Virtual Private Cloud(VPC)网络环境。

## 环境准备
在使用Python连接GDB实例之前，需要准备以下环境：
- [Python](https://www.python.org/downloads/) 2.7+或者3.5+
- [pip](https://pip.pypa.io/en/stable/installing/)

如果您的Python为2.7版本，则需要额外安装以下module：
```
pip install futures --user
```

## 使用Python连接到GDB实例
1、输入以下命令以安装gremlinpython程序包：
```
pip install gremlinpython ‑‑user
```

2、下载代码
使用git命令直接clone代码到本地
```
git clone https://github.com/aliyun/alibabacloud-gdb-python-demo.git
```

编辑`GdbTest.py`文件中的以下内容，并替换其中的配置信息：
- 将`${your-gdb-endpoint}`改为您的图数据库GDB实例的域名
- 将`${username}`改为您的图数据库GDB实例的用户名
- 将`${password}`改为您的图数据库GDB实例的密码

3、 输入以下命令以运行示例

```
python test.py
```

执行结果范例如下：
```
[v[3a63cc90-d957-4324-9ffc-16a8e4c1c1f4]]
```

* 上面的例子是使用`g.V().limit(1)`遍历返回GDB中的一个点。要查询其他内容，可以替换成其他的DSL
* 其他dsl范例如下,图的点、边结构链接 http://tinkerpop.apache.org/docs/current/reference/#traversal

---

1. 删除指定label的点、边
```
g.E().hasLabel('gdb_sample_knows').drop()
g.E().hasLabel('gdb_sample_created').drop()
g.V().hasLabel('gdb_sample_person').drop()
g.V().hasLabel('gdb_sample_software').drop()
```

2. 添加顶点,为其设置id、property
```
g.addV('gdb_sample_person').property(id, 'gdb_sample_marko').property('age', 28).property('name', 'marko')
g.addV('gdb_sample_person').property(id, 'gdb_sample_vadas').property('age', 27).property('name', 'vadas')
g.addV('gdb_sample_person').property(id, 'gdb_sample_josh').property('age', 32).property('name', 'josh')
g.addV('gdb_sample_person').property(id, 'gdb_sample_peter').property('age', 35).property('name', 'peter')
g.addV('gdb_sample_software').property(id, 'gdb_sample_lop').property('lang', 'java').property('name', 'lop')
g.addV('gdb_sample_software').property(id, 'gdb_sample_ripple').property('lang', 'java').property('name', 'ripple')
```

3. 修改(或新增) age 属性
```
g.V('gdb_sample_marko').property('age', 29)
```

4. 建立关系,设置属性 weight
```
g.addE('gdb_sample_knows').from(V('gdb_sample_marko')).to(V('gdb_sample_vadas')).property('weight', 0.5f)
g.addE('gdb_sample_knows').from(V('gdb_sample_marko')).to(V('gdb_sample_josh')).property('weight', 1.0f)
g.addE('gdb_sample_created').from(V('gdb_sample_marko')).to(V('gdb_sample_lop')).property('weight', 0.4f)
g.addE('gdb_sample_created').from(V('gdb_sample_josh')).to(V('gdb_sample_lop')).property('weight', 0.4f)
g.addE('gdb_sample_created').from(V('gdb_sample_josh')).to(V('gdb_sample_ripple')).property('weight', 1.0f)
g.addE('gdb_sample_created').from(V('gdb_sample_peter')).to(V('gdb_sample_lop')).property('weight', 0.2f)
```

5. 查询所有点/指定label的点数量
```
g.V().count()
g.V().hasLabel('gdb_sample_person').count()
```

6. 查询指定条件的顶点 (>29岁的人, 按name降序排列所有人)
```
g.V().hasLabel('gdb_sample_person').has('age', gt(29))
g.V().hasLabel('gdb_sample_person').order().by('name', decr)
```

7. 关联查询(获取 marko 认识的人, marko认识的人created的software)
```
g.V('gdb_sample_marko').outE('gdb_sample_knows').inV().hasLabel('gdb_sample_person')
g.V('gdb_sample_marko').outE('gdb_sample_knows').inV().hasLabel('gdb_sample_person').outE('gdb_sample_created').inV().hasLabel('gdb_sample_software')
```

8. 删除关系、顶点
```
g.V('gdb_sample_marko').outE('gdb_sample_knows').where(inV().has(id, 'gdb_sample_josh')).drop()
g.V('gdb_sample_marko').drop()
```

---

有关Gremlin Python接口的更多信息，请参阅Apache TinkerPop3文档中的[Gremlin-Python](http://tinkerpop.apache.org/docs/current/reference/#gremlin-python)。
