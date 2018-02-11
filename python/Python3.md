# Python3

## 1.简单实用

- 命令行

  ```bash
  C:\Users\Administrator>python
  Python 3.6.4 (v3.6.4:d48eceb, Dec 19 2017, 06:54:40) [MSC v.1900 64 bit (AMD64)]
   on win32
  Type "help", "copyright", "credits" or "license" for more information.
  >>> 100+200
  300
  >>> print('hello');
  hello
  >>> exit()

  ```

- 文件 `.py`

  ```python
  python test.py

  # test.py

  ```

  ​

- 输入与输出

  `print()`会依次打印每个字符串，遇到逗号“,”会输出一个空格，因此，输出的字符串是这样拼起来的：

  ![print-explain](https://cdn.liaoxuefeng.com/cdn/files/attachments/001431643506965540b8016b45c4d27b84c734543f78840000/l)

  - ###### 输入

    ```python
    name = input("prompt");
    print(name);
    ```

  - ###### 