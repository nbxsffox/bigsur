作者：IMnot
链接：https://www.zhihu.com/question/403361335/answer/1583471375
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



解决思路是: 将系统重新挂载在到一个有读写权限的新目录下，在该目录下操作即可。

补充具体方法：

1. 开机按住Commond+R，进入系统Recovery模式，从terminal中将系统保护SIP关掉（csrutil authenticated-root disable；csrutil disable）；
2. 重启开机进入系统，可以在终端查看是否已将SIP关掉（csrutil status；[csrutil authenticated-root status](https://www.zhihu.com/search?q=csrutil+authenticated-root+status&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1583471375})），都显示Disable即可；
3. 通过 [sudo mount -o nobrowse -t apfs](https://www.zhihu.com/search?q=sudo+mount+-o+nobrowse+-t+apfs&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1583471375}) /dev/diskNsM /xxx/xx 重新将系统盘挂载到本地。/xxx/xx 是可以在任意地方新建的目录（例如：/Users/mac/Desktop/aaa）。diskNsM 为系统盘的设备名，这个在"磁盘工具-设备" 里可以看到（例如：disk1s5s1），注意要把后面的s1去掉，因为磁盘工具里面里显示的是系统盘的快照，挂载成这个snapshot是没用的！！ 
4. 如果是上述例子的情况的话，就执行 sudo mount -o nobrowse -t apfs /dev/disk1s5 /Users/mac/Desktop/aaa，这样[系统盘](https://www.zhihu.com/search?q=系统盘&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1583471375})就重新挂载到了桌面的[aaa文件夹](https://www.zhihu.com/search?q=aaa文件夹&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1583471375})下面，从这个文件夹进去就是系统根目录了。而且这个文件夹是可读可写的，我们把要修改的系统配置文件夹/文件 复制到相应的系统目录里就OK。
5. 最后需要重建系统 snapshot，使用到的命令为：sudo bless --folder /xxx/xx/System/Library/CoreServices --bootefi --create-snapshot，其中/xxx/xx是最初设置的目录；如果是上述例子的情况的话，就执行：sudo bless --folder /Users/mac/Desktop/aaa/System/Library/CoreServices --bootefi --create-snapshot
6. 重启系统，会发现挂载的磁盘消失，而修改的设置已经写入到原先不能更改的系统文件中了。Over……

过程中省略了一些简单的步骤，可自行baidu/ google。解决问题后，不要忘记再次重启进入Recovery模式，打开terminal，输入：

```text
csrutil enable
csrutil authenticated-root enable
```

重新开启SIP以免系统失去安全防护。(ps: 有反馈说重新开启SIP会遇到问题，我是没有重新打开系统防护的，所以目前一切正常……大佬们视自己情况决定enable or disable！

对了，如何评价苹果这次更加闭环的更改: 道高一尺，魔高一丈hh。