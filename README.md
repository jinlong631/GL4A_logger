# GL4A_logger
>一款用于设备追踪崩溃和数据的日志库(log)，仅包含头文件，支持组件/模块开关
# 1 目的：
>  用于工业设备问题追踪和分析。<br>
>  用于在运行和调试过程中，记录崩溃所在的大致位置。<br>
>  用于在日常运行过程中，监控必要的数据。<br>
# 2 特点
 > 记录时间小于1ms；<br>
 > 实时记录，可用于崩溃追踪；<br>
 > 缓冲记录，可用于记录日常运行数据；<br>
 > 记录文件名和行号，并支持格式化输出；<br>
 > 支持运行过程中实时重加载配置；<br>
 > 可记录在控制台中，自由选择功能开关；<br>
 > 可记录在文件中，自由选择功能开关；<br>
 > 可自由添加组件/模块，分模块记录，模块可自由开关；<br>
 > 可自由配置日志等级；<br>
 >支持实时配置日志参数；<br>
 > 支持多线程操作。<br>
# 3 组成
## 3.1 主要概念
  >组件、日志记录器、等级<br>
  >组件可由用户增删改，组件可自由开关，如果某个组件关闭，那么所有其挂载的日志记录器均不会对其进行日志记录。<br>
 > 日志记录器分为console控制台日志记录、rolling滚动型日志记录、daily按天日志记录。三种日志记录器均可以自由开关，如果某种日志记录器被关闭，那么所有此类型的日志均不会被记录。<br>
  >等级分为trace和error，其中，error等级用于追踪崩溃，trace用于日常数据记录。所有error日志均会在trace中备份记录一份，主要用于保持trace数据上下文完整性。<br>
  >所有配置项均存在config文件夹中的配置文件GL4A_config.cfg中。<br>
# 4 使用

## 4.1 基本用法
~~~
{
//引用头文件
#include "GL4A_user.h"

// 在程序入口处，根据配置文件路径，初始化日志库
eGL4A_init(GL4A_DEFAULT_CONFIG_DIR);

// 在M0组件函数入口处，记录ERROR，并标识为 IN（用于定位大致崩溃位置）
GL4A_ERROR_LOG("M0", __F__, __L__, "XXX函数，IN！\n");
// 在中间必要时，记录 TRACE，用于日常数据记录
GL4A_TRACE_LOG("M0", __F__, __L__, "记录数据！\n");
// 在M0组件函数入口处，记录ERROR，并标识为 OUT（用于定位大致崩溃位置）
GL4A_ERROR_LOG("M0", __F__, __L__, "XXX函数，OUT！\n");

// 在程序出口处，关闭日志库
GL4A_close();
}
~~~
~~~
{
	cout << "欢迎使用GL4A：基本用法" << endl;
	cout << "版本：" << GL4A_VERSION << endl;
	errHandle err = errHandle();
	// 初始化日志库（在程序入口处）
	cout << "对GL4A进行初始化......，默认配置文件路径：" << GL4A_DEFAULT_CONFIG_DIR << endl;
	err = GL4A_init(GL4A_DEFAULT_CONFIG_DIR);
	if (err == 0)
	{
		cout << "日志库初始化成功" << endl;
	}
	else
	{
		cout << "日志库初始化失败" << endl;
	}

	// 将 组件 M0 输出到 TRACE 和 ERROR
	// 在上一级目录下生成log文件夹，分别含有critical/rolling/daily三个文件夹
	// 由于默认配置文件中只开启了daily日志记录器，所有仅daily中记录了 trace 和 error
	GL4A_TRACE_LOG("M0", __F__, __L__, "TRACE 测试成功！\n");
	GL4A_ERROR_LOG("M0", __F__, __L__, "ERROR 测试成功！\n");

	// .... 继续输出

	// 关闭日志库（在程序退出时）
	cout << "日志库使用完毕!" << endl;
	GL4A_close();
  }
~~~
>注：当程序发生崩溃时，相应函数入口ERROR IN会被记录，但ERROR OUT不会被记录，那么，根据记录的文件名和行号，即可定位崩溃发生的位置。
在记录ERROR时，同时会在TRACE中备份记录一份，用于保持TRACE记录上下文的完整性。<br>
## 4.2 基本函数
  >主要包括两个部分：
  >1.	日志库初始化/关闭/重加载，日常输出<br>
  >>GL4A_init——用于初始化日志库<br>
  >>GL4A_reloadConfig——用于实时重加载日志配置文件<br>
  >>GL4A_close——用于关闭日志库<br>
  >>GL4A_TRACE_LOG——用于输出TRACE日志<br>
  >>GL4A_ERROR_LOG——用于输出ERROR日志<br>
  >2.	日志库配置文件修改<br>
  >>loadCfgFileToStruct——用于获取配置文件结构体<br>
  >>saveCfgFileFromStruct——用于将配置结构体保存到配置文件<br>
  >>genConfigDefault——重新按照默认的配置结构体生成配置文件<br>
  >>TotalCfgInfos——配置结构类，其中包含对配置项的基本操作<br>
  >注：如果对json格式比较了解，可直接对配置文件进行操作<br>
  >注：具体参见GL4A_user.h中GL4A命名空间，有非常详尽的注释 <br>
