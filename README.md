# yjcXcode-
1.github 以后不支持 https的提交,改为ssh
2.【Step 1】 打开终端，输入 cd ~/.ssh，检查是否已经存在了SSH密钥。如果你看到类似id_rsa.pub的文件，说明你已经有了一对公钥和私钥，可以跳过第 2 步和第 3 步。

【Step 2】 在终端输入ssh-keygen -t rsa -C "你的邮箱地址" ，生成新的SSH密钥。你可以直接按回车键使用默认的文件路径和空密码，也可以自己设置。
3.去github 设置中 添加ssh  复制 id_rsa.pub 
4.可以提交内容了
