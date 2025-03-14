## Python 常用内置库说明

### 1. os 模块

#### 1.1 模块简介
`os` 模块提供了丰富的与操作系统交互的功能，包括文件和目录操作、环境变量管理、进程管理等。它是一个跨平台的模块，适用于多种操作系统。

#### 1.2 常用功能

##### 1.2.1 文件和目录操作

- **获取当前工作目录**  
  `os.getcwd()` 返回当前工作目录的路径。

  ```python
  import os
  current_dir = os.getcwd()
  print(current_dir)  # 输出当前工作目录
  ```

- **改变工作目录**  
  `os.chdir(path)` 改变当前工作目录到指定路径。

  ```python
  os.chdir('/path/to/directory')
  print(os.getcwd())  # 输出新的工作目录
  ```

- **列出目录内容**  
  `os.listdir(path)` 返回指定路径下的文件和目录列表。

  ```python
  files_and_dirs = os.listdir('.')
  print(files_and_dirs)  # 输出当前目录下的文件和目录
  ```

- **创建目录**  
  `os.mkdir(path)` 创建一个目录。

  ```python
  os.mkdir('new_directory')
  ```

- **删除目录**  
  `os.rmdir(path)` 删除一个空目录。

  ```python
  os.rmdir('new_directory')
  ```

- **删除文件**  
  `os.remove(path)` 删除一个文件。

  ```python
  os.remove('file_to_delete.txt')
  ```

- **重命名文件或目录**  
  `os.rename(src, dst)` 将文件或目录从 `src` 重命名为 `dst`。

  ```python
  os.rename('old_name.txt', 'new_name.txt')
  ```

##### 1.2.2 路径操作

- **路径拼接**  
  `os.path.join()` 用于安全地拼接路径。

  ```python
  path = os.path.join('directory', 'subdirectory', 'file.txt')
  print(path)  # 输出：directory/subdirectory/file.txt
  ```

- **路径分割**  
  `os.path.split()` 将路径分割为目录和文件名。

  ```python
  directory, filename = os.path.split('/path/to/file.txt')
  print(directory)  # 输出：/path/to
  print(filename)  # 输出：file.txt
  ```

- **获取文件扩展名**  
  `os.path.splitext()` 分割文件名和扩展名。

  ```python
  filename, extension = os.path.splitext('file.txt')
  print(filename)  # 输出：file
  print(extension)  # 输出：.txt
  ```

- **检查路径是否存在**  
  `os.path.exists()` 检查路径是否存在。

  ```python
  print(os.path.exists('/path/to/file.txt'))  # 输出：True 或 False
  ```

- **检查是否为文件或目录**  
  `os.path.isfile()` 和 `os.path.isdir()` 分别检查路径是否为文件或目录。

  ```python
  print(os.path.isfile('/path/to/file.txt'))  # 输出：True 或 False
  print(os.path.isdir('/path/to/directory'))  # 输出：True 或 False
  ```

##### 1.2.3 环境变量

- **获取环境变量**  
  `os.getenv(key)` 获取环境变量的值。

  ```python
  home_dir = os.getenv('HOME')
  print(home_dir)  # 输出：用户的主目录路径
  ```

- **设置环境变量**  
  `os.environ[key] = value` 设置环境变量。

  ```python
  os.environ['MY_VARIABLE'] = 'some_value'
  print(os.getenv('MY_VARIABLE'))  # 输出：some_value
  ```

#### 1.3 注意事项
- 在使用 `os` 模块时，路径分隔符在 Windows 系统中是 `\`，而在类 Unix 系统中是 `/`。`os.path.join()` 和 `os.path.sep` 可以帮助处理跨平台的路径问题。
- 操作文件和目录时，需要注意权限问题，避免因权限不足导致操作失败。

---

### 2. datetime 模块

#### 2.1 模块简介
`datetime` 模块提供了处理日期和时间的类，包括 `datetime`、`date`、`time` 和 `timedelta` 等。它支持日期和时间的创建、操作和格式化。

#### 2.2 常用功能

##### 2.2.1 获取当前日期和时间

- **获取当前日期和时间**  
  `datetime.datetime.now()` 返回当前日期和时间。

  ```python
  from datetime import datetime
  now = datetime.now()
  print(now)  # 输出：2023-10-10 12:34:56.789012
  ```

- **获取当前日期**  
  `datetime.date.today()` 返回当前日期。

  ```python
  from datetime import date
  today = date.today()
  print(today)  # 输出：2023-10-10
  ```

##### 2.2.2 日期和时间的格式化

- **格式化日期和时间**  
  `strftime(format)` 将日期或时间格式化为字符串。

  ```python
  formatted_date = now.strftime('%Y-%m-%d %H:%M:%S')
  print(formatted_date)  # 输出：2023-10-10 12:34:56
  ```

- **解析日期和时间字符串**  
  `strptime(string, format)` 将字符串解析为日期或时间对象。

  ```python
  date_str = '2023-10-10 12:34:56'
  parsed_date = datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')
  print(parsed_date)  # 输出：2023-10-10 12:34:56
  ```

##### 2.2.3 日期和时间的计算

- **时间差**  
  `datetime.timedelta` 表示两个日期或时间之间的差值。

  ```python
  from datetime import timedelta
  delta = timedelta(days=5, hours=3, minutes=10)
  future_date = now + delta
  print(future_date)  # 输出：未来日期和时间
  ```

- **日期和时间的比较**  
  可以直接使用比较运算符比较日期或时间对象。

  ```python
  date1 = date(2023, 10, 10)
  date2 = date(2023, 10, 20)
  print(date1 < date2)  # 输出：True
  ```

##### 2.2.4 时区处理

- **使用时区**  
  `datetime.timezone` 和 `datetime.timedelta` 可以处理时区相关的问题。

  ```python
  from datetime import timezone
  utc_offset = timedelta(hours=8)  # 东八区时区
  tz = timezone(utc_offset)
  now_with_tz = datetime.now(tz)
  print(now_with_tz)  # 输出：带时区的当前日期和时间
  ```

#### 2.3 注意事项
- 在处理日期和时间时，注意时区问题，尤其是涉及国际化应用时。
- 格式化和解析日期时，格式字符串必须与实际日期或时间字符串匹配，否则会抛出异常。

---

### 3. json 模块

#### 3.1 模块简介
`json` 模块用于处理 JSON 数据的编码和解码。它支持将 Python 数据结构（如字典、列表等）转换为 JSON 格式，也可以将 JSON 数据解析为 Python 数据结构。

#### 3.2 常用功能

##### 3.2.1 JSON 编码

- **将 Python 数据结构转换为 JSON 字符串**  
  `json.dumps(obj)` 将 Python 数据结构转换为 JSON 格式的字符串。

  ```python
  import json
  data = {
      'name': 'Alice',
      'age': 25,
      'is_student': False,
      'courses': ['Math', 'Science']
  }
  json_str = json.dumps(data)
  print(json_str)  # 输出：JSON 格式的字符串
  ```

- **将 Python 数据结构写入 JSON 文件**  
  `json.dump(obj, file)` 将 Python 数据结构写入文件。

  ```python
  with open('data.json', 'w') as f:
      json.dump(data, f)
  ```

##### 3.2.2 JSON 解码

- **将 JSON 字符串解析为 Python 数据结构**  
  `json.loads(json_str)` 将 JSON 格式的字符串解析为 Python 数据结构。

  ```python
  json_str = '{"name": "Alice", "age": 25, "is_student": false, "courses": ["Math", "Science"]}'
  data = json.loads(json_str)
  print(data)  # 输出：Python 字典
  ```

- **从 JSON 文件中读取数据**  
  `json.load(file)` 从文件中读取 JSON 数据并解析为 Python 数据结构。

  ```python
  with open('data.json', 'r') as f:
      data = json.load(f)
  print(data)  # 输出：Python 字典
  ```

##### 3.2.3 自定义编码和解码

- **自定义编码**  
  使用 `json.JSONEncoder` 的子类可以自定义 JSON 编码逻辑。

  ```python
  class CustomEncoder(json.JSONEncoder):
      def default(self, obj):
          if isinstance(obj, set):
              return list(obj)
          return super().default(obj)

  data = {'numbers': {1, 2, 3}}
  json_str = json.dumps(data, cls=CustomEncoder)
  print(json_str)  # 输出：{"numbers": [1, 2, 3]}
  ```

- **自定义解码**  
  使用 `json.JSONDecoder` 的子类可以自定义 JSON 解码逻辑。

  ```python
  class CustomDecoder(json.JSONDecoder):
      def __init__(self, *args, **kwargs):
          super().__init__(object_hook=self.object_hook, *args, **kwargs)

      def object_hook(self, obj):
          if 'numbers' in obj:
              obj['numbers'] = set(obj['numbers'])
          return obj

  json_str = '{"numbers": [1, 2, 3]}'
  data = json.loads(json_str, cls=CustomDecoder)
  print(data)  # 输出：{'numbers': {1, 2, 3}}
  ```

#### 3.3 注意事项
- JSON 格式要求键必须是字符串，值可以是字符串、数字、布尔值、列表或对象（字典）。如果需要将其他类型（如 `datetime` 或自定义对象）序列化为 JSON，需要自定义编码逻辑。
- 解码 JSON 数据时，注意数据类型的安全性，避免解析不可信的 JSON 数据。

---

### 4. re 模块

#### 4.1 模块简介
`re` 模块提供了正则表达式的支持，用于字符串的匹配、搜索、替换等操作。它是一个强大的文本处理工具，适用于复杂的文本模式匹配。

#### 4.2 常用功能

##### 4.2.1 匹配模式

- **编译正则表达式**  
  `re.compile(pattern)` 编译一个正则表达式对象，用于后续的匹配操作。

  ```python
  import re
  pattern = re.compile(r'\d+')  # 匹配一个或多个数字
  ```

- **匹配字符串**  
  `re.match(pattern, string)` 从字符串开头开始匹配模式。

  ```python
  match = re.match(pattern, '123abc')
  if match:
      print(match.group())  # 输出：123
  ```

- **搜索字符串**  
  `re.search(pattern, string)` 在字符串中搜索模式。

  ```python
  match = re.search(pattern, 'abc123')
  if match:
      print(match.group())  # 输出：123
  ```

- **查找所有匹配项**  
  `re.findall(pattern, string)` 返回字符串中所有匹配的子串。

  ```python
  matches = re.findall(pattern, 'abc123def456')
  print(matches)  # 输出：['123', '456']
  ```

- **替换字符串**  
  `re.sub(pattern, repl, string)` 替换字符串中匹配的模式。

  ```python
  new_string = re.sub(pattern, 'X', 'abc123def456')
  print(new_string)  # 输出：abcXdefX
  ```

##### 4.2.2 分组和捕获

- **分组捕获**  
  使用圆括号 `()` 在正则表达式中分组捕获匹配的内容。

  ```python
  pattern = re.compile(r'(\d+)-(\d+)')
  match = re.search(pattern, '123-456')
  if match:
      print(match.group(0))  # 输出：123-456
      print(match.group(1))  # 输出：123
      print(match.group(2))  # 输出：456
  ```

- **命名捕获组**  
  使用 `(?P<name>...)` 定义命名捕获组。

  ```python
  pattern = re.compile(r'(?P<first>\d+)-(?P<second>\d+)')
  match = re.search(pattern, '123-456')
  if match:
      print(match.group('first'))  # 输出：123
      print(match.group('second'))  # 输出：456
  ```

##### 4.2.3 贪婪与非贪婪匹配

- **贪婪匹配**  
  默认情况下，正则表达式是贪婪的，会尽可能多地匹配字符。

  ```python
  pattern = re.compile(r'.*')
  match = re.search(pattern, 'abc123')
  if match:
      print(match.group())  # 输出：abc123
  ```

- **非贪婪匹配**  
  在量词后添加 `?` 可以使匹配变为非贪婪。

  ```python
  pattern = re.compile(r'.*?')
  match = re.search(pattern, 'abc123')
  if match:
      print(match.group())  # 输出：空字符串
  ```

#### 4.3 注意事项
- 正则表达式非常强大，但也容易出错。复杂的正则表达式可能难以理解和维护，建议在使用时进行充分测试。
- 在处理大量文本数据时，正则表达式的性能可能是一个问题。如果可能，尽量使用更高效的字符串操作方法（如 `str.split()` 或 `str.replace()`）。

