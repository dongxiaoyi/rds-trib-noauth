# redis-trib-noauth

解决redis-trib.rb脚本在执行远程ansible指令时，普通用户下的依赖问题：

（1）redis-trib.rb需要交互操作输入确认(ansible远程操作需要被控端安装pexpect，无root无法安装) === > 去掉交互部分代码。

（2）redis-trib.rb默认无密码连接redis  === > 增加通过读文件获取密码代码。

（3）rb脚本执行需要ruby环境  === > 编译ruby脚本为二进制脚本。

操作：

（1）参考https://github.com/pmq20/ruby-packer，使用其二进制指令rubyc编译redis-trib.rb

```# shell
   $ ./rubyc redis-trib.rb -o rds-trib-pass
```

（2）将依赖库（连接redis的依赖库）、密码文件放到rds-trib-pass同级目录

最终项目结构：
```shell
$ tree redis-trib-pass
redis-trib-pass
├── lib                                # ruby连接redis的驱动库
│   ├── redis
│   │   ├── client.rb
│   │   ├── cluster
│   │   │   ├── command_loader.rb
│   │   │   ├── command.rb
│   │   │   ├── key_slot_converter.rb
│   │   │   ├── node_key.rb
│   │   │   ├── node_loader.rb
│   │   │   ├── node.rb
│   │   │   ├── option.rb
│   │   │   ├── slot_loader.rb
│   │   │   └── slot.rb
│   │   ├── cluster.rb
│   │   ├── connection
│   │   │   ├── command_helper.rb
│   │   │   ├── hiredis.rb
│   │   │   ├── registry.rb
│   │   │   ├── ruby.rb
│   │   │   └── synchrony.rb
│   │   ├── connection.rb
│   │   ├── distributed.rb
│   │   ├── errors.rb
│   │   ├── hash_ring.rb
│   │   ├── pipeline.rb
│   │   ├── subscribe.rb
│   │   └── version.rb
│   └── redis.rb
├── password.txt                   # 密码文件
├── rds-trib-pass                  # 编译后的redis-trib
├── redis-trib.rb                  # 原版的redis-trib.rb文件
└── rubyc                          # 编译ruby脚本为二进制文件的指令
```
