由于目前博客还没建好，先白嫖gayhub。

关于我们OS环境在18.04及以上（我是19.10）的坑简单说几个解决办法。

主要是两个坑：perl语法的变化（x16当成控制字符）和make utils时提示一个库缺失了，还有bochs版本问题。

解决方案：使用作者维护的pintos-anon；http://pintos-os.org/cgi-bin/gitweb.cgi

这个版本解决了在现代编译器下的问题和perl语法问题，同时bochs版本改为2.6.2

由于git和sourceforge下载都很慢，建议翻墙或者使用国内VPS中转，FTP下载到本地。

这个版本的编译bochs，可以把bochs放在/src/misc下，通过修改和执行其中的bochs脚本（注意版本号请严格对应，因为存在某些patch连接，很容易翻车），自动make
修改方法是给路径赋值，类似这样

`
#! /bin/sh -e

SRCDIR=/home/enimga/Desktop/Enigma_OS/pintos-anon/src/misc
PINTOSDIR=/home/enimga/Desktop/Enigma_OS/pintos-anon
DSTDIR=/usr/local
if test -z "$SRCDIR" || test -z "$PINTOSDIR" || test -z "$DSTDIR"; then
    echo "usage: env SRCDIR=<srcdir> PINTOSDIR=<srcdir> DSTDIR=<dstdir> sh $0"
    echo "  where <srcdir> contains bochs-2.6.2.tar.gz"
    echo "    and <pintosdir> is the root of the pintos source tree"
    echo "    and <dstdir> is the installation prefix (e.g. /usr/local)"
    exit 1
fi
`

然后bash里面./bochs-2.6.2-build.sh
  
 
随后在/src/utils下make时会遇到编译器提示在squish-pty和squish-unix中缺失<stropts.h>

这个库不是给Linux用的，所以删掉没关系，只需要注释掉，同时把squish-pty.c中286行注释写着System的if语句注释即可

`
/* System V implementations need STREAMS configuration for the
     slave. */
  //if (isastream (slave))
    //{
      //if (ioctl (slave, I_PUSH, "ptem") < 0
          //|| ioctl (slave, I_PUSH, "ldterm") < 0)
        //fail_io ("ioctl");
    //}
`

顺便说一下环境变量不需要重复修改，修改的话也只需要source即可，注销没必要
