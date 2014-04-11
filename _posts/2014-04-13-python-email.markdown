---
title: Python：邮件定时发
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d90
tags:
  - python
  - 人生苦短
---

### 缘起

人生苦短，我用Python。

作为数据分析师，各种不给数据库权限，就给阿里云划定了一小片空间，差点连运行Python的权限都没给我。哦顺便一说，pip的权限确实没给我。

领导要做报表，每个月/每周都会有几天特别内什么，然后我对于MySQL+Python计算+...+邮件任务实在烦不过，整理了网上的资料如下。

### 数据提取并执行邮件脚本

以下为automail_with_attachment_from_mysql.sh内容：

    #!/bin/sh
    #DB连接定义
    DB_HOST="..." #主机名
    DB_NAME="..." #数据库名
    DB_USER="..." #用户名
    DB_PASS="..." #密码
    #临时环境变量定义
    BIN_DIR="/usr/bin" #Linux都一样，可执行的路径，最好自己确认下
    PY_DIR="..." #python脚本与放置mysql查询输出txt的文件夹
    #执行MySQL查询，整理成CSV，并执行Python脚本
    $BIN_DIR/mysql -h $DB_HOST -u $DB_USER --password=$DB_PASS --database=$DB_NAME --execute="SET names utf8;你的查询语句写这里;" | sed 's/^//;s/[\t]/,/g;s/$//;s/\n//g' > $PY_DIR/weekly_amap.csv
    $BIN_DIR/python $PY_DIR/automail.py

### 自动发送邮件主体

以下为automail.py内容：

    # -*- coding: utf-8  -*-
    import smtplib
    from email.mime.text import MIMEText
    from email.MIMEMultipart import MIMEMultipart
    import datetime
    import time
    import codecs
    
    
    TODAY = datetime.date.today()
    CURRENTDAY=TODAY.strftime('%Y%m%d')
    def sendattachmail ():
        msg = MIMEMultipart()
        
        att = MIMEText(codecs.open(r'这里写附件1的绝对路径', 'rb','utf-8').read().encode("gb2312",'ignore'), 'base64', 'gb2312') #增加附件，用gb2312编码增加附件的小白友好度
        att['content-type'] = 'application/octet-stream'
        att['content-disposition'] = 'attachment;filename="这里写附件1文件名"'
        msg.attach(att)
    
        att = MIMEText(codecs.open(r'这里写附件2的绝对路径', 'rb','utf-8').read().encode("gb2312",'ignore'), 'base64', 'gb2312')
        att['content-type'] = 'application/octet-stream'
        att['content-disposition'] = 'attachment;filename="这里写附件2文件名"'
        msg.attach(att) #再粘贴一个文件
    
        content = (u'这是一封自动发送的邮件，附件内容为：xxx。请查收。').encode("gb2312") #用gb2312编码增加附件的小白友好度
        body = MIMEText(content,'plain','GBK')
        msg.attach(body)
    
        msgto = ['目标邮件地址1','目标邮件地址2'] # 逗号分隔一个邮件地址列表
        msgfrom ='<我的邮件地址>' # 发送人属性
        msg['From'] = '我的名字<我的邮件地址>' # 这个属性必须填
        msg['subject'] = '周报：发送于'+CURRENTDAY  # 标题
        msg['date']=time.ctime() # 时间
        msg['Cc']='抄送地址1' # 不支持多重抄送
    
    
        mailuser = '我用于登陆的邮件地址用户名'
        mailpwd = '密码'
    
        try:
            smtp = smtplib.SMTP()
            smtp.connect(r'smtp.exmail.qq.com',25)# 这里我使用腾讯企业邮箱smtp
            smtp.login(mailuser, mailpwd) # 登录
            smtp.sendmail(msgfrom, msgto, msg.as_string()) # OK，发送一下
            smtp.close()
        except Exception, e:
            print e
    
    
    if __name__ == '__main__':
        sendattachmail()
        
### 自动发送

在命令行下使用

     crontab -e

仿佛进入了vim界面，输入

     0 7 * * 5 sh /你sh脚本的路径/automail_with_attachment_from_mysql.sh
     
这表示，每逢周五的早晨7点0分执行这个脚本。完成后按ESC，再按:wq保存。前五个数字参数表示分 时 日 月 星期几，后面的是执行脚本。

这东西有啥用呢，打个比方，如果你很喜欢一个妹子，而且你有一个自动作诗机能够源源不断的产生情诗，你可以先把它放在MySQL里面，然后使用这些脚本，最后在crontab -e里面输入：

     30 9 * * * sh /你sh脚本的路径/automail_with_attachment_from_mysql.sh
     
这表示你的女神每天早上9：30一睁眼就看见你那文字不通的诗句了，会有一天十分感动，然后拉黑你。