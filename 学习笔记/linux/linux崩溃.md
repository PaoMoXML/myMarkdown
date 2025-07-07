#### 内存诊断

```bash
apt install memtester
memtester 1G 1    # 测试1GB内存，运行1轮
```

#### 存储设备健康

```bash
apt install smartmontools
smartctl -a /dev/sda | grep -i "error\|reallocated"
```

#### 查看硬件传感器数据

```bash
apt install lm-sensors
sensors           # 检查当前CPU/主板温度
```

