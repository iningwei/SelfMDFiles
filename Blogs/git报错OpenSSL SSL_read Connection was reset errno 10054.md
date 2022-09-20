git pull时报错：fatal: unable to access 'xxxxxx': OpenSSL SSL_read: Connection was reset, errno 10054

解决方法：
打开git bash，输入命令：``git config --global http.sslVerify "false"``即可解除ssl验证。