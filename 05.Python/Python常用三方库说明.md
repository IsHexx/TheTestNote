## Python 常用三方库说明

### 1. Pandas

#### 1.1 使用领域
Pandas 是一个开源的数据分析和操作库，广泛应用于数据科学、数据分析、金融、生物信息学等领域。它提供了强大的数据结构和数据分析工具，特别适合处理表格数据（如 CSV 文件）。

#### 1.2 常用功能

##### 1.2.1 数据结构
- **DataFrame**：二维表格数据结构，类似于 Excel 表格。
- **Series**：一维数组，类似于 Python 列表或 NumPy 数组。

##### 1.2.2 创建 DataFrame
```python
import pandas as pd

data = {'Name': ['Alice', 'Bob', 'Charlie'],
        'Age': [25, 30, 35],
        'City': ['New York', 'Los Angeles', 'Chicago']}
df = pd.DataFrame(data)
print(df)
```

##### 1.2.3 数据读取和写入
- **读取 CSV 文件**：
  ```python
  df = pd.read_csv('data.csv')
  ```
- **写入 CSV 文件**：
  ```python
  df.to_csv('output.csv', index=False)
  ```

##### 1.2.4 数据筛选和操作
- **筛选数据**：
  ```python
  filtered_df = df[df['Age'] > 30]
  print(filtered_df)
  ```
- **添加列**：
  ```python
  df['Salary'] = [50000, 60000, 70000]
  print(df)
  ```

##### 1.2.5 数据聚合
```python
grouped = df.groupby('City').mean()
print(grouped)
```

### 2. NumPy

#### 2.1 使用领域
NumPy 是 Python 中用于科学计算的核心库，广泛应用于数据分析、机器学习、图像处理等领域。它提供了高性能的多维数组对象和大量操作这些数组的工具。

#### 2.2 常用功能

##### 2.2.1 创建数组
```python
import numpy as np

array = np.array([1, 2, 3, 4, 5])
print(array)
```

##### 2.2.2 数组操作
- **数组形状**：
  ```python
  print(array.shape)
  ```
- **数组重塑**：
  ```python
  reshaped_array = array.reshape((5, 1))
  print(reshaped_array)
  ```

##### 2.2.3 数学运算
- **数组加法**：
  ```python
  array1 = np.array([1, 2, 3])
  array2 = np.array([4, 5, 6])
  result = array1 + array2
  print(result)
  ```

##### 2.2.4 随机数生成
```python
random_array = np.random.rand(3, 3)  # 生成 3x3 的随机数组
print(random_array)
```

### 3. Requests

#### 3.1 使用领域
Requests 是一个简单易用的 HTTP 库，用于发送 HTTP 请求。它广泛应用于 Web 开发、爬虫开发、API 调用等领域。

#### 3.2 常用功能

##### 3.2.1 发送 GET 请求
```python
import requests

response = requests.get('https://api.github.com')
print(response.status_code)  # 输出：200
print(response.json())  # 输出：JSON 数据
```

##### 3.2.2 发送 POST 请求
```python
data = {'key': 'value'}
response = requests.post('https://httpbin.org/post', data=data)
print(response.json())
```

##### 3.2.3 处理响应
```python
print(response.headers)  # 输出：响应头
print(response.text)  # 输出：响应内容
```

### 4. Flask

#### 4.1 使用领域
Flask 是一个轻量级的 Web 框架，适合开发小型 Web 应用、API 和微服务。它提供了灵活的路由、模板渲染等功能。

#### 4.2 常用功能

##### 4.2.1 创建 Web 应用
```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Flask!"

@app.route('/api/data', methods=['GET'])
def get_data():
    return jsonify({'message': 'Hello, API!'})

if __name__ == '__main__':
    app.run(debug=True)
```

##### 4.2.2 模板渲染
```python
from flask import render_template

@app.route('/template')
def template_example():
    return render_template('index.html', name='Alice')
```

### 5. Django

#### 5.1 使用领域
Django 是一个全栈 Web 框架，适合开发大型 Web 应用、企业级应用和内容管理系统。它提供了强大的 ORM、模板引擎、用户认证等功能。

#### 5.2 常用功能

##### 5.2.1 创建项目和应用
```bash
django-admin startproject myproject
cd myproject
python manage.py startapp myapp
```

##### 5.2.2 定义模型
```python
# myapp/models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
```

##### 5.2.3 创建视图
```python
# myapp/views.py
from django.shortcuts import render
from .models import Post

def home(request):
    posts = Post.objects.all()
    return render(request, 'home.html', {'posts': posts})
```

### 6. FastAPI

#### 6.1 使用领域
FastAPI 是一个现代的、快速的（高性能）Web 框架，用于构建 API。它基于 Python 类型注解，支持自动文档生成，适合开发 RESTful API 和 GraphQL API。

#### 6.2 常用功能

##### 6.2.1 创建 API
```python
from fastapi import FastAPI

app = FastAPI()

@app.get('/')
def read_root():
    return {'message': 'Hello, FastAPI!'}
```

##### 6.2.2 请求参数和路径参数
```python
@app.get('/items/{item_id}')
def read_item(item_id: int, query: str = None):
    return {'item_id': item_id, 'query': query}
```

### 7. SQLAlchemy

#### 7.1 使用领域
SQLAlchemy 是一个功能强大的 ORM（对象关系映射）库，用于将 Python 对象映射到数据库表。它广泛应用于需要与数据库交互的 Web 应用和数据分析项目。

#### 7.2 常用功能

##### 7.2.1 定义模型
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)
```

##### 7.2.2 创建会话和操作数据库
```python
engine = create_engine('sqlite:///example.db')
Session = sessionmaker(bind=engine)
session = Session()

# 添加数据
new_user = User(name='Alice', age=25)
session.add(new_user)
session.commit()
```

### 8. BeautifulSoup

#### 8.1 使用领域
BeautifulSoup 是一个用于解析 HTML 和 XML 文档的库，广泛应用于 Web 爬虫开发和数据抓取。

#### 8.2 常用功能

##### 8.2.1 解析 HTML
```python
from bs4 import BeautifulSoup

html = '''
<html>
    <body>
        <h1>Hello, BeautifulSoup!</h1>
        <p>This is a paragraph.</p>
    </body>
</html>
'''

soup = BeautifulSoup(html, 'html.parser')
print(soup.prettify())
```

##### 8.2.2 提取数据
```python
title = soup.find('h1').text
print(title)  # 输出：Hello, BeautifulSoup!
```

### 9. Matplotlib

#### 9.1 使用领域
Matplotlib 是一个用于数据可视化的库，广泛应用于数据分析、机器学习、科学研究等领域。它支持绘制各种图表，如折线图、柱状图、散点图等。

#### 9.2 常用功能

##### 9.2.1 绘制折线图
```python
import matplotlib.pyplot as plt

x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]
plt.plot(x, y)
plt.title('Line Plot')
plt.xlabel('X-axis')
plt.ylabel('Y-axis')
plt.show()
```

##### 9.2.2 绘制柱状图
```python
plt.bar(x, y)
plt.title('Bar Chart')
plt.xlabel('X-axis')
plt.ylabel('Y-axis')
plt.show()
```

### 10. OpenCV

#### 10.1 使用领域
OpenCV 是一个用于计算机视觉和图像处理的库，广泛应用于图像识别、视频处理、机器视觉等领域。

#### 10.2 常用功能

##### 10.2.1 读取和显示图像
```python
import cv2

image = cv2.imread('image.jpg')
cv2.imshow('Image', image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

##### 10.2.2 图像处理
```python
gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
cv2.imshow('Gray Image', gray_image)
cv2.waitKey(0)
```

### 11. TensorFlow

#### 11.1 使用领域
TensorFlow 是一个开源的机器学习框架，广泛应用于深度学习、神经网络、自然语言处理等领域。

#### 11.2 常用功能

##### 11.2.1 构建简单模型
```python
import tensorflow as tf

model = tf.keras.Sequential([
    tf.keras.layers.Dense(10, activation='relu', input_shape=(4,)),
    tf.keras.layers.Dense(3, activation='softmax')
])

model.compile(optimizer='adam', loss='sparse_categorical_crossentropy', metrics=['accuracy'])
```

##### 11.2.2 训练模型
```python
import numpy as np

# 示例数据
x_train = np.random.rand(100, 4)
y_train = np.random.randint(0, 3, 100)

model.fit(x_train, y_train, epochs=10)
```

### 12. PyTorch

#### 12.1 使用领域
PyTorch 是一个开源的机器学习库，广泛应用于深度学习、计算机视觉、自然语言处理等领域。它以动态计算图和易用性著称。

#### 12.2 常用功能

##### 12.2.1 构建简单模型
```python
import torch
import torch.nn as nn

class SimpleModel(nn.Module):
    def __init__(self):
        super(SimpleModel, self).__init__()
        self.fc1 = nn.Linear(4, 10)
        self.fc2 = nn.Linear(10, 3)

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        x = self.fc2(x)
        return x

model = SimpleModel()
```

##### 12.2.2 训练模型
```python
criterion = nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)

# 示例数据
x_train = torch.randn(100, 4)
y_train = torch.randint(0, 3, (100,))

for epoch in range(10):
    optimizer.zero_grad()
    outputs = model(x_train)
    loss = criterion(outputs, y_train)
    loss.backward()
    optimizer.step()
    print(f'Epoch {epoch+1}, Loss: {loss.item()}')
```

