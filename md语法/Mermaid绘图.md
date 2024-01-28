[关于 Mermaid | Mermaid 中文网 (nodejs.cn)](https://mermaid.nodejs.cn/intro/)

## 1. 基础类

### 1.1 流程图

```mermaid
graph TB

id1(圆角矩形)--普通线-->id2[矩形];
subgraph 子图
id2==粗线==>id3{菱形}
id3-. 虚线.->id4>右向旗帜]
id3--无箭头---id5((圆形))
end

```

```mermaid
%% graph TD; comment
graph LR

Zero

A(This is A)

B[This is B]

C([This is C])

D[[This is D]]

E[(Database E)]

F((This is F))

G>This is G]

H{This is H}

I{{This is I}}

J[/J/]

K[\K\]

L[/L\]

M[\ M/]

Zero --> A --> B --> C --> D --> E --> F --> G --> H --> I --> J --> K --> L --> M
```

```mermaid
graph TD

A-->B
C---D
E--RUN!---F
G---|RUN!|H
I -.- J
K .-> L
M -."RUN(!".->N
O ==RUN!==>P
Q --RUN!-->R--STOP!-->S

a --> b & c--> d
e & f--> g & h

```

#### - 方向

| 用词 | 含义     |
| ---- | -------- |
| TB   | 从上到下 |
| BT   | 从下到上 |
| RL   | 从右到左 |
| LR   | 从左到右 |

#### - 节点

| 表述       | 说明           |
| ---------- | -------------- |
| id[文字]   | 矩形节点       |
| id(文字)   | 圆角矩形节点   |
| id((文字)) | 圆形节点       |
| id>文字]   | 右向旗帜状节点 |
| id{文字}   | 菱形节点       |

#### - 连线

| 表述       | 说明           |
| ---------- | -------------- |
| >          | 添加尾部箭头   |
| -          | 不添加尾部箭头 |
| --         | 单线           |
| --text--   | 单线上加文字   |
| `==`       | 粗线           |
| `==text==` | 粗线加文字     |
| -.-        | 虚线           |
| -.text.-   | 虚线加文字     |

### 1.2 时序图

```mermaid
sequenceDiagram

Alice->>John: Hello John, how are you?
loop Healthcheck
  John->>John: Fight against hypochondria
end
Note right of John: Rational thoughts!
   John-->>Alice: Great!
   John->>Bob   : How about you?
   Bob-->>John  : Jolly good!
```

### 1.3 Gantt 图

```mermaid
gantt

section Section
      Completed: done, des1, 2014-01-06, 2014-01-08
      Active   : active, des2, 2014-01-07, 3d
     Parallel 1    : des3, after des1, 1d
     Parallel 2    : des4, after des1, 1d
     Parallel 3    : des5, after des3, 1d
     Parallel 4    : des6, after des4, 1d
```

## 2. 工程类

### 2.1 类图

```mermaid
classDiagram

  Class01 <|-- AveryLongClass: Cool
  <<interface>> Class01
  Class09-->C2: Where am i?
  Class09 --* C3
  Class09 --|> Class07
  Class07: equals()
  Class07: Object[] elementData
  Class01: size()
  Class01: int chimp
  Class01: int gorilla
  class Class10 {
  <<service>>
  int id
  size()
 }
```

### 2.2 状态图

```mermaid
stateDiagram

  [*]-->Active
  state Active {
    [*]-->NumLockOff
    NumLockOff-->NumLockOn : EvNumLockPressed
    NumLockOn-->NumLockOff : EvNumLockPressed
    --
    [*]-->CapsLockOff
    CapsLockOff-->CapsLockOn : EvCapsLockPressed
    CapsLockOn-->CapsLockOff : EvCapsLockPressed
    --
    [*]-->ScrollLockOff
    ScrollLockOff-->ScrollLockOn : EvCapsLockPressed
    ScrollLockOn-->ScrollLockOff : EvCapsLockPressed
}
```

### 2.3 实体关系图

```mermaid
erDiagram
  CUSTOMER ||--o{ORDER : places
  ORDER ||--|{LINE-ITEM : contains
  CUSTOMER}|..|{DELIVERY-ADDRESS : uses
```

### 2.4 Git 图

```mermaid
gitGraph:
options
  {
  "nodeSpacing": 150,
  "nodeRadius": 10
 }
end
commit
branch master
branch newbranch
checkout newbranch
commit
commit
checkout master
commit
commit
merge newbranch
```

## 3. 统计类

### 3.1 饼图

```mermaid
pie

  title Key elements in Product X
  "Calcium" : 42.96
  "Potassium" : 50.05
  "Magnesium" : 10.01
  "Iron" :  5
```

## 4. 其他类

### 4.1 旅程图

```mermaid
journey
  title My working day
  section Go to work
    Make tea: 5: Me
    Go upstairs: 3: Me
    Do work: 1: Me, Cat
  section Go home
    Go downstairs: 5: Me
    Sit down: 3: Me
```

### 4.2 时间线

```mermaid
timeline
    title Major .NET Releases
    section .NET Framework
        2000 - 2005 
             : .NET Framework 1.0
             : .NET Framework 1.0 SP1
             : .NET Framework 1.0 SP2
             : .NET Framework 1.1
             : .NET Framework 1.0 SP3
             : .NET Framework 2.0
        2006 - 2009 
             : .NET Framework 3.0
             : .NET Framework 3.5 
             : .NET Framework 2.0 SP 1 
             : .NET Framework 3.0 SP 1 
             : .NET Framework 2.0 SP 2 
             : .NET Framework 3.0 SP 2 
             : .NET Framework 3.5 SP 1
        2010 - 2015 
             : .NET Framework 4.0
             : .NET Framework 4.5
             : .NET Framework 4.5.1
             : .NET Framework 4.5.2
             : .NET Framework 4.6
             : .NET Framework 4.6.1
    section .NET Core
        2016 - 2017 
             : .NET Core 1.0
             : .NET Core 1.1
             : .NET Framework 4.6.2
             : .NET Core 2.0
             : .NET Framework 4.7
             : .NET Framework 4.7.1
        2018 - 2019 
             : .NET Core 2.1
             : .NET Core 2.2
             : .NET Framework 4.7.2             
             : .NET Core 3.0
             : .NET Core 3.1
             : .NET Framework 4.8
    section Modern .NET
        2020 : .NET 5
        2021 : .NET 6
        2022 : .NET 7
             : .NET Framework 4.8.1
```

