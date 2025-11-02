项目架构：  
ewfd防御都在这个目录，导出接口给库调用。  
- (done) 验证padding unit是否正确，看包的频率  
- 验证schedule unit  


整合代码：
- 拆分helper
- 在ebpf/test里面测一次
    -- 修改map_op clear
    -- 统一unit的create
---
2025.10.29：
- 编译项目：make build  生成可执行文件位于 build/bin/test
- 扩展防御功能：编写符合 eBPF 程序编写规范的c代码，放在路径 ewfd-defense/bpf/new_defense 里，
  用 clang 等编译器将 .ebpf.c 代码编译为 eBPF 字节码（目标文件 .o），
  通过工具从 .o 文件中提取字节码，转换为 C 语言的十六进制数组，在头文件里硬编码，放在路径 ewfd-defense/src 里，
  后期调用时，用 ebpf_load 函数加载字节码，用 ebpf_compile 函数编译字节码，用 ebpf_run_code 函数执行对应指令。
  如果字节码是直接执行一套完整的逻辑，不需要注册到ewfd_helper？如果字节码是调用函数，包括CALL指令，需要将调用的函数注册到ewfd_helper。
---
8-27日
- (done) 搞定map create and release
- (done) 在feature/ewfd的conf里面加载 ebpf code
- 适配feature/ewfd ebpf-unit
    -- 先用c版的schedule unit跑 ebpf front unit
- 在feature里面正式运行起来
- 测试
- push
---
8月31日
- ebpf schedule unit

11月11日：
- tor全局asan，configure好像无效
- ebpf hashmap
- c wpf-pad
- 优化读配置

### 开发进度

1. 基于最小项目cmake跑起来
libebpf静态库，和test main测试各种算法
先在当前项目跑通
- 写front算法，实现helper
    - 先helper写在ebpf中，完成基本功能
        - init, 创建队列
        - run tick, 读取队列
    - helper移到c中
    - 调试通过jit和interpreter，确保功能正常
- 基于测试数据调试front和schedule unit
- 思考模拟框架

test framework

TODO:
ebpf vm改成指令引用，不要复制指令。这样就能共享code cache，不需要每个connection都copy一份code，减小vm内存消耗。


### 当前所用的libebpf版本
https://github.com/IoTAccessControl/libebpf.git

开发方式，先在这里面开发，修改push回libebpf的repo。

由于tor makefile不支持 -I 参数，因此直接把文件copy出来。

当前使用的hashmap库：
https://github.com/tidwall/hashmap.c/blob/master/hashmap.c
