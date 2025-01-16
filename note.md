https://github.com/yifengyou/jz2440/blob/master/docs/Linux%E9%A9%B1%E5%8A%A8/%E5%AD%97%E7%AC%A6%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F%E4%B9%8B%E4%B8%AD%E6%96%AD%E6%96%B9%E5%BC%8F%E7%9A%84%E6%8C%89%E9%94%AE%E9%A9%B1%E5%8A%A8_%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81.md

ioctl函数 
poll()函数 gpio监听


#include <stdio.h>
#include <fcntl.h>
#include <poll.h>
#include <unistd.h>

int main()
{
    int fd = open("/sys/class/gpio/gpio7/value", O_RDONLY);
    if (fd < 0)
    {
        perror("open '/sys/class/gpio/gpio7/value' failed!\n");
        return -1;
    }
    struct pollfd fds[1];
    fds[0].fd = fd;
    fds[0].events = POLLPRI;

    while (1)
    {
        if (poll(fds, 1, 0) == -1)
        {
            perror("poll failed!\n");
            return -1;
        }

        if (fds[0].revents & POLLPRI)
        {
            if (lseek(fd, 0, SEEK_SET) == -1)
            {
                perror("lseek failed!\n");
                return -1;
            }

            char buffer[16];
            int len;

            if ((len = read(fd, buffer, sizeof(buffer))) == -1)
            {
                perror("read failed!\n");
                return -1;
            }
            
            buffer[len] = 0;
            printf("%s", buffer);
        }
    }
    return 0;
}
