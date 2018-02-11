# Ant 的3风格

- – ? 匹配文件名中的一个字符
  – *  匹配文件名中的任意字符
  – ** ** 匹配多重路径
- 例如：RequestMapping("/Hello")
- 可以使用 RequestMapping("/Hello?")匹配/Helloaa
- 可以使用 RequestMapping("/**/Hello?")匹配/aa/aa/Helloaa
- 可以使用 RequestMapping("/*/Hello")匹配/aa/Helloaa