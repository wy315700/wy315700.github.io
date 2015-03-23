---
layout: post
title:  "Linux里防止多个程序实例运行"
date:   2012-05-31 10:56:12
categories: Linux
---
有的时候，一个程序只能允许一个实例运行，比如一些守护进程，这个时候就需要加一个锁，一般来说都是在/var/run/目录下放一个pid文件，然后用fcntl来达到锁定的目的。

以下代码摘自hustoj

{% highlight C %}
int already_running(){
        int fd;
        char buf[16];
        fd = open(LOCKFILE, O_RDWR|O_CREAT, LOCKMODE);
        if (fd < 0){
                syslog(LOG_ERR|LOG_DAEMON, "can't open %s: %s", LOCKFILE, strerror(errno));
                exit(1);
        }
        if (lockfile(fd) < 0){
                if (errno == EACCES || errno == EAGAIN){
                        close(fd);
                        return 1;
                }
                syslog(LOG_ERR|LOG_DAEMON, "can't lock %s: %s", LOCKFILE, strerror(errno));
                exit(1);
        }
        ftruncate(fd, 0);
        sprintf(buf,"%d", getpid());
        write(fd,buf,strlen(buf)+1);
        return (0);
}
int lockfile(int fd)
{
        struct flock fl;
        fl.l_type = F_WRLCK;
        fl.l_start = 0;
        fl.l_whence = SEEK_SET;
        fl.l_len = 0;
        return (fcntl(fd,F_SETLK,&fl));
}
{% endhighlight %}
 