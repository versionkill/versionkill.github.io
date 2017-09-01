---
layout: post
title: Usage Of Redis
tags: tools
comments: true
---


```
./redis-server 啓動redis
redis-cli進入
keys * 查看所有keys
lrange （key） 0 -1
```
### 1.1. How to install redis
Redis Cluster, the distributed version of Redis, is making a lot of progresses and will be released as beta at the start of Q3 2013

### 1、Download and unpack
  ```
  wget http://download.redis.io/releases/redis-2.6.16.tar.gz
  tar xzf redis-2.6.16.tar.gz
  mv redis-2.6.16 /usr/local/redis
  ```
### 2、Install the package manually
  ```
  cd /usr/local/redis
  make
  yes|cp -p src/redis-check-aof  /usr/local/bin/  &amp;amp;&amp;amp;
  yes|cp -p src/redis-check-dump  /usr/local/bin/  &amp;amp;&amp;amp;
  yes|cp -p src/redis-cli  /usr/local/bin/  &amp;amp;&amp;amp;
  yes|cp -p src/redis-server /usr/local/bin/ &amp;amp;&amp;amp;
  yes|cp -p src/redis-benchmark /usr/local/bin/
  mkdir /etc/redis/
  cp -p redis.conf /etc/redis/
  ```
### 3、Install as a service

Create service file and link:

```
touch /etc/init.d/redisd
chmod +x /etc/init.d/redisd
```

Edit /etc/init.d/redisd with:

```
#!/bin/bash

Init file for redis

#

chkconfig: 2345 99 90

description: redis daemon

#

processname: redisd

config: /etc/redis.conf

pidfile: /var/run/redis.pid

Source function library.

if [ -f /etc/init.d/functions ] ; then
  . /etc/init.d/functions
elif [ -f /etc/rc.d/init.d/functions ] ; then
  . /etc/rc.d/init.d/functions
else
  exit 1
fi

Avoid using root's TMPDIR
unset TMPDIR

CONF="/etc/redis/redis.conf"
PIDFILE=/var/run/redis.pid
EXEC=/usr/local/bin/redis-server
CLIEXEC=/usr/local/bin/redis-cli
desc=${DESC-Redis Server}

Check CONF file exists.

if [ ! -f $CONF ]
then
  echo "$CONF does not exist!"
  exit 6
fi

RETVAL=0

start() {
  if [ -f $PIDFILE ]
  then
      echo "$PIDFILE exists, process is already running or crashed"
  else
      echo "Starting $desc..."
      $EXEC $CONF
      RETVAL=$? 
      [ $RETVAL = 0 ]&amp;amp;&amp;amp; echo "$desc started"
      return $RETVAL
  fi
}
stop() {
   if [ ! -f $PIDFILE ]
   then
        echo "$PIDFILE does not exist, process is not running"
   else
        PID=$(cat $PIDFILE)
        echo "Stopping $desc..."
        # $CLIEXEC -p $REDISPORT shutdown
        $CLIEXEC shutdown 
        while [ -x /proc/${PID} ]
        do
            echo "Waiting for $desc to shutdown ..."
            sleep 1
        done
        echo "$desc stopped"
   fi
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        start
        ;;
    status)
        #status -p ${pidfile} $EXEC
        status $EXEC
        RETVAL=$?
        ;;
    *)
        echo $"Usage: $0 {start|stop|restart|status}"
        RETVAL=1
        ;;
esac

exit $RETVAL
```

### 4. Start redis service Before start redis server, you should configure the following setting in /etc/redis/redis.conf

```
logfile /var/log/redis/redisd_error.log
dir /xxx/xxx/ 
vm-swap-file /xxx/redis.swap 
```

Note: The path specified must exist

Then start the service:

```
chkconfig --add redisd
chkconfig redisd on
/etc/init.d/redisd start
``` 

####5. Testing Us e the client utility to test it

```
redis-cli
redis 127.0.0.1:6379> INFO
……
``` 

For detail commands please refer to http://redis.io/commands
