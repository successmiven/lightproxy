# anyproxy
vps动态拨号、搭建vps代理池、扫描收集万维网上开放的代理端口
### 扫描代理服务器

### 文件说明：
    db.py->Redis数据库文件，进行各种数据库操作
    config.py->配置文件，配置Redis数据库的host，port等其他信息
    sender.py->拨号脚本文件，实现定时拨号和更新远程Redis数据库中的代理信息
    api.py->接口文件，对外提供获取代理信息的访问接口
### 部署方法：
    1.sender.py + db.py + config.py 部署到VPS主机端，注意将config.py中Redis数据库host、port等信息与实际一致。

    2.Redis数据库可以部署在某台固定IP的VPS，也可以购买ASP提供的Redis数据库服务，如华为云、阿里云、腾讯云等。
      注意VPS主机不能部署到1中的动态拨号VPS主机里，因为拨号主机的出口ip是拨号产生的，如果把Redis数据库部署到该主机上，
      则会出现数据库无法访问现象，因为拨号主机一直运行着拨号脚本，其ip一直在变化。另外，当把Redis数据库部署到了某商的
      云服务器后要记得在云控制台改变安全访问控制策略，即添加一个入方向规则，允许Redis数据库的6379端口接收外部访问。

    3.api.py部署到服务器或测试主机上，即可通过Web接口获取可用代理。注意初始化Redis的数据库信息与实际一致。即：
        if __name__ == '__main__':
            #将实际的Redis数据库信息填写到参数列表中
            redis = RedisClient(host='',port'',password='',proxykey='')
            server(redis=redis)

### 在VPS主机端部署代理服务器的作用：
    1.测试拨号生成的代理ip是否可用；
    2.应用端通过api模块获取到代理后，进行数据的转发传输,即使用该代理进行爬虫访问。

### 测试方法：
    1.将VPS主机设置为代理服务器；
    2.在华为云服务器上部署Redis数据库,并更改安全策略，允许开放6379端口接收访问；
    3.在VPS主机端运行拨号脚本,20s拨一次号;
    4.PC端运行api脚本来获取代理。
    

扫端口我们可以用 nmap 这个工具。nmap 是一个网络扫描的工具，它可以用来扫描对方服务器启用了哪些端口、哪些服务，服务器是否在线，以及猜测服务器可能运行的操作系统。
运行这个程序，当我们通过代理访问这个web服务，它就会返回代理请求头的信息，我们可以据此判断代理是透明、匿名还是高匿代理。

### 维护代理池

好，有了代理和代理的类型，我们可以将他们做成一个代理池，提供一个接口给客户，让他们通过接口来获取可用的代理。

当然这些扫出来的代理有效时间长短不一，有的代理也许可以用很久，有的代理可能一会儿时间就失效了。我们需要保证代理池中的代理是有效的，可以定期的去检查代理的有效性，把失效的从列表中去除，把新的有效的加入进来。
