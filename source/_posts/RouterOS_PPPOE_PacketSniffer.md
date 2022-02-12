---
title: RouterOS抓包恢复PPPOE密码
date: 2018-05-01 14:10:44
tags: life
---
RouterOS PPPOE(Session) Password Packet Sniffer 
---
# 背景
* 老家的破旧路由器跟不上现在的需求,想升级成更好的,但是宽带密码忘了,管理员密码也不记得了,如何恢复**PPPOE**密码?
<!--more-->
# 准备条件
* 安装了虚拟机**VMware**的电脑并导入[routeros6.44.2](https://github.com/codewindy/soft-common/blob/master/MikroTik-RouterOS-6.44.2_crackzsoft.com.ova)镜像或淘宝**rb750gr3**等
* 网线一根
* [VMWARE 虚拟机](https://rsload.net/soft/10943-vmware-workstation.html) 下载
* [wireshark](https://2.na.dl.wireshark.org/win64/Wireshark-win64-3.0.1.exe) 下载
*  [winbox ](https://mikrotik.com/download#) 客户端

# 过程
* 考虑到我买的rb750gr3不具有通用性及经济实惠性,我后面都采用虚拟机来实现
  ![2019-05-02_153951_000.png](https://i.loli.net/2019/05/02/5ccaa3bb59313.png)

  ![2019-05-02_150656_001.png](https://i.loli.net/2019/05/02/5ccaa3bc747cc.png)

  ![2019-05-02_151738_002.png](https://i.loli.net/2019/05/02/5ccaa3be60e45.png)

  ![2019-05-02_152312_003.png](https://i.loli.net/2019/05/02/5ccaa3bd57277.png)
* 先将家里老旧路由器的  <font style="color:black; background:yellow"> WAN口 </font>  插上网线连接到电脑的 **RJ45网口** 
* 打开虚拟机**vmware** 导入 [routeros 镜像](https://github.com/codewindy/soft-common/blob/master/MikroTik-RouterOS-6.44.2_crackzsoft.com.ova), 桥接到**`物理网卡`**
* 下载并打开**winbox**客户端,并通过**winbox** 来登录ros,默认帐号是 **admin** 密码是 **空**

  ![2019-05-02_152916_004.png](https://i.loli.net/2019/05/02/5ccaa3be54129.png)

  ![2019-05-02_153222_005.png](https://i.loli.net/2019/05/02/5ccaa3becc873.png)
  
  ![2019-05-02_153852_007.png](https://i.loli.net/2019/05/02/5ccaa3ba5e19d.png)

```shell
[admin@fsm] > ip export 
# may/02/2019 15:45:48 by RouterOS 6.44.2
# software id = ZJ3M-ESHW
#
/ip pool
add name=pool1 ranges=10.16.0.2-10.16.0.254
/ip address
add address=192.168.199.22/24 comment=defconf interface=ether1 network=192.168.199.0
add address=192.168.199.122/24 interface=ether1 network=192.168.199.0
/ip cloud
set update-time=no
/ip dhcp-client
add dhcp-options=hostname,clientid disabled=no interface=ether1
/ip dns
set allow-remote-requests=yes servers=192.168.199.1
/ip firewall nat
add action=masquerade chain=srcnat out-interface-list=WAN
/ip route
add disabled=yes distance=1 gateway=192.168.199.1
[admin@fsm] > ppp export 
# may/02/2019 15:45:52 by RouterOS 6.44.2
# software id = ZJ3M-ESHW

/ppp profile
add dns-server=223.5.5.5 local-address=10.16.0.1 name=8M remote-address=pool1
```

# 原理
* **chap ** 认证过程是md5加密的,但是**pap **是明文认证方式,所以我们通过ros伪造**pppoe**服务器来抓包获取
* [PPP-PAP认证原理](http://blog.sina.com.cn/s/blog_5384e78b0100fhno.html)
* [PPP-CHAP认证原理](http://blog.sina.com.cn/s/blog_5384e78b0100fhnr.html)
  >PPP提供了两种可选的身份认证方法：口令验证协议PAP（Password Authentication Protocol，PAP）和质询握手协议（Challenge Handshake Authentication Protocol，CHAP）。如果双方协商达成一致，也可以不使用任何身份认证方法。　　CHAP认证比PAP认证更安全，因为CHAP不在线路上发送明文密码，而是发送经过摘要算法加工过的随机序列，也 被称为"挑战字符串".如图所示。同时，身份认证可以随时进行，包括在双方正常通信过程中。因此，非法用户就算截获并成功破解了一次密码，此密码也将在 一段时间内失效
![chap认证过程](https://i.loli.net/2019/05/02/5cca8c8d6a894.jpg)) 


* **使用Java代码来创建和解析出账号密码**
 ![PPPoE processon](https://i.loli.net/2020/04/05/tlpzI5nU2fwXQbu.png)
* [项目地址](https://github.com/codewindy/JavaTutorials) `https://github.com/codewindy/JavaTutorials`


```java
package com.codewindy.mongodb.service.impl;

import cn.hutool.core.date.DateUtil;
import cn.hutool.core.io.FileUtil;
import cn.hutool.core.util.CharsetUtil;
import cn.hutool.core.util.StrUtil;
import cn.hutool.extra.ssh.JschUtil;
import cn.hutool.extra.ssh.Sftp;
import com.codewindy.mongodb.pojo.ApiResult;
import com.codewindy.mongodb.pojo.PppoeDetail;
import com.codewindy.mongodb.service.MikrotikService;
import com.google.common.collect.Lists;
import lombok.extern.slf4j.Slf4j;
import me.legrange.mikrotik.ApiConnection;
import me.legrange.mikrotik.MikrotikApiException;
import org.springframework.stereotype.Service;
import org.springframework.util.CollectionUtils;

import java.io.File;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.stream.Collectors;

/**
 * @author codewindy
 * @desc <a href="https://wiki.mikrotik.com/wiki/Manual:RouterOS_features">RouterOS_features</>
 *       RouterOS is MikroTik's stand-alone operating system based on linux v3.3.5 kernel.
 * @date 2020-03-25 9:44 PM
 * @since 1.0.0
 */
@Service
@Slf4j
public class MikrotikServiceImpl implements MikrotikService {
    /**
     * @param username
     * @param password
     * @return
     */
    @Override
    public ApiResult login(String username, String password) {
        ApiConnection con = null;
        // connect to router
        try {
            con = initApiConnection(username, password);
            // log in to router
            List<Map<String, String>> execute = con.execute("/ip/address/print");
            // execute a command
            con.close();
            // disconnect from router
            return new ApiResult(execute);

        } catch (MikrotikApiException e) {
            log.info("登录RouterOS失败 = {}", e.getMessage());
            return new ApiResult(e.getMessage());
        }
    }

    /*
    /ip pool add name=pppoe_pool1 ranges=10.10.10.2-10.10.10.254
    /ppp profile add name=pppoe_profile local-address=10.10.10.1 remote-address=pppoe_pool1
    /interface pppoe-server server add authentication=pap service-name=PPPoE_Server interface=wan one-session-per-host=yes default-profile=pppoe_profile
    Mikrotik API 调用和直接winbox的不同，需要添加/ 转义
       */
    @Override
    public ApiResult createPPPOEServer(String ipPoolRange) {
        ApiConnection con = null;
        // connect to router
        try {
            con = initApiConnection("admin", "");
            // log in to router
            con.execute("/ip/pool/print");
            con.execute("/ip/pool/add name=pool1 ranges=10.10.10.2-10.10.20.1");
            con.execute("/ppp/profile/add name=pppoe_10M remote-address=pool1");
            con.execute("/ppp/secret/add name=0327 password=0327Test service=pppoe profile=pppoe_10M");
            con.execute("/interface/pppoe-server/server/add authentication=pap service-name=PPPoE_Server interface=ether1 one-session-per-host=yes default-profile=pppoe_10M");
            con.execute("/interface/pppoe-server/server/enable numbers=0");
            //开始执行抓包pppoe-session file-limit=10KiB  控制文件大小 内存好像没起作用
            String command4capFileName = "/tool/sniffer/set file-name=%s.cap filter-mac-protocol=pppoe file-limit=10KiB memory-limit=10KiB";
            con.execute(String.format(command4capFileName, DateUtil.today()));
            log.info("开始执行抓包pppoe-session==={}", String.format(command4capFileName, DateUtil.today()));
            con.execute("/tool/sniffer/start mac-protocol=pppoe interface=ether1 direction=rx");
            List<Map<String, String>> resultMapList = con.execute("/file/print");
            for (Map<String, String> fileMap : resultMapList) {
                if ((fileMap.get("type").contains(".cap") || fileMap.get("type").contains(".pcap"))&& Integer.parseInt(fileMap.get("size"))>1000){
                    //创建pppoe服务器抓包时候控制文件size 一般大于2000B 就无法执行file print detail，即不能在terminal上解析报文了
                    con.execute("/tool/sniffer/stop");
                }
            }
            return new ApiResult("初始化pppoe服务器成功!");
        } catch (MikrotikApiException e) {
            log.info("登录RouterOS失败 = {}", e.getMessage());
            return new ApiResult(e.getMessage());
        }
    }

    private ApiConnection initApiConnection(String username, String password) throws MikrotikApiException {
        ApiConnection con;
        con = ApiConnection.connect("192.168.2.2");
        con.setTimeout(5000);
        // set command timeout to 5 seconds
        con.login(username, password);
        return con;
    }

    /**
     * 解析后的json中包含了\u0000 空格
     *  {
     *         "account": "test4pppoe",
     *         "password": "123456Test\u0000\u0000\u0000\u0000\u0000\u0000"
     *     }
     * @return
     */
    @Override
    public ApiResult getPcapFileDetail() {
        ApiConnection con = null;
        // connect to router
        try {
            con = initApiConnection("admin", "");
            // log in to router
            List<Map<String, String>> resultMapList = con.execute("/file/print");
            resultMapList.stream().filter(s -> s.get("type").contains(".cap") || s.get("type").contains(".pcap")).forEach(s -> System.out.println("pcap报文的size="+s.get("size")));
            //获取每个cap数据包里面的content
            List<String> contentList = resultMapList.stream().filter(s -> s.get("type").contains(".cap") || s.get("type").contains(".pcap")).map(s -> s.get("contents")).collect(Collectors.toList());
            //获取每个content里面的pppoe 账号密码
            Set<String> pppoeAccountSet = contentList.stream().filter(content -> content.contains("user")&& content.contains("authentication")).map(content -> StrUtil.subBetween(content, "user", "authentication").trim()).collect(Collectors.toSet());
            List<PppoeDetail> pppoeDetailList = Lists.newArrayListWithCapacity(pppoeAccountSet.size());
            for (String pppoeAccount : pppoeAccountSet) {
                List<String> passwordlist = contentList.stream().filter(content->content.contains(pppoeAccount)).map(content -> StrUtil.subBetween(content, pppoeAccount, "\u0000").trim()).distinct().collect(Collectors.toList());
                PppoeDetail pppoeDetail = new PppoeDetail();
                pppoeDetail.setAccount(pppoeAccount);
                if (!CollectionUtils.isEmpty(passwordlist)) {
                    //直接截取字符串 去除了二进制流中的乱码 \u0000空格 �LV
                    pppoeDetail.setPassword(StrUtil.subPre(passwordlist.get(0),16));
                }
                pppoeDetailList.add(pppoeDetail);
            }
            // execute a command
            con.close();
            // disconnect from router
            return new ApiResult(pppoeDetailList);
        } catch (MikrotikApiException e) {
            log.info("获取PPPOESession详情失败 = {}", e.getMessage());
            return new ApiResult(e.getMessage());
        }
    }

    @Override
    public ApiResult downloadPPPOESession() {
        //下载Packet Sniffer下载创建的pcap文件
        //TODO
        //1. 不能直接抓去pppoe-session的数据
        //2. pppoe 服务器的ip 地址动态传入
        //3. 切换网络会出现winbox退出
        //4. file print detail 不能查看2kb之外大文件，无法解析出contents内容
        //5. 登录接口只调用一次，使用redis缓存账号密码
        //6. 统一修改接口返回参数 T data
        //7. 使用vim 或cat 来解析cap或pcap抓包文件
        //ApiConnection con = null;
        // connect to router
        //C:\WBYF_IDEA
        try {
           // con = initApiConnection("admin", "");
            // log in to router
            //List<Map<String, String>> resultMapList = con.execute("/file/print");
            Sftp sftp= JschUtil.createSftp("192.168.2.2", 22, "admin", "");
            //进入远程目录
            sftp.cd("/");
            log.info("获取sftp文件当前路径",sftp.pwd());
            log.info("显示当前目录下的文件",sftp.ls("/"));
            List<String> fileList = sftp.ls("/");
            //下载远程文件
            fileList.stream().filter(s -> s.contains(".cap") || s.contains(".pcap")).forEach(capFile -> sftp.get(capFile, "C:\\WBYF_IDEA\\"));
            //上传本地文件
            //sftp.put("e:/test.jpg", "/opt/upload");
            //sftp.get("0325.cap", "C:\\WBYF_IDEA\\");
            //关闭连接
           // List<String> strings = FileUtil.readUtf8Lines("C:\\WBYF_IDEA\\0325.cap");

            sftp.close();
            return new ApiResult("下载pcap数据文件成功");
        } catch (Exception e) {
            log.info("下载pcap抓包文件失败 = {}", e.getMessage());
            return new ApiResult(e.getMessage());
        }
    }

    @Override
    public ApiResult parseLocalPcapFile() {
        //String localPwd = System.getProperty("user.dir");

        String path ="C:\\WBYF_IDEA\\JavaTutorials\\mongodb\\src\\main\\resources\\pcap";
        List<String> fileList = FileUtil.listFileNames(path);
        List<String> parsedStringList = Lists.newArrayListWithCapacity(fileList.size());
        for (String pcapFileName : fileList) {
            if (pcapFileName.contains(".cap")||pcapFileName.contains(".pcap")) {
                System.out.println("pcapFileName = " + path+File.separator+pcapFileName);
                String parsedString = FileUtil.readString(path+File.separator+pcapFileName, CharsetUtil.UTF_8);
                parsedStringList.add(parsedString);
            }
        }

        //System.out.println("path = " + path);
        //已经获取到解析好的pcap二进制流数据转String了，接下来分批去重提取账号密码
        //1， winbox公用api端口会被挤下线
        //2. mac地址修改成和服务器一样的
        return  new ApiResult(parsedStringList);
    }
}
```

# 结论
* 使用wireshark 打开抓到的cap 报文即可.
  ![2019-05-02_153339_006.png](https://i.loli.net/2019/05/02/5ccaa3bdac0fa.png)

# 参考
* [抓包获取PPPOE账号密码](https://blog.ich8.com/post/3699)
* [pppoe源码分析](https://blog.csdn.net/vivianliulu/article/details/77449780)
* [mikrotik routeros 6.44.2 LEVEL 6 FOR VMWARE + CRACK](https://crackzsoft.com/mikrotik-routeros/)
* [mikrotik demo](http://demo2.mt.lv/webfig/) MikroTik RouterOS online demo
* [MikroTik手册](https://wiki.edcwifi.com/index.php?title=MikroTik%E6%89%8B%E5%86%8C)
* [EDCwifi.com](https://download.edcwifi.com/index.php?title=MikroTik%E6%89%8B%E5%86%8C)
* [pppoe自动到期通知](http://www.rosjb.com)
* [routeros入门到精通v637e.pdf](https://gitee.com/jkwindy/pic4blog2/blob/master/RouterOS_RECOVER_PPPOE_PASSWORD/routeros%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9Av637e.pdf)
* [Packet_Sniffer](https://wiki.mikrotik.com/wiki/Manual:Tools/Packet_Sniffer) 
* [PPPOE Protocal diagram](https://wiki.mikrotik.com/wiki/Manual:Interface/PPPoE)
* [ros大玩家](https://space.bilibili.com/434853043spm_id_from=333.788.b_765f7570696e666f.2) 视频教程 `QQ：1247004718（微信同号）`
* [RouterOS  edcwifi](https://www.edcwifi.com.cn/wiki/13.pdf)
* [rfc2516.pdf](https://tools.ietf.org/pdf/rfc2516.pdf) `datatracker.ietf.org/doc/rfc2516`
* [GideonLeGrange-mikrotik-java](https://github.com/GideonLeGrange/mikrotik-java)  Mikrotik Java API Client
* [PPPoE服务器编写](https://stefenson.github.io/tags/PPPoE/)
* [https://www.ros9.com/index.php]([http://www.roslm.com:82/index.php?dir=/002.%E8%B7%AF%E7%94%B1%E4%B8%93%E5%8C%BA](http://www.roslm.com:82/index.php?dir=/002.路由专区))
* [HunterTik2.3.1.iso](https://docs.google.com/file/d/0ByWLVUEElhsmLXhBeEt3MHk1U2s/edit)
* [X86 3865U安装ROS+LEDE双软路由(重要插件)](https://www.huakings.cn/post/216.html)
* [mikrotik-routerosv7-first-look-vxlan](https://stubarea51.net/2020/02/15/mikrotik-routerosv7-first-look-vxlan/)
* [Mikrotik jira docs](https://help.mikrotik.com/docs/display/ROS/RouterOS)
* [MikroTik 5.25](https://mirrors.dtops.cc/iso/ros5.25_1G/)
* [RouterOS wifi](https://www.edcwifi.com.cn/project/afc_api/Public/Uploads/2019-10-17/5da816a82f565.pdf)
* [mikrotik archive](https://mikrotik.com/download/archive)
* [RouterOS kernel 2.6.35-3.3.5](https://kkslinuxinfo.wordpress.com/category/mikrotik-routeros/)
* [国外教程网站 https://rickfreyconsulting.com/packet-sniffer/](https://rickfreyconsulting.com/packet-sniffer/)
* [PPPOE拨号上网流程及密码窃取具体实现](https://www.cnblogs.com/sky-star/p/pppoe.html)
