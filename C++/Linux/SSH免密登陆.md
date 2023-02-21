# SSH免密登陆

首先，本地Windows端，打开命令行并输入

![image-20221220115355310](https://raw.githubusercontent.com/rowenci/ImagePool/main/img/typora202212201154424.png)

三次回车，直接默认即可，生成的密钥在第三行有显示

其中id_rsa是私钥，id_rsa.pub是公钥

随后，在远端（Ubuntu）输入同样的命令，并生成密钥

![image-20221220115710062](https://raw.githubusercontent.com/rowenci/ImagePool/main/img/typora202212201157097.png)

完成后，进入.ssh文件夹，创建一个文件名为authorized_keys，并将本地的pub公钥当中的内容复制到该文件当中。

至此，ssh可直接免密，无需重启ssh服务。