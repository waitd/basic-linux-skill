# Reids 安装及配置

## Redis 安装

- Redis 安装方式

  - 下载: `wget http://download.redis.io/releases/redis-3.0.7.tar.gz`
  - 解压: `tar zxvf redis-3.0.7.tar.gz`
  - 进入解压目录: `cd  redis-3.0.7.tar.gz`
  - 编译并安装：`make & make install
  - 启动:`src/redis-server redis.conf`
  - 开机启动配置:`echo "/usr/local/redis/src/redis-server /usr/local/redis/redis.conf" `
  - 开启防火墙端口:
    - 添加规则:` sudo iptables -I INPUT -p tcp -m tcp —dport 6379 -j ACCEPT`
    - 保存规则:`sudo /etc/rc.d/init.d/iptables save `
    - 重启防火墙:`sudo /etc/init.d/iptables restart`

- Redis常用配置

  - 开机启动:把旧值`daemonize no`改为`daemonize yes`
  - 绑定IP:`bind 127.0.0.1`改为需要的IP（一般是内网IP）

- Redis重启脚本

  ```\# Source function library.

  ```

  . /etc/rc.d/init.d/functions

  \# Source networking configuration.
  . /etc/sysconfig/network

  \# Check that networking is up.
  [ "$NETWORKING" = "no" ] && exit 0
  redis="/usr/local/webserver/redis-2.4.4/bin/redis-server"
  prog=$(basename $redis)
  REDIS_CONF_FILE="/usr/local/webserver/redis-2.4.4/etc/redis.conf"
  [ -f /etc/sysconfig/redis ] && . /etc/sysconfig/redis
  lockfile=/var/lock/subsys/redis

  start() {
      [ -x $redis ] || exit 5
      [ -f $REDIS_CONF_FILE ] || exit 6
      echo -n $"Starting $prog: "
      daemon $redis $REDIS_CONF_FILE
      retval=$?
      echo
      [ $retval -eq 0 ] && touch $lockfile
      return $retval
  }

  stop() {
      echo -n $"Stopping $prog: "
      killproc $prog -QUIT
      retval=$?
      echo
      [ $retval -eq 0 ] && rm -f $lockfile
      return $retval
  }

  restart() {
      stop
      start
  }

  reload() {
      echo -n $"Reloading $prog: "
      killproc $redis -HUP
      RETVAL=$?
      echo
  }

  force_reload() {
      restart
  }

  rh_status() {
      status $prog
  }

  rh_status_q() {
      rh_status >/dev/null 2>&1
  }

  case "$1" in
      start)
          rh_status_q && exit 0
          $1
          ;;

      stop)
          rh_status_q || exit 0
          $1
          ;;

      restart|configtest)
          $1
          ;;

      reload)
          rh_status_q || exit 7
          $1
          ;;

      force-reload)
          force_reload
          ;;

      status)
          rh_status
          ;;

      condrestart|try-restart)
          rh_status_q || exit 0
      ;;

      *)
          echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload}"
          exit 2
  esac

  ```

  ```

  ​