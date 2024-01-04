上次修改时间: {docsify-updated}

# Python脚本

本节主要用于介绍 RestCloud 中相关 Python脚本 的内容

# 输入与输出
```python
# 由于 RestCloud 中 Python 的执行相当于是在命令行中调用一个 Python 脚本
# 所以 参数 的传入是通过在命令后接入变量的形式来实现的，类似如下
# python3 myTest.py hello world
# 因为是从命令行中传入所需参数，故参数长度不可太长，否则会报错
# 传出需要使用 print() 函数来进行最终结果的输出

from sys import argv
# 获取传入的第一个参数
param1 = argv[1]
# 获取传入的第二个参数
param2 = argv[2]
# 进行获取之后的相关逻辑操作
# 这里仅仅是将两个字符串拼接到一起
result = f"{param1}:{param2}"
# 将最终结果通过 print 函数进行输出
print({
    "code": 0,
    "message": "成功",
    "data": {
        "result": result
    }
})
```

# 其他依赖库的使用

如果需要进行其他依赖库的使用，例如 pandas 可使用类似如下的命令

```bash
# 该命令的作用是在 RestCloud 所在的服务器安装相关依赖，供后续使用
pip install pandas -i https://pypi.douban.com/simple/
```

然后就可以在 python 脚本中引入该依赖库进行使用了

```python
import pandas as pd
from sys import argv

def process_csv(input_file, output_file):
    # 读取 CSV 文件
    data = pd.read_csv(input_file)
    # 进行数据处理，这里只是一个示例，你可以根据具体需求进行数据处理
    processed_data = data.dropna()  # 删除缺失值
    # 将处理后的数据写入到新的 CSV 文件
    processed_data.to_csv(output_file, index=False)

# 获取传入的第一个参数
oldCsvPath = argv[1]
# 获取传入的第二个参数
newCsvPath = argv[2]

# 进行参数获取之后的相关逻辑操作
process_csv(oldCsvPath, newCsvPath)

# 将最终结果通过 print 函数进行输出
print({
    "code": 0,
    "message": "成功",
    "data": {
        "oldCsvPath": oldCsvPath,
        "newCsvPath": newCsvPath
    }
})
```

# shell命令组装
```python
import subprocess

# 定义要执行的命令列表
commands = [
    'ls -l',
    'echo "Hello, World!"',
    'pwd'
]

# 遍历命令列表，并执行每个命令
for command in commands:
    # 执行命令并捕获输出
    result = subprocess.run(command, shell=True, capture_output=True, text=True)
    
    # 打印命令输出
    print(f"Command: {command}")
    print(f"Output: {result.stdout.strip()}")
    print(f"Error: {result.stderr.strip()}")
    print("=" * 40)
```

-------------------------

上次修改时间: {docsify-updated}