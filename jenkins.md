# 1 服务器免登录

* 在jenkins 客户端使用命令生成公钥密钥

  ```bash
  ssh-keygen -t rsa
  ```

* 将公钥复制到服务器的用户

  ```bash
  ssh-copy-i id_rsa.pub user@xxx.xxx.xxx
  ```

* 检查用户文件下的/user/.ssh/authorized_keys

* 配置ssh_config

  https://blog.csdn.net/m0_37268841/article/details/86592949



# 2 通过shell命令执行部署

* 复制到指定指定文件夹

  * windows:

    ``` 
    scp targer/*.jar user@xxx.xxx.xxx:D:/kevin-test
    ```

  * linux:

    ```
    scp targer/*.jar user@xxx.xxx.xxx:/home/kevin-test
    ```

* 使用ssh命令执行脚本

  * windows：

    ```bash
    ssh user@xxx.xxx.xxx "d: && d:/kevin-test/start.bat"
    ```

  * linux:

    ```bash
    ssh user@xxx.xxx.xxx "./home/kevin/start.sh"
    ```

  注意：docker： buildKit: false

  















