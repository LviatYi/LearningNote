# 20. 备忘录模式

## 20.1 模式动机

软件经常遇到撤销的需求。撤销即将系统恢复到某个历史状态。

备忘录模式提供了一种状态恢复的解决方案，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原。

## 20.2 模式定义

> **备忘录模式** (Memento Pattern) 又称为 **记号模式** (Token)，它属于对象行为型模式。
>
> - 在不破坏封装的前提下，捕获一个对象的内部状态。
> - 在该对象之外保存这个状态，以便在以后将对象恢复到原先保存的状态。

## 20.3 模式结构

备忘录模式包含如下角色：

- Originator 原发器
  - 一般将需要保存内部状态的类设计为原发器。
  - 可以创建一个备忘录，并存储它的当前内部状态。
  - 可以使用备忘录来恢复其内部状态。
- Memento 备忘录
  - 存储原发器的内部状态。
  - 备忘录的设计一般参考原发器的设计，根据实际需要确定备忘录类中的属性，以决定保存哪些内部状态。
  - 备忘录对象不能直接供其他类使用，原发器的设计在不同的编程语言中实现机制会有所不同。
- Caretaker 负责人
  - 又称为管理者。
  - 负责保存备忘录，但是不能对备忘录的内容进行操作或检查。
  - 可以 **存储** 一个或多个备忘录对象，且只负责存储对象，而不能修改对象，也无须知道对象的实现细节。

```PlantUML
@startuml Mediator_ClassDiagram
hide empty members

class Originator{
    - state
    + restoreMemnto(Memento m)
    + createMemento()
}

class Memento{
    - state
    + getState()
    + setState()
}

class Caretaker{
}

Originator .right.> Memento
Memento <--o Caretaker
note bottom of Originator
    restorMemento(Memento m){
        state = m.getState();
    }

    createMemento( ){
        return new Memento(state);
    }
end note
@enduml
```

## 20.4 时序图

## 20.5 代码分析

```JAVA
public class Originator {
    private String state;

    public Originator(){}

　　// 创建一个备忘录对象
    public Memento createMemento() {
　　　　return new Memento(this);
    }

　　// 根据备忘录对象恢复原发器状态
    public void restoreMemento(Memento m) {
　　　　 state = m.state;
    }

    public void setState(String state) {
        this.state=state;
    }

    public String getState() {
        return this.state;
    }
}
```

```JAVA
class Memento {
    private String state;

    public Memento(Originator o) {
　　　　state = o.getState();
    }

    public void setState(String state) {
        this.state=state;
    }

    public String getState() {
        return this.state;
    }
}
```

```JAVA
public class Caretaker {
    private Memento memento;

    public Memento getMemento() {
        return memento;
    }

    public void setMemento(Memento memento) {
        this.memento=memento;
    }
}
```

## 20.6 模式分析

- 除 Originator 类，不允许其他类调用 Memento 类的构造函数与相关方法，以免破坏封装性。
- 一般地，Caretaker 仅用于存储。

## 20.7 实例

## 20.8 优点

- 提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原。
- 实现了对信息的封装，一个备忘录对象是一种原发器对象状态的表示，不会被其他代码所改动。备忘录保存了原发器的状态，采用列表、堆栈等集合来存储备忘录对象可以实现多次撤销操作。

## 20.9 缺点

- 资源消耗过大，如果需要保存的原发器类的成员变量太多，就不可避免需要占用大量的存储空间，每保存一次对象的状态都需要消耗一定的系统资源。

## 20.10 适用环境

- 保存一个对象在某一个时刻的全部状态或部分状态，这样以后需要时它能够恢复到先前的状态，实现撤销操作。
- 防止外界对象破坏一个对象历史状态的封装性，避免将对象历史状态的实现细节暴露给外界对象。

## 20.11 模式应用

## 20.12 模式扩展

### 20.12.1 多次撤销与重做

在负责人类中定义一个集合，并实现正向与逆向遍历，即可实现多步撤销与重做。
