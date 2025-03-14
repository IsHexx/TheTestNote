# Python 基础知识

## 1. Python 简介

### 1.1 Python 的定义
- **定义**：Python 是一种高级、解释型、交互式和面向对象的编程语言。
- **特点**：
  - **简洁易读**：语法简洁，易于学习和使用。
  - **跨平台**：支持 Windows、Linux、macOS 等多种操作系统。
  - **丰富的库**：拥有大量的标准库和第三方库，支持多种编程任务。
  - **动态类型**：变量在运行时动态绑定类型，无需显式声明。
  - **面向对象**：支持面向对象编程，可以定义类和对象。

### 1.2 Python 的应用场景
- Web 开发（Django、Flask）
- 数据分析（Pandas、NumPy）
- 人工智能和机器学习（TensorFlow、PyTorch）
- 自动化脚本和系统管理
- 游戏开发（Pygame）

## 2. Python 基础语法

### 2.1 注释
- **单行注释**：使用 `#`。
  ```python
  # 这是一个单行注释
  print("Hello, World!")
  ```
- **多行注释**：使用三引号 `"""` 或 `'''`。
  ```python
  """
  这是一个多行注释
  可以跨越多行
  """
  print("Hello, World!")
  ```

### 2.2 变量和数据类型
- **变量赋值**：
  ```python
  x = 10
  y = "Hello"
  ```
- **基本数据类型**：
  - **整数**：`int`
    ```python
    a = 10
    ```
  - **浮点数**：`float`
    ```python
    b = 3.14
    ```
  - **字符串**：`str`
    ```python
    c = "Hello, World!"
    ```
  - **布尔值**：`bool`
    ```python
    d = True
    e = False
    ```

### 2.3 类型转换
- **类型转换**：
  ```python
  a = int("123")  # 字符串转整数
  b = str(123)    # 整数转字符串
  c = float("3.14")  # 字符串转浮点数
  ```

### 2.4 输入和输出
- **输出**：
  ```python
  print("Hello, World!")
  ```
- **输入**：
  ```python
  name = input("Enter your name: ")
  print(f"Hello, {name}!")
  ```

## 3. 控制流

### 3.1 条件语句
- **if-elif-else**：
  ```python
  age = 20
  if age > 18:
      print("Adult")
  elif age > 12:
      print("Teenager")
  else:
      print("Child")
  ```

### 3.2 循环语句
- **for 循环**：
  ```python
  for i in range(5):
      print(i)
  ```
- **while 循环**：
  ```python
  count = 0
  while count < 5:
      print(count)
      count += 1
  ```

### 3.3 循环控制
- **break**：退出循环
  ```python
  for i in range(10):
      if i == 5:
          break
      print(i)
  ```
- **continue**：跳过当前迭代
  ```python
  for i in range(10):
      if i % 2 == 0:
          continue
      print(i)
  ```

## 4. 函数

### 4.1 定义函数
- **函数定义**：
  ```python
  def greet(name):
      print(f"Hello, {name}!")
  ```

### 4.2 调用函数
- **函数调用**：
  ```python
  greet("Alice")
  ```

### 4.3 参数和返回值
- **带参数的函数**：
  ```python
  def add(a, b):
      return a + b
  ```
- **调用带参数的函数**：
  ```python
  result = add(3, 5)
  print(result)
  ```

### 4.4 默认参数
- **默认参数**：
  ```python
  def greet(name="World"):
      print(f"Hello, {name}!")
  greet()  # 输出：Hello, World!
  greet("Alice")  # 输出：Hello, Alice!
  ```

### 4.5 关键字参数
- **关键字参数**：
  ```python
  def describe_pet(animal_type, pet_name):
      print(f"I have a {animal_type} named {pet_name}.")
  describe_pet(animal_type="hamster", pet_name="harry")
  ```

### 4.6 可变参数
- **可变参数**：
  ```python
  def sum_numbers(*args):
      total = 0
      for num in args:
          total += num
      return total
  print(sum_numbers(1, 2, 3, 4))  # 输出：10
  ```

## 5. 数据结构

### 5.1 列表（List）
- **列表定义**：
  ```python
  my_list = [1, 2, 3, 4]
  ```
- **列表操作**：
  ```python
  my_list.append(5)  # 添加元素
  my_list.remove(2)  # 删除元素
  print(my_list[0])  # 访问元素
  ```

### 5.2 元组（Tuple）
- **元组定义**：
  ```python
  my_tuple = (1, 2, 3)
  ```
- **元组操作**：
  ```python
  print(my_tuple[1])  # 访问元素
  ```

### 5.3 字典（Dictionary）
- **字典定义**：
  ```python
  my_dict = {"name": "Alice", "age": 25}
  ```
- **字典操作**：
  ```python
  print(my_dict["name"])  # 访问键值
  my_dict["age"] = 26  # 修改键值
  ```

### 5.4 集合（Set）
- **集合定义**：
  ```python
  my_set = {1, 2, 3}
  ```
- **集合操作**：
  ```python
  my_set.add(4)  # 添加元素
  my_set.remove(2)  # 删除元素
  ```

## 6. 模块和包

### 6.1 模块
- **创建模块**：
  ```python
  # my_module.py
  def greet(name):
      print(f"Hello, {name}!")
  ```
- **导入模块**：
  ```python
  import my_module
  my_module.greet("Alice")
  ```

### 6.2 包
- **创建包**：
  ```python
  # my_package/
  #   __init__.py
  #   my_module.py
  ```
- **导入包中的模块**：
  ```python
  from my_package import my_module
  my_module.greet("Alice")
  ```

## 7. 异常处理

### 7.1 try-except
- **捕获异常**：
  ```python
  try:
      result = 10 / 0
  except ZeroDivisionError:
      print("Cannot divide by zero!")
  ```

### 7.2 finally
- **finally 块**：
  ```python
  try:
      result = 10 / 2
  except ZeroDivisionError:
      print("Cannot divide by zero!")
  finally:
      print("This will always execute.")
  ```

### 7.3 自定义异常
- **自定义异常**：
  ```python
  class MyError(Exception):
      pass

  raise MyError("This is a custom error.")
  ```

## 8. 文件操作

### 8.1 打开文件
- **打开文件**：
  ```python
  file = open("example.txt", "r")
  content = file.read()
  file.close()
  print(content)
  ```

### 8.2 使用 `with` 自动管理文件
- **自动关闭文件**：
  ```python
  with open("example.txt", "r") as file:
      content = file.read()
  print(content)
  ```

### 8.3 写入文件
- **写入文件**：
  ```python
  with open("example.txt", "w") as file:
      file.write("Hello, World!")
  ```

## 9. 面向对象编程

### 9.1 定义类
- **类定义**：
  ```python
  class Dog:
      def __init__(self, name):
          self.name = name

      def bark(self):
          print(f"{self.name} says Woof!")
  ```

### 9.2 创建对象
- **创建对象**：
  ```python
  my_dog = Dog("Buddy")
  my_dog.bark()
  ```

### 9.3 类的继承
- **继承**：
  ```python
  class Cat(Dog):
      def meow(self):
          print(f"{self.name} says Meow!")
  my_cat = Cat("Whiskers")
  my_cat.meow()
  ```

## 10. 标准库

### 10.1 `math` 模块
- **数学函数**：
  ```python
  import math
  print(math.sqrt(16))  # 输出：4.0
  ```

### 10.2 `datetime` 模块
- **日期和时间**：
  ```python
  from datetime import datetime
  now = datetime.now()
  print(now.strftime("%Y-%m-%d %H:%M:%S"))
  ```

### 10.3 `json` 模块
- **JSON 数据处理**：
  ```python
  import json
  data = {"name": "Alice", "age": 25}
  json_data = json.dumps(data)
  print(json_data)
  ```

## 11. 第三方库

### 11.1 `requests` 库
- **发送 HTTP 请求**：
  ```python
  import requests
  response = requests.get("https://api.github.com")
  print(response.json())
  ```

### 11.2 `numpy` 库
- **数值计算**：
  ```python
  import numpy as np
  array = np.array([1, 2, 3])
  print(array)
  ```

### 11.3 `pandas` 库
- **数据分析**：
  ```python
  import pandas as pd
  df = pd.DataFrame({"A": [1, 2], "B": [3, 4]})
  print(df)
  ```
