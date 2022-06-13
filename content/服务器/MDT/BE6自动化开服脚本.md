```json
{
    "date":"2022.06.12 15:58",
    "tags": ["BLOG","MDT","Mindustry","Server"],
    "title": "Mindustry的自动化开服更新脚本",
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```
# 系统要求Centos

## 脚本

 1. 安装脚本

    ```shell
    #!/bin/bash
    echo -e "欢迎使用\033[36mBE6\033[0m自动化环境安装脚本"
    echo -e "QQ群\033[36m780806682\033[0m"
    echo -e "第二次启动代码"
    echo -e "\033[4;36mcd xs && sh start.sh\033[0m"
    systemctl stop firewalld.service
    echo -e "\033[31m防火墙以关闭\033[0m"
    sleep 5s
    
    echo -e "…\033[36m正在安装Java环境\033[0m…"
    
    yum -y install java-1.8.0-openjdk*
    
    echo -e "…\033[36m安装压缩包管理工具\033[0m…"
    
    yum install -y unzip zip
    
    echo -e "………\033[36m1.进程名 小白单服 大佬开环境隔离\033[0m………"
    echo -e "………\033[36m2.端口号 小白多服 手动输入端口号\033[0m………"
    read -p "请输入需要的模式: " mode
    case $mode in
    "1")
        echo -e "欢迎使用\033[36mBE6\033[0m自动化开服脚本"
        echo -e "………\033[36m更新配置文件中\033[0m………"
        wget http://be6.top/xsgc/name.sh
        chmod +x name.sh
        ./name.sh && >>./shpid
        ;;
    "2")
        echo -e "欢迎使用\033[36mBE6\033[0m自动化开服脚本"
        echo -e "………\033[36m更新配置文件中\033[0m………"
        read -p "请输入端口号: " port
        echo -e "………\033[36m端口号：$port\033[0m………"
        read -p "二次确认: " port0
        if [[ $port == $port0 ]]; then
            $port >>port
            wget http://be6.top/xsgc/port.sh
            chmod +x port.sh
            ./port.sh && >>./shpid
        else
            echo -e "………\033[36m端口号不同\033[0m………"
            read -p "请输入端口号: " port
            echo -e "………\033[36m端口号：$port\033[0m………"
            read -p "二次确认: " port0
            $port >>port
            wget http://be6.top/xsgc/port.sh
            chmod +x port.sh
            ./port.sh && >>./shpid
        fi
    esac
    wget http://be6.top/xsgc/start.sh
    chmod +x start.sh
    curl https://github.com/Anuken/Mindustry 2>/dev/null | grep -E 'href="/Anuken/Mindustry/releases/tag/v[0-9]*' | sed 's/^.*\/tag\/v//g' | sed 's/\.[0-9]">.*$//g' | sed 's/">.*$//g' >>./bb1
    echo -e "………\033[36m 游戏版本获取成功 \033[0m………"
    curl https://github.com/way-zer/ScriptAgent4MindustryExt 2>/dev/null | grep -E '/way-zer/ScriptAgent4MindustryExt/releases/tag/v[0-9]*' | sed 's/^.*\/tag\/v//g' | sed 's/">.*$//g' >>./bb3
    echo -e "………\033[36m 插件版本获取成功 \033[0m………"
    data1=$(cat bb1)
    data3=$(cat bb3)
    curl http://www.iyuji.cn/iyuji/s/Zk9HWjhRNk5tWmVYTklQS2FRRmxxUT09/1612931423341522 2>/dev/null | grep -E '<div>【wlgg】' | sed 's/<div>【wlgg】/【网络公告】/g' | sed 's/{wlgg}<\/div>/\n/g' | sed 's/^.*【wlggt】/【网络公告】/g' | sed 's/{wlggw}.*$//g'
    echo -e "………\033[36m下载游戏核心中\033[0m………"
    wget -O server.jar https://gh.api.99988866.xyz/https://github.com/Anuken/Mindustry/releases/download/v${data1}/server-release.jar
    mv bb1 bb2
    echo -e "………\033[36m下载配置文件中\033[0m………"
    echo -e "-----\033[36m插件官网\033[0m-----"
    echo -e "\033[36mhttps://github.com/way-zer/ScriptAgent4MindustryExt\033[0m"
    wget -O config.zip http://be6.top/xsgc/config.zip -q
    wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
    echo -e "………\033[36m解压配置文件中\033[0m………"
    jar=$(curl https://api.github.com/repos/way-zer/ScriptAgent4MindustryExt/releases/latest 2>/dev/null | grep -E '"browser_download_url": ".*+jar"' | sed 's/^.*"browser_download_url": "//g' | sed 's/"//g')
    wget -O wayzer.jar $jar
    rm -f config/mods/wayzer.jar
    mv wayzer.jar config/mods/wayzer.jar
    unzip config.zip
    rm -f config.zip
    echo -e "………\033[36ms删除旧配置中\033[0m………"
    mv bb3 bb4
    rm -rf config/scripts/
    echo -e "………\033[36m解压脚本文件中\033[0m………"
    unzip -o scripts.zip -d config/scripts/
    rm -f scripts.zip
    echo -e "………\033[36m启动服务器\033[0m………"
    java -jar server.jar
    ```

 2. 自动更新脚本

    ```shell
    #!/bin/bash
    echo -e "欢迎使用\033[36mBE6\033[0m自动化开服脚本"
    curl http://www.iyuji.cn/iyuji/s/Zk9HWjhRNk5tWmVYTklQS2FRRmxxUT09/1612931423341522 2>/dev/null | grep -E '<p>【wlgg】' | sed 's/<p>【wlgg】/【网络公告】/g' | sed 's/{wlgg}<\/p>/\n/g' | sed 's/^.*【wlggt】/【网络公告】/g' | sed 's/{wlggw}.*$//g' | sed 's/<br>//g' | sed 's/<p><\/p>//g'
    systemctl stop firewalld.service
    echo -e "\033[31m防火墙以关闭\033[0m"
    sleep 5s
    shpid=$(cat shpid)
    kill -9 $shpid
    rm -f shpid
    rm -f bb1
    rm -f bb3
    echo -e "………\033[36m检测更新中\033[0m………"
    curl https://github.com/Anuken/Mindustry 2>/dev/null | grep -E 'href="/Anuken/Mindustry/releases/tag/v[0-9]*' | sed 's/^.*\/tag\/v//g' | sed 's/\.[0-9]">.*$//g' | sed 's/">.*$//g' >>./bb1
    echo -e "………\033[36m 游戏版本获取成功 \033[0m………"
    curl https://github.com/way-zer/ScriptAgent4MindustryExt 2>/dev/null | grep -E '/way-zer/ScriptAgent4MindustryExt/releases/tag/v[0-9]*' | sed 's/^.*\/tag\/v//g' | sed 's/">.*$//g' >>./bb3
    echo -e "………\033[36m 插件版本获取成功 \033[0m………"
    data1=$(cat bb1)
    data2=$(cat bb2)
    data3=$(cat bb3)
    data4=$(cat bb4)
    if [ -e name.sh ]; then
        ./name.sh && >>./shpid
    fi
    if [ -e port.sh ]; then
        ./port.sh && >>./shpid
    fi
    echo -e "………\033[36m 游戏核心 \033[0m………"
    echo -e "\033[36m最新版本\t当前版本\033[0m\n$data1\t$data2"
    echo -e "………\033[36m=============\033[0m………"
    echo -e "………\033[36m 游戏插件 \033[0m………"
    echo -e "\033[36m最新版本\t当前版本\033[0m\n$data3\t$data4"
    if [[ $data1 == $data2 ]]; then
        echo -e "………\033[36m核心没有更新\033[0m………"
        if [[ $data3 == $data4 ]]; then
            echo -e "………\033[36m插件没有更新\033[0m………"
            java -jar server.jar
        else
            echo -e "………\033[36m更新配置文件中\033[0m………"
            echo -e "………\033[36m更新说明\033[0m………"
            curl https://api.github.com/repos/way-zer/ScriptAgent4MindustryExt/releases/latest 2>/dev/null | grep -E '"body": "' | sed 's/^.*"body": "//g' | sed 's/\\r\\n*/\n/g' | sed 's/".*$//g'
            echo -e "-----\033[36m插件官网\033[0m-----"
            sleep 5s
            echo -e "\033[36mhttps://github.com/way-zer/ScriptAgent4MindustryExt\033[0m"
            jar=$(curl https://api.github.com/repos/way-zer/ScriptAgent4MindustryExt/releases/latest 2>/dev/null | grep -E '"browser_download_url": ".*+jar"' | sed 's/^.*"browser_download_url": "//g' | sed 's/"//g')
            wget -O wayzer.jar $jar
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            echo -e "………\033[36m删除旧配置中\033[0m………"
            rm -f bb4
            rm -f config/mods/wayzer.jar
            mv bb3 bb4
            mv wayzer.jar config/mods/wayzer.jar
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            echo -e "………\033[36m解压配置文件中\033[0m………"
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar
        fi
    else
        echo -e "………\033[36m更新游戏核心中\033[0m………"
        echo -e "………\033[36m更新说明\033[0m………"
        curl https://api.github.com/repos/Anuken/Mindustry/releases/latest 2>/dev/null | grep -E '"body": "' | sed 's/^.*"body": "//g' | sed 's/".*$//g' | sed 's/\\n-/\n/g'
        sleep 5s
        rm -f bb2
        mv bb1 bb2
        rm -f server.jar
        wget -O server.jar https://gh.api.99988866.xyz/https://github.com/Anuken/Mindustry/releases/download/v${data1}/server-release.jar
        if [[ $data3 == $data4 ]]; then
            echo -e "………\033[36m插件没有更新\033[0m………"
            java -jar server.jar &
        else
            echo -e "………\033[36m更新配置文件中\033[0m………"
            echo -e "………\033[36m更新说明\033[0m………"
            curl https://api.github.com/repos/way-zer/ScriptAgent4MindustryExt/releases/latest 2>/dev/null | grep -E '"body": "' | sed 's/^.*"body": "//g' | sed 's/\\r\\n*/\n/g' | sed 's/".*$//g'
            echo -e "-----\033[36m插件官网\033[0m-----"
            sleep 5s
            echo -e "\033[36mhttps://github.com/way-zer/ScriptAgent4MindustryExt\033[0m"
            jar=$(curl https://api.github.com/repos/way-zer/ScriptAgent4MindustryExt/releases/latest 2>/dev/null | grep -E '"browser_download_url": ".*+jar"' | sed 's/^.*"browser_download_url": "//g' | sed 's/"//g')
            wget -O wayzer.jar $jar
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            echo -e "………\033[36m删除旧配置中\033[0m………"
            rm -f bb4
            rm -f config/mods/wayzer.jar
            mv bb3 bb4
            mv wayzer.jar config/mods/wayzer.jar
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            echo -e "………\033[36m解压配置文件中\033[0m………"
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar
        fi
    fi
    ```

 3. 基于进程名的自动更新脚本

    ```shell
    #!/bin/bash
    APP_NAME=server.jar
    while true
    do
    sleep 1h
    curl https://github.com/Anuken/Mindustry 2>/dev/null | grep -E 'href="/Anuken/Mindustry/releases/tag/v[0-9]*' | sed 's/^.*  <a class="link-gray-dark d-flex no-underline" href="\/Anuken\/Mindustry\/releases\/tag\/v//g' | sed 's/\.[0-9]">.*$//g' | sed 's/">.*$//g' >>./bb1
    curl https://github.com/way-zer/ScriptAgent4MindustryExt 2>/dev/null | grep -E '/way-zer/ScriptAgent4MindustryExt/releases/tag/v[0-9]*' | sed 's/^.*<a class="link-gray-dark d-flex no-underline" href="\/way-zer\/ScriptAgent4MindustryExt\/releases\/tag\/v//g' | sed 's/">.*$//g' >>./bb3
    data1=$(cat bb1)
    data2=$(cat bb2)
    data3=$(cat bb3)
    data4=$(cat bb4)
    if [[ $data1 == $data2 ]]; then
        if [[ $data3 == $data4 ]]; then
        else
            pid=$(ps -ef | grep $APP_NAME | grep -v grep | awk '{print $2}')
            kill -9 $pid
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            rm -f bb4
            mv bb3 bb4
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar &
        fi
    else
        pid=$(ps -ef | grep $APP_NAME | grep -v grep | awk '{print $2}')
        kill -9 $pid
        rm -f bb2
        mv bb1 bb2
        rm -f server.jar
        wget -O server.jar https://gh.api.99988866.xyz/https://github.com/Anuken/Mindustry/releases/download/v${data1}/server-release.jar
        if [[ $data3 == $data4 ]]; then
        java -jar server.jar &
        else
            pid=$(ps -ef | grep $APP_NAME | grep -v grep | awk '{print $2}')
            kill -9 $pid
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            rm -f bb4
            mv bb3 bb4
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar &
        fi
    fi
    done
    ```

 4. 基于端口号的更新脚本

    ```shell
    #!/bin/bash
    port=$(cat port)
    while true
    do
    sleep 1h
    curl https://github.com/Anuken/Mindustry 2>/dev/null | grep -E 'href="/Anuken/Mindustry/releases/tag/v[0-9]*' | sed 's/^.*  <a class="link-gray-dark d-flex no-underline" href="\/Anuken\/Mindustry\/releases\/tag\/v//g' | sed 's/\.[0-9]">.*$//g' | sed 's/">.*$//g' >>./bb1
    curl https://github.com/way-zer/ScriptAgent4MindustryExt 2>/dev/null | grep -E '/way-zer/ScriptAgent4MindustryExt/releases/tag/v[0-9]*' | sed 's/^.*<a class="link-gray-dark d-flex no-underline" href="\/way-zer\/ScriptAgent4MindustryExt\/releases\/tag\/v//g' | sed 's/">.*$//g' >>./bb3
    data1=$(cat bb1)
    data2=$(cat bb2)
    data3=$(cat bb3)
    data4=$(cat bb4)
    if [[ $data1 == $data2 ]]; then
        if [[ $data3 == $data4 ]]; then
        else
            pid pid=$(netstat -nlp | grep :$port | awk '{print $7}' | awk -F"/" '{ print $1 }') 
            kill -9 $pid
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            rm -f bb4
            mv bb3 bb4
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar &
        fi
    else
        pid pid=$(netstat -nlp | grep :$port | awk '{print $7}' | awk -F"/" '{ print $1 }') 
        kill -9 $pid
        rm -f bb2
        mv bb1 bb2
        rm -f server.jar
        wget -O server.jar https://gh.api.99988866.xyz/https://github.com/Anuken/Mindustry/releases/download/v${data1}/server-release.jar
        if [[ $data3 == $data4 ]]; then
            java -jar server.jar &
        else
            pid pid=$(netstat -nlp | grep :$port | awk '{print $7}' | awk -F"/" '{ print $1 }') 
            kill -9 $pid
            wget -O scripts.zip https://gh.api.99988866.xyz/https://github.com/way-zer/ScriptAgent4MindustryExt/releases/download/v${data3}/ScriptAgent4Mindustry-${data3}-scripts.zip
            rm -f bb4
            mv bb3 bb4
            mv config/scripts/data /be6/data
            rm -rf config/scripts/
            unzip -o scripts.zip -d config/scripts/
            rm -f scripts.zip
            mv /be6/data/ config/scripts/data/
            java -jar server.jar &
        fi
    fi
    done
    ```

 5. 服务器状态信息通知脚本

    ```shell
    #!/bin/bash
    if [ -e /be6server ]; then
        echo -e "欢迎使用\033[36mBE6\033[0m自动检测脚本"
    else
        echo -e "欢迎使用\033[36mBE6\033[0m自动检测脚本"
        echo -e "QQ群\033[36m780806682\033[0m"
        echo -e "Server酱\033[36mhttps://sc.ftqq.com/\033[0m"
        read -p "请输入Server酱key: " send
        read -p "请输入检测服务器名称；" name
        read -p "请输入端口号；" port
        echo "$send" >>/be6server
        echo "$name" >>/be6server
        echo "$port" >>/be6server
    fi
    rm -f /be6cpu.info
    cpu_bit=$(getconf LONG_BIT)
    total_processor_num=$(cat /proc/cpuinfo | grep "processor" | wc -l)
    CPU_name=$(cat /proc/cpuinfo | grep "model name" | uniq | sed 's/^.*://g')
    TIME_INTERVAL=5
    time=$(date "+%Y-%m-%d %H:%M:%S")
    LAST_CPU_INFO=$(cat /proc/stat | grep -w cpu | awk '{print $2,$3,$4,$5,$6,$7,$8}')
    LAST_SYS_IDLE=$(echo $LAST_CPU_INFO | awk '{print $4}')
    LAST_TOTAL_CPU_T=$(echo $LAST_CPU_INFO | awk '{print $1+$2+$3+$4+$5+$6+$7}')
    sleep ${TIME_INTERVAL}
    NEXT_CPU_INFO=$(cat /proc/stat | grep -w cpu | awk '{print $2,$3,$4,$5,$6,$7,$8}')
    NEXT_SYS_IDLE=$(echo $NEXT_CPU_INFO | awk '{print $4}')
    NEXT_TOTAL_CPU_T=$(echo $NEXT_CPU_INFO | awk '{print $1+$2+$3+$4+$5+$6+$7}')
    SYSTEM_IDLE=$(echo ${NEXT_SYS_IDLE} ${LAST_SYS_IDLE} | awk '{print $1-$2}')
    TOTAL_TIME=$(echo ${NEXT_TOTAL_CPU_T} ${LAST_TOTAL_CPU_T} | awk '{print $1-$2}')
    CPU_USAGE=$(echo ${SYSTEM_IDLE} ${TOTAL_TIME} | awk '{printf "%.2f", 100-$1/$2*100}')
    echo "![BE6.RUN](https://img.shields.io/badge/FOR-BE6.RUN-blue)" >>/be6cpu.info
    echo "# ***CPU***" >>/be6cpu.info
    echo ">当前**时间**-> *$time*" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">CPU-名字: **$CPU_name**" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">处理器数: *$total_processor_num*" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">CPU-BIT: ***$cpu_bit***" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">CPU *使用率*:**${CPU_USAGE}**%" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo "***" >>/be6cpu.info
    total_mem=$(grep MemTotal /proc/meminfo | sed 's/^[^0-9]*//g' | sed 's/kB//g')
    total_mem_g=$(($total_mem / 1048576))
    memory_Hz=$(dmidecode | grep -A16 "Memory Device" | grep 'Speed' | uniq | grep 'Hz' | sed 's/^.*: //g' | uniq)
    memory_max=$(dmidecode | grep "Maximum Capacity" | sed 's/^.*: //g')
    memory_list=$(dmidecode | grep -A5 "Memory Device" | grep Size)
    memory_num=$(dmidecode | grep -A5 "Memory Device" | grep Size -c)
    memory_num_used=$(dmidecode | grep -A5 "Memory Device" | grep MB -c)
    total=$(free -m | sed -n '2p' | awk '{print $2}')
    used=$(free -m | sed -n '2p' | awk '{print $3}')
    free=$(free -m | sed -n '2p' | awk '{print $4}')
    shared=$(free -m | sed -n '2p' | awk '{print $5}')
    buff=$(free -m | sed -n '2p' | awk '{print $6}')
    cached=$(free -m | sed -n '2p' | awk '{print $7}')
    rate=$(echo "scale=2;$used/$total" | bc | awk -F. '{print $2}')
    echo "# ***内存***" >>/be6cpu.info
    echo ">总内存: $total_mem_g G" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo -e ">**${used}**M/**${total}**M\n\n>*使用率*:**${rate}**%" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo "***" >>/be6cpu.info
    echo "# ***硬盘***" >>/be6cpu.info
    DEV=$(df -hP | grep '^/dev/*' | cut -d' ' -f1 | sort)
    for I in $DEV; do
        dev=$(df -Ph | grep $I | awk '{print $1}')
        size=$(df -Ph | grep $I | awk '{print $2}')
        used=$(df -Ph | grep $I | awk '{print $3}')
        free=$(df -Ph | grep $I | awk '{print $4}')
        rate=$(df -Ph | grep $I | awk '{print $5}')
        mount=$(df -Ph | grep $I | awk '{print $6}')
        echo -e ">$I:\t$used/$size" >>/be6cpu.info
        echo "" >>/be6cpu.info
        echo ">*使用率*；**$rate**" >>/be6cpu.info
        echo "" >>/be6cpu.info
    done
    total_disk=$(fdisk -l | grep '^Disk' | sed 's/,.*//g')
    ina=$(awk '/pgpgin/{print $2}' /proc/vmstat)
    sleep 30s
    inb=$(awk '/pgpgin/{print $2}' /proc/vmstat)
    ioin=$(((inb - ina) / 30))
    outa=$(awk '/pgpgout/{print $2}' /proc/vmstat)
    sleep 30s
    outb=$(awk '/pgpgout/{print $2}' /proc/vmstat)
    ioout=$(((outb - outa) / 30))
    echo ">总硬盘: $total_disk" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">读取: **$ioin**" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo ">写入: **$ioout**" >>/be6cpu.info
    echo "" >>/be6cpu.info
    echo "***" >>/be6cpu.info
    desp=$(cat /be6cpu.info)
    name =$(sed -n 2p /be6server)
    port =$(sed -n 3p /be6server)
    curl -G --data-urlencode "text=服务器状态->${name}-${port}" --data-urlencode "desp=${desp}" https://sc.ftqq.com/$(sed -n 1p /be6server).send
    ```

