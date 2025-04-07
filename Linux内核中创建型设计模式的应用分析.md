# Linux内核中创建型设计模式的应用分析

Linux内核虽然没有显式使用面向对象的设计模式（主要用C语言编写），但许多创建型模式的概念在架构中得到了体现。

## 1. 工厂方法模式(Factory Method)

**应用场景**：设备驱动模型中的设备创建

### UML类图

```plantuml
@startuml
class Device {
    + operations
}

class Driver {
    + probe()
    + remove()
}

Driver "1" *-- "n" Device : 创建

class PCI_Device extends Device
class PCI_Driver extends Driver
@enduml

### UML顺序图

````plantuml
@startuml
participant "Bus Driver" as bus
participant "PCI Core" as pci
participant "PCI Driver" as driver

bus -> pci : detect
pci -> pci : enumerate
pci -> driver : match device/driver
driver -> driver : probe()
driver --> pci : device created
@enduml

## 2. 抽象工厂模式(Abstract Factory)

**应用场景**：虚拟文件系统(VFS)中的对象创建

### UML类图

```plantuml
@startuml
interface VFS_Factory {
    + create_inode()
    + create_dentry()
    + create_file()
}

class Ext4_Factory implements VFS_Factory {
    + create_inode()
    + create_dentry()
    + create_file()
}
@enduml

## 3.建造者模式(Builder)

**应用场景**：sk_buff网络数据包的构建

### UML类图

```plantuml
@startuml
class sk_buff {
    - data
    - len
    - truesize
}

class sk_build {
    + alloc_skb()
    + add_header()
    + add_data()
    + finalize()
}

sk_build --> sk_buff : 构建
@enduml

### UML顺序图

@startuml
participant "Network Stack" as net
participant "sk_build" as builder
participant "Memory Allocator" as mem

net -> builder : create
builder -> mem : alloc_skb
mem --> builder : skb
net -> builder : add hdr
builder -> builder : add_header
net -> builder : add data
builder -> builder : add_data
net -> builder : finalize
builder -> builder : finalize
builder --> net : complete
@enduml

## 4.原型模式(Prototype)

**应用场景**：进程创建(fork系统调用)

### UML类图

```plantuml
@startuml
class task_struct {
    + clone()
}

class fork {
    + do_fork()
}

fork -> task_struct : clone()
@enduml

### UML顺序图

@startuml
participant "Process A" as pa
participant "Kernel" as kernel
participant "Process B" as pb

pa -> kernel : fork
kernel -> kernel : copy task
kernel -> kernel : create new
kernel --> pa : return pid
kernel -> pb : start
@enduml

## 5.单例模式(Singleton)

**应用场景**：内核中的全局唯一对象，如init进程

### UML类图

```plantuml
@startuml
class init_task {
    - instance