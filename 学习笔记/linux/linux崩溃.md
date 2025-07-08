### 内存诊断

```bash
apt install memtester
memtester 1G 1    # 测试1GB内存，运行1轮
```

### 存储设备健康

```bash
apt install smartmontools
smartctl -a /dev/sda | grep -i "error\|reallocated"
```

### 查看硬件传感器数据

```bash
apt install lm-sensors
sensors           # 检查当前CPU/主板温度
watch sensors     # 实时监控
```

#### 温度过高自动发送邮件提醒

1. 邮件功能安装

   ```bash
   # 安装
   apt install bsd-mailx
   
   # 邮件配置
   vim /etc/mail.rc
   
   # 可以不配置👇
   set ssl-verify=ignore
   set nss-config-dir=/etc/pki/nssdb
   # 要配置👇
   set from=xxx@163.com
   set smtp=邮件服务器如163的是smtp.163.com
   set smtp-auth-user=xxx@xxx.com
   set smtp-auth-password=登录密码
   set smtp-auth=login
   ```

2. `exim4`安装（Mail Transfer Agent 邮件传输代理）

   ```bash
   # 安装
   apt install exim4
   
   # 配置
   vim /etc/exim4/update-exim4.conf.conf
   # local 改为 internet
   dc_eximconfig_configtype='internet'
   
   # 重启服务
   systemctl restart exim4
   ```

3. 测试

   ```bash
   echo "这是邮件的内容" | mailx -v -s "这是邮件标题" XXX@163.com
   
   #发送代码可抄送密送
   echo "这是邮件的内容" | mailx -b 密送对象邮箱,逗号分隔 -c 抄送对象邮箱,逗号分隔 -v -s "这是邮件标题" xxx@163.com
   ```

4. 温度监控脚本

   ```bash
   #!/bin/bash
   
   # 设置温度阈值
   THRESHOLD=75
   
   # 获取CPU核心温度
   TEMP=$(sensors | grep 'Core 0' | awk '{print $3}' | tr -d '+°C')
   
   # 比较温度与阈值
   if [ $(echo "$TEMP >= $THRESHOLD" | bc) -eq 1 ]; then
       echo "警告：CPU温度过高！当前温度为 ${TEMP}°C" | mail -s "CPU温度警报" xml001001@126.com
   fi
   ```

   > 1. `THRESHOLD=75`：设置温度阈值为75°C。
   >
   > 2. ```bash
   >    TEMP=$(sensors | grep 'Core 0' | awk '{print $3}' | tr -d '+°C')：
   >    ```
   >
   >    - 使用**sensors**命令获取 `Core 0`的温度。
   >    - `grep 'Core 0'`过滤出 `Core 0`的信息。
   >    - `awk '{print $3}'`提取温度值。
   >    - `tr -d '+°C'`去除多余的字符，只保留数值。
   >
   > 3. `if [ $(echo "$TEMP >= $THRESHOLD" | bc) -eq 1 ]; then`：判断温度是否超过阈值。
   >
   > 4. `echo "警告：CPU温度过高！当前温度为 ${TEMP}°C" | mail -s "CPU温度警报" user@example.com`：发送邮件警报。

5. 自动化执行

   ```bash
   chmod +x temp_warnning.sh
   crontab -e
   # 五分钟执行一次
   */5 * * * * /path/to/temp_warnning.sh
   ```
