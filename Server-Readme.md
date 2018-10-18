# Server 项目重构 


* 以开源为目的
* 功能层次清晰
* 无代码的冗余
* 有配套测试程序，日志清晰
* 除外部第三方库外，无其他附件库

## 开源标准+开发规则+Todo List

### Git相关

* 开发者账号
* Git库
* Git服务器 + Git LFS支持
* Github对接

### 开发环境

* 开发工具
  * Windows : Visual Studio 2017 Community Edition
    - 语言设置调整为英文 / Tools-> options -> Environment -> Internaltional Settings = English 
    - 缩进需要调整为4个空格
  
## 项目信息

* 项目代号: serverX 
* 主工程用各自的产品命名 

### 目录结构

        + 产品目录名 // dms or other product solution folder, includ all dependencies library and main app
  
          + serverX // public serverX library
            + src

          + dms // 具体的产品实现代码
  
          + dependencies
            + include
            + lib
            + projects
              + libfribidi
              + libiconv

          + 产品UI
          + 产品集成依赖工程
          + 其他

        目前以windows为例, 其他平台待参考


### 多产品支持原则

* 在serverX的主代码中, 不能出现和具体产品相关的代码、URL、字符串、功能
* 产品相关的代码，通过在serverX中实现通用的虚函数，在具体产品的cpp中实现相应的功能

        + serverX

                function.h

                        class function
                        {
                            virtual int doSomething() = 0;
                        }

        + serverX / products / dms /

        dms_function.h
        dms_function.cpp

                class dms_function : public function
                {
                    virtual int doSomething()
                    {
                        //do something
                    }
                }



* 在serverX主代码中，只有功能宏，不能有产品宏，产品宏在相应的产品配置工程中实现

* 配置过程在产品封装层完成，URL、字符串等都通过产品代码来传入到serverX中

        对照目前的日志来进行分析，修正这部分代码

* serverX是中性代码，编译成静态库，各类产品可以自行封装成动态库或者应用程序

        dms 
        - windows:
         - dms.exe  
           - serverx.lib
         
        - android
         - TV apk 
          - dms.so
           - serverx.lib  

### 构建规则

* Windows
  
        - 独立的vcproj / sln，对不同的产品
        - 独立的目录, 来保存runtime等环境

        - 创建新的产品vcproj/sln，只需要完整复制一份，然后把产品名全部修改下即可

### 标识、编码规则

    - 不要出现DVDFab, Vidon等字样，如有需要以项目代号(SERVERX)来替代
  
    - 外部的安装程序, 路径, 环境变量等在程序中都需要以变量的形式出现, 具体名称依赖外部配置

### 编译宏说明

* Common

        _LIB

        _DEBUG
        NDEBUG

        SERVERX_ODB_AS_MYSQL
        SERVERX_ODB_AS_SQLITE

* Windows

        WIN32
        _WINDOWS
        TARGET_WINDOWS
        _CRT_SECURE_NO_WARNINGS

* Linux

        TARGET_LINUX


* TODO marco 

        __SERVERX_TODO__

 
 temporary record 

### 日志规则

* TODO：日志的一些通用规则
        
        参考目前的日志，会发现很多无效的信息，不相关信息
  
## 第三方程序来源及其编译

### 参考文档

        kodi编译文档: 
        https://github.com/xbmc/xbmc/blob/master/docs/README.md
        https://github.com/xbmc/xbmc/blob/master/docs/README.Windows.md

        目前kodi提供的只是部分已经编译好的二进制文件, 应该最好找齐所有的第三方库的源代码

### 编译

* 工具
  
        Windows
        - Visual Studio 2017 Community Edition 
        - CMake 

         https://cmake.org/download/
         可以直接下载 Windows win64-x64 or Windows win32-x86

         CMake install notes (from kodi)
         All install screens should remain at their default values with the exception of the following.

         Under Install options change default to Add CMake to system PATH for all users or Add CMake to system PATH for current user (whichever you prefer).


### 第三方库

#### windows的第三方库使用

        分为几个步骤

        - 初期使用kodi编译好的第三方库
                注意: 目前有部分源代码使用的是旧版本的第三方库，要注意查看代码区别 
        - 中期准备好源代码，自行编译，替换kodi编译的库
        - 长期放入自动化构架步骤中

#### kodi的第三方库源文件镜像站点
        - http://softlibre.unizar.es/kodi/build-deps/sources/
        - https://mirrors.up.pt/pub/kodi/build-deps/sources/
        - https://mirror.nl.leaseweb.net/xbmc/build-deps/sources/
        - https://mirrors.ustc.edu.cn/xbmc/build-deps/sources/

#### ServerX使用的第三方库

* libiconv
  
        https://www.gnu.org/software/libiconv/
        libiconv库为需要做转换的应用提供了一个iconv()的函数，以实现一个字符编码到另一个字符编码的转换。

        serverx中主要用在 CharsetConverter 工具
        当前版本: 1.15
  
* fribidi

        https://github.com/fribidi/fribidi
        https://directory.fsf.org/wiki/Fribidi


## ServerX的功能模块+工作流程

* 辅助模块
* 功能模块
* 产品模块
* 主工作流程
  



***
### 辅助模块


#### 环境变量+配置
        - URL（云服务，通讯类，鉴权等）

        - 路径环境变量

        - 端口设置（web服务，数据库通讯等）

        - 产品名称（外部显示名，内部缩写等）

        - 版本信息（核心模块版本，主程序版本，git版本等）
  
        - 


#### 日志
#### 调试+异常+crashDump

* MiniDump机制
     - Windows
  
                /serverX/src/Win/miniDump.*

     - Android
     - Linux
  
#### WEB服务

    - json rpc 接口设计
        - 传参可选
        - 错误报告清晰
        - 
        

#### 数据库服务


***
### 功能模块




#### 刮擦
#### 媒体分析
#### 下载

***
### 产品模块
### 测试程序

#### 接口测试程序（golang 实现）
