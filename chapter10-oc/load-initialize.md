### 1> +(void)load
- 当类对象被引入项目时, runtime 会向每一个类对象发送 load 消息
- load 方法会在每一个类甚至分类被引入时仅调用一次,调用的顺序：父类优先于子类, 子类优先于分类
- load 方法不会被类自动继承

---
### 2> +(void)initialize

- 也是在第一次使用这个类的时候会调用这个方法
