# Android NDK基础和NDK独立编译SO环境搭建

 

1.Android NDK基础

1.1 简介
本目录下的是安卓ndk的一个副本,主要用具构建安卓native构件.

1. 构建ndk的三种方式
   使用ndk-build工具链进行构建
   使用cmake进行构建
   使用原始命令调用Clang进行构建
   使用独立工具链进行构建
2. 1.2 结构


| 文件              | 作用                              |
| ----------------- | --------------------------------- |
| build             | 构建脚本的储存目录                |
| meta              | 配置文件                          |
| platforms         | 不同Android版本下的链接库         |
| prebuilt          | 预构建文件,主要用于远程调试       |
| python-packages   | python文件夹,python构建的工具集   |
| shader-tools      | 影子工具(没弄懂)                  |
| simpleperf        | cpu性能分析工具                   |
| source            | NDK自带的一些源代码库             |
| sysroot           | 所有头文件的位置                  |
| toolchains        | 针对不同的平台的不同的构建工具链  |
| wrap.sh           | 打包的脚本                        |
| ndk-build.cmd     | 构建工具                          |
| ndk-gdb.cmd       | 调试工具                          |
| ndk-stack.cmd     | 堆栈追踪工具                      |
| ndk-which.cmd     | 在调试时选择ABI和调试工具（弃用） |
| source.properties | ndk版本信息                       |

1.3 目录详解
sysroot
在ndk的根目录中应该包含一个sysroot目录,但是
在toolchains文件中包含真实的构建工具链,
在llvm中包含sysroot目录,其中包含真实的sysroot
目录,在本目录中的sysroot目录只是llvm目录中的sysroot
的一个副本.
prebuilt
预构建的调试器,在其中有对应不同Android目标ABI的远程调试
工具,和本地调试工具.
build
主要的构建脚本所在文件夹,包含构建主要的的规则.类似CLEAR_VARS脚本都在其中.
meta
对于一些数据的配置,例如支持的ABI,支持的平台,一些系统库的版本.
platforms
针对不同的Android版本所对应的链接库,其中每个不同的ABI系列下有不同的对应库文件.在toolchain的llvm文件夹的sysroot目录中包含lib目录,其中是以不同的CPU架构来设置文件夹的,其中每个文件中又包含不同的Android版本库,二者是对应的,但是在链接时需要使用platforms下的系统库,所以这个文件夹不能像假的sysroot目录一样删掉.在Linux系统下,可能此目录是一个假的软连接,但在Windows目录下不是.
python-packages
使用python写成的一些工具集,包含adb,fastboot,gdbrunner三个工具集.应该都是用于ndk-gdb脚本的,暂时不做研究.
shader-tools
性能测试工具,没进行研究.
simpleperf
cpu性能分析工具.
sources
某些第三方库的源文件和构建目录
toolchains
构建目标文件的工具链,包含llvm工具链和针对不同平台的链接器和库文件.
wrap.sh
一个包装脚本,在windows下无法使用.


1.4 工具详解
1.4.1ndk-build.cmd
简介:构建ndk工程的工具,是对一系列make脚本的包装

内部原理:

$GNUMAKE-f\<ndk\>/build/core/build-local.mk\<parameters\>

make构建工具-f\<ndk目录\>/build/core/build-local.mk\<参数\>

命令行调用:在构建目录下运行ndk-build脚本

ndk-build可选参数参考网页:

选项

含义

clean

清除之前生成的所有二进制文件

V=1

显示构建命令

-B

强制执行完整构建

NDK_DEBUG=1

强制执行可调试构建

NDK_DEBUG=0

强制发布build

NDK_HOST_32BIT=1

始终使用32位构建工具

NDK_APPLICATION_MK=<file>

使用指定的Application.mk文件

-C<project>

构建指定project目录下的项目

ndk-build构建工具需要使用:Android.mk和Application.mk

1.4.2 Android.mk构建规则脚本
参考网页
该脚本定义构建规则和构建目标,是构建静态库,动态库,还是构建可执行文件.
同时定义依赖项和导出项.
最基本构建模块:

LOCAL_PATH:=$(callmy-dir)#设定项目目录

include$(CLEAR_VARS)#包含清除变量的系统脚本

LOCAL_MODULE:=hello-jni#声明构建的模块名称

LOCAL_SRC_FILES:=hello-jni.c#声明要构建的文件

include$(BUILD_SHARED_LIBRARY)#声明构建共享库文件

变量与宏:

最好不要使用的变量名称
以LOCAL_开头的变量名称,例如LOCAL_MODULE
以PRIVATE_,NDK_,APP_开头的名称会被构架系统使用.
小写名称,例如my-dir,构建系统也会使用.
如果要是用自定义变量,请以特定名称开头,例如MY_,来避免与内部变量冲突.
include变量(包含指定的构建模式):

#使用方式:

include$(VARS)

CLAER_VARS
清除本地变量,会清除所有LOCAL_开头的变量.
BUILD_EXECUTABLE
根据LOCAL_变量可执行文件.
BUILD_SHARED_LIBRARY
根据LOCAL_变量构建动态库文件.
BUILD_STATIC_LIBRARY
根据LOCAL_变量构建静态库文件.
PREBUILT_SHARED_LIBRARY
指向用于指定预构建共享库的构建脚本。与BUILD_SHARED_LIBRARY和BUILD_STATIC_LIBRARY的情况不同，这里的LOCAL_SRC_FILES值不能是源文件，而必须是指向预构建共享库的单一路径，例如foo/libfoo.so。
PREBUILT_STATIC_LIBRARY
与PREBUILT_SHARED_LIBRARY相同，但用于预构建静态库。
目标信息变量:
构建系统会根据ABI的不同重新解析Android.mk,进行重新构建.

TARGET_ARCH
不同的cpu系列,arm,arm64,x86,x86_64四种架构系列.
TARGET_PLATFORM
对应Android-API版本,例如android-23.
TARGET_ARCH_ABI
解析Android.mk时使用的ABI,
详情见-[ABI架构对应表].
TARGET_ABI
目标AndroidAPI级别和ABI的串联.
例如:android-23-arm64-v8a.
ABI架构对应表

ABI(CPU和架构)

设置名

ARMv7

armeabi-v7a

ARMv8AArch64

arm64-v8a

i686

x86

x86_64

x86_64

实例:

#cpu系列

ifeq($(TARGET_ARCH),arm)

#dosomething

endif

 

#版本信息

ifeq($(TARGET_PLATFORM),android-22)

#dosomething

endif

 

#关于ABI

ifeq($(TARGET_ARCH_ABI),arm64-v8a)

#dosomething

endif

 

#使用目标构建

ifeq($(TARGET_ABI),android-23-arm64-v8a)

#dosomething

endif

模块描述变量:

LOCAL_PATH
定义当前路径.
LOCAL_MODULE
定义当前模块名称.
LOCAL_MODULE_FILENAME
定义生成模块的名称.
LOCAL_SRC_FILES
包含的源文件.
LOCAL_CPP_EXTENSION
使用指定的文件扩展名作为CPP文件的扩展名.
LOCAL_CPP_FEATURES
指定使用的c++特性.例如:-frtti-fexceptions
LOCAL_CPP_FEATURES:=exceptionsrtti

LOCAL_C_INCLUDES
指定的C头文件包含目录,相对于NDKroot目录的路径列表.
LOCAL_CFLAGS
传递给C编译器的命令标识.
LOCAL_CPPFLAGS
传递给C++编译器的命令标识.
LOCAL_STATIC_LIBRARIES
指定当前模块构建时要依赖的静态库.
LOCAL_SHARED_LIBRARIES
指定构建当前模块所需的静态库模块.
LOCAL_WHOLE_STATIC_LIBRARIES
此变量是LOCAL_STATIC_LIBRARIES的变体，表示链接器应将相关的库模块视为完整归档。如需详细了解完整归档，请参阅有关--whole-archive标记的GNUId文档。多个静态库之间存在循环依赖关系时，此变量十分有用。使用此变量构建共享库时，它将强制构建系统将静态库中的所有对象文件添加到最终二进制文件。但是，生成可执行文件时不会发生这种情况。
LOCAL_LDLIBS
此变量列出了在构建共享库或可执行文件时使用的额外链接器标记。利用此变量，您可使用-l前缀传递特定系统库的名称.
例如，以下示例指示链接器生成在加载时链接到/system/lib/libz.so的模块：
LOCAL_LDLIBS:=-lz

说白了,就是链接指定的Android库.AndroidNDK原生API库
注意:该构建变量只对可执行文件和共享库有用,对于静态库无用.

LOCAL_LDFLAGS构建系统在构建共享库和可执行文件时使用的其他连接器标记。例如，在ARM/X86上使用ld.bfd连接器.
LOCAL_LDFLAGS+=-fuse-ld=bfd

静态库不适用此变量.

LOCAL_ALLOW_UNDEFINED_SYMBOLS
默认情况下，如果构建系统在尝试构建共享库时遇到未定义的引用，将会抛出“未定义的符号”错误。此错误可帮助您捕获源代码中的错误。如果停用此检查,请将此变量设置为ture.
注意:静态库不适应此变量.
LOCAL_ARM_MODE
默认情况下，构建系统会以thumb模式生成ARM目标二进制文件，其中每条指令都是16位宽，并与thumb/目录中的STL库链接。将此变量定义为arm会强制构建系统以32位arm模式生成模块的对象文件。
LOCAL_ARM_MODE:=arm

LOCAL_ARM_NEON
此变量仅在以armeabi-v7aABI为目标时才有意义。它允许在C和C++源文件中使用ARMAdvancedSIMD(NEON)编译器固有特性，以及在Assembly文件中使用NEON指令。
请注意，并非所有基于ARMv7的CPU都支持NEON扩展指令集。因此，必须执行运行时检测，以便在运行时安全地使用此代码。如需了解详细信息，请参阅NEON支持和cpufeatures库。此外，您也可以使用.neon后缀，指定构建系统仅以NEON支持来编译特定源文件。在以下示例中，构建系统以Thumb和NEON支持编译foo.c，以Thumb支持编译bar.c，并以ARM和NEON支持编译zoo.c：
LOCAL_SRC_FILES=foo.c.neonbar.czoo.c.arm.neon

LOCAL_DISABLE_FORMAT_STRING_CHECKS
默认情况下，构建系统会在编译代码时保护格式字符串。这样的话，如果printf样式的函数中使用了非常量格式的字符串，就会强制引发编译器错误。此保护默认启用，但您也可通过将此变量的值设置为true将其停用。如果没有必要的原因，我们不建议停用。
LOCAL_EXPORT_CFLAGS
此变量用于记录一组C/C++编译器标记，这些标记将添加到通过LOCAL_STATIC_LIBRARIES或LOCAL_SHARED_LIBRARIES变量使用此模块的任何其他模块的LOCAL_CFLAGS定义中。
include$(CLEAR_VARS)
LOCAL_MODULE:=foo
LOCAL_SRC_FILES:=foo/foo.c
LOCAL_EXPORT_CFLAGS:=-DFOO=1
include$(BUILD_STATIC_LIBRARY)

include$(CLEAR_VARS)
LOCAL_MODULE:=bar
LOCAL_SRC_FILES:=bar.c
LOCAL_CFLAGS:=-DBAR=2
LOCAL_STATIC_LIBRARIES:=foo
include$(BUILD_SHARED_LIBRARY)

在这里，构建系统在构建bar.c时会向编译器传递-DFOO=1和-DBAR=2标记。它还会在模块的LOCAL_CFLAGS前面加上导出的标记，以便您轻松进行替换。

此外，模块之间的关系也具有传递性：如果zoo依赖于bar，而后者依赖于foo，那么zoo也会继承从foo导出的所有标记。

最后，构建系统在执行局部构建时（即，构建要导出标记的模块时），不使用导出的标记。因此，在以上示例中，构建系统在构建foo/foo.c时不会将-DFOO=1传递到编译器。如需执行局部构建，请改用LOCAL_CFLAGS。

LOCAL_EXPORT_CPPFLAGS
类似于LOCAL_EXPORT_CFLAGS,只是面对与CPP的.
LOCAL_EXPORT_C_INCLUDES
此变量与LOCAL_EXPORT_CFLAGS相同，但适用于Cinclude路径。例如，当bar.c需要包括模块foo的头文件时，此变量很有用。
LOCAL_EXPORT_LDFLAGS
此变量与LOCAL_EXPORT_CFLAGS相同，但适用于链接器标记。
LOCAL_EXPORT_LDLIBS
此变量与LOCAL_EXPORT_CFLAGS相同，用于指示构建系统将特定系统库的名称传递到编译器。请在您指定的每个库名称前附加-l。请注意，构建系统会将导入的链接器标记附加到模块的LOCAL_LDLIBS变量值上。其原因在于Unix链接器的工作方式。当模块foo是静态库并且具有依赖于系统库的代码时，此变量通常很有用。然后，您可以使用LOCAL_EXPORT_LDLIBS导出依赖项。
例如：
include$(CLEAR_VARS)
LOCAL_MODULE:=foo
LOCAL_SRC_FILES:=foo/foo.c
LOCAL_EXPORT_LDLIBS:=-llog
include$(BUILD_STATIC_LIBRARY)

include$(CLEAR_VARS)
LOCAL_MODULE:=bar
LOCAL_SRC_FILES:=bar.c
LOCAL_STATIC_LIBRARIES:=foo
include$(BUILD_SHARED_LIBRARY)

在此示例中，构建系统在构建libbar.so时，将在链接器命令的末尾指定-llog。这样就会告知链接器，由于libbar.so依赖于foo，因此它也依赖于系统日志记录库。

LOCAL_SHORT_COMMANDS
当您的模块有很多源文件和/或依赖的静态或共享库时，请将此变量设置为true。这样会强制构建系统将@语法用于包含中间对象文件或链接库的归档。
此功能在Windows上可能很有用，在Windows上，命令行最多只接受8191个字符，这对于复杂的项目来说可能太少。它还会影响个别源文件的编译，而且将几乎所有编译器标记都放在列表文件内。
请注意，true以外的任何值都将恢复为默认行为。您也可以在Application.mk文件中定义APP_SHORT_COMMANDS，对项目中的所有模块强制实施此行为。我们不建议默认启用此功能，因为它会减慢构建速度。
LOCAL_THIN_ARCHIVE
构建静态库时，请将此变量设置为true。这样会生成一个瘦归档，即一个库文件，其中不含对象文件，而只包含它通常包含的实际对象的文件路径。
这对于减小构建输出的大小非常有用。但缺点是，这样的库无法移至其他位置（其中的所有路径都是相对路径）。
有效值为true、false或空白。您可在Application.mk文件中通过APP_THIN_ARCHIVE变量来设置默认值。
注意:此变量对于非静态库和预构件库无用.
LOCAL_FILTER_ASM
请将此变量定义为一个shell命令，供构建系统用于过滤根据您为LOCAL_SRC_FILES指定的文件提取或生成的汇编文件。定义此变量会导致发生以下情况:
构建系统从任何C或者C++文件生成临时汇编文件,而不进行编译.
构建系统在任何临时汇编文件以及LOCAL_SRC_FILES中所列任何汇编文件的LOCAL_FILTER_ASM中执行shell命令,因此会生成另一个临时汇编文件.
构建系统将这些过滤的汇编文件编译到对象文件中.
示例:
LOCAL_SRC_FILES:=foo.cbar.S
LOCAL_FILTER_ASM:=

foo.c--1-->$OBJS_DIR/foo.S.original--2-->$OBJS_DIR/foo.S--3-->$OBJS_DIR/foo.o
bar.S--2-->$OBJS_DIR/bar.S--3-->$OBJS_DIR/bar.o

“1”对应于编译器，“2”对应于过滤器，“3”对应于汇编程序。过滤器必须是一个独立的shell命令，它接受输入文件名作为第一个参数，接受输出文件名作为第二个参数。
例如：

myasmfilter$OBJS_DIR/foo.S.original$OBJS_DIR/foo.S

myasmfilterbar.S$OBJS_DIR/bar.S

NDK提供的宏函数:

my-dir
这个宏返回最后包括的makefile的路径，通常是当前Android.mk的目录。my-dir可用于在Android.mk文件开头定义LOCAL_PATH。
例如：
LOCAL_PATH:=$(callmy-dir)

由于GNUMake的工作方式，这个宏实际返回的是构建系统解析构建脚本时包含的最后一个makefile的路径。因此，包括其他文件后就不应调用my-dir。例如:

LOCAL_PATH:=$(callmy-dir)

 

#...declareonemodule

 

include$(LOCAL_PATH)/foo/`Android.mk`

 

LOCAL_PATH:=$(callmy-dir)

 

#...declareanothermodule

这里的问题在于，对my-dir的第二次调用将LOCAL_PATH定义为PATH/foo，而不是PATH/foo，而不是PATH，因为这是其最近的include所指向的位置。
在Android.mk文件中的任何其他内容后指定额外的include可避免此问题。
例如：

LOCAL_PATH:=$(callmy-dir)

 

#...declareonemodule

 

LOCAL_PATH:=$(callmy-dir)

 

#...declareanothermodule

 

#extraincludesattheendoftheAndroid.mkfile

include$(LOCAL_PATH)/foo/Android.mk

如果以这种方式构造文件不可行，请将第一个my-dir调用的值保存到另一个变量中。
例如：

MY_LOCAL_PATH:=$(callmy-dir)

 

LOCAL_PATH:=$(MY_LOCAL_PATH)

 

#...declareonemodule

 

include$(LOCAL_PATH)/foo/`Android.mk`

 

LOCAL_PATH:=$(MY_LOCAL_PATH)

 

#...declareanothermodule

all-subdir-makefiles
返回位于当前my-dir路径所有子目录中的Android.mk文件列表。利用此函数，您可以为构建系统提供深度嵌套的源目录层次结构。默认情况下，NDK只在Android.mk文件所在的目录中查找文件。
this-makefile
返回当前makefile（构建系统从中调用函数）的路径。
parent-makefile
返回包含树中父makefile的路径（包含当前makefile的makefile的路径）。
grand-parent-makefile
返回包含树中祖父makefile的路径（包含当前父makefile的makefile的路径）。
import-module
此函数用于按模块名称来查找和包含模块的Android.mk文件。典型的示例如下所示：
$(callimport-module,<name>)

在此示例中，构建系统在NDK_MODULE_PATH环境变量所引用的目录列表中查找具有标记的模块，并且自动包括其Android.mk文件。

1.4.3 Application.mk构建配置脚本
概览:
Application.mk指定ndk-build的项目级设置。默认情况下，它位于应用项目目录中的jni/Application.mk下。

注意：其中许多参数也具有模块等效项。例如，APP_CFLAGS对应于LOCAL_CFLAGS。无论何种情况下，特定于模块的选项都将优先于应用级选项。对于标记，两者都使用，但特定于模块的标记将后出现在命令行中，因此它们可能会替换项目级设置。

APP_ABI
默认情况下，NDK构建系统会为所有非弃用ABI生成代码。您可以使用APP_ABI设置为特定ABI生成代码。下表显示了不同指令集的APP_ABI设置。
指令集

值

32位ARMv7

APP_ABI:=armeabi-v7a

64位ARMv8(AArch64)

APP_ABI:=arm64-v8a

x86

APP_ABI:=x86

x86_64

APP_ABI:=x86_64

所有支持的ABI

APP_ABI:=all

详细ABI参见这里

您也可以指定多个值，方法是将它们放在同一行上，中间用空格分隔。例如：

APP_ABI:=armeabi-v7aarm64-v8ax86

APP_ASFLAGS
要传递给项目中每个汇编源文件（.s和.S文件）的编译器的标记。
注意：ASFLAGS与ASMFLAGS不同。后者专用于YASM源文件（请参阅关于APP_ASMFLAGS的部分）。
APP_ASMFLAGS
对于所有YASM源文件（.asm，仅限x86/x86_64），要传递给YASM的标记。
APP_BUILD_SCRIPT
默认情况下，ndk-build假定Android.mk文件位于项目根目录的相对路径jni/Android.mk中。
如需从其他位置加载Android.mk文件，请将APP_BUILD_SCRIPT设置为Android.mk文件的绝对路径。
APP_CFLAGS
要为项目中的所有C/C++编译传递的标记。
注意：Include路径应使用LOCAL_C_INCLUDES而不是显式-I标记。
APP_CLANG_TIDY
若要为项目中的所有模块启用clang-tidy，请将此标记设置为“True”。默认处于停用状态。
APP_CLANG_TIDY_FLAGS
要为项目中的所有clang-tidy执行传递的标记。
APP_CONLYFLAGS
要为项目中的所有C编译传递的标记。这些标记不会用于C++代码。
APP_CPPFLAGS
要为项目中的所有C++编译传递的标记。这些标记不会用于C代码。
APP_CXXFLAGS
注意：APP_CPPFLAGS应优先于APP_CXXFLAGS。
与APP_CPPFLAGS相同，但在编译命令中将出现在APP_CPPFLAGS之后。例如：
APP_CPPFLAGS:=-DFOO

APP_CXXFLAGS:=-DBAR

以上配置将导致编译命令类似于clang++-DFOO-DBAR，而不是clang++-DBAR-DFOO。

APP_DEBUG
若要构建可调试的应用，请将此标记设置为True。
APP_LDFLAGS
关联可执行文件和共享库时要传递的标记。
注意：这些标记对静态库没有影响。不会关联静态库。
APP_MANIFEST
AndroidManifest.xml文件的绝对路径。
默认情况下将使用$(APP_PROJECT_PATH)/AndroidManifest.xml)（如果存在）。注意：使用externalNativeBuild时，Gradle不会设置此值。
APP_MODULES
要构建的模块的显式列表。此列表的元素是模块在Android.mk文件的LOCAL_MODULE中显示的名称。
默认情况下，ndk-build将构建所有共享库、可执行文件及其依赖项。仅当项目使用静态库、项目仅包含静态库或者在APP_MODULES中指定了静态库时，才会构建静态库。
注意：将不会构建导入的模块（在使用$(callimport-module)导入的构建脚本中定义的模块），除非要在APP_MODULES中构建或列出的模块依赖导入的模块。
APP_OPTIM
将此可选变量定义为release或debug。默认情况下，将构建发布二进制文件。发布模式会启用优化，并可能生成无法与调试程序一起使用的二进制文件。调试模式会停用优化，以便可以使用调试程序。
请注意，您可以调试发布二进制文件或调试二进制文件。但是，发布二进制文件在调试期间提供的信息较少。例如，变量可能会被优化掉，导致无法检查代码。此外，代码重新排序会使单步调试代码变得更加困难；堆栈轨迹更可能不可靠。
在应用清单的<application>标记中声明android:debuggable将导致此变量默认为debug，而不是release。通过将APP_OPTIM设置为release可替换此默认值。
意：使用externalNativeBuild进行构建时，AndroidStudio将根据您的构建风格适当地设置此标记。
APP_PLATFORM
APP_PLATFORM会声明构建此应用所面向的AndroidAPI级别，并对应于应用的minSdkVersion。如果未指定，ndk-build将以NDK支持的最低API级别为目标。最新NDK支持的最低API级别总是足够低，可以支持几乎所有有效设备。
警告：将APP_PLATFORM设置为高于应用的minSdkVersion可能会生成一个无法在旧设备上运行的应用。在大多数情况下，库将无法加载，因为它们引用了在旧设备上不可用的符号。
例如，值android-16指定库使用在Android4.1（API级别16）以前的版本中不可用的API，并且无法在运行较低平台版本的设备上使用。如需查看平台名称和相应Android系统映像的完整列表，请参阅AndroidNDK原生API。
使用Gradle和externalNativeBuild时，不应直接设置此参数。而应在模块级别build.gradle文件的defaultConfig或productFlavors块中设置minSdkVersion属性。这样就能确保只有在运行足够高Android版本的设备上安装的应用才能使用您的库。
请注意，NDK不包含Android每个API级别的库，省略了不包含新的原生API的版本以节省NDK中的空间。ndk-build按以下优先级降序使用API：
匹配APP_PLATFORM的平台版本。
低于APP_PLATFORM的下一个可用API级别。例如，APP_PLATFORM为android-20时，将使用android-19，因为android-20中没有新的原生API。
NDK支持的最低API级别。
APP_PROJECT_PATH
项目根目录的绝对路径。
APP_SHORT_COMMANDS
LOCAL_SHORT_COMMANDS的项目级等效项。如需了解详情，请参阅Android.mk中有关LOCAL_SHORT_COMMANDS的文档。
APP_STL
用于此应用的C++标准库。
默认情况下使用systemSTL。其他选项包括c++_shared、c++_static和none。请参阅NDK运行时和功能。
APP_STRIP_MODE
要为此应用中的模块传递给strip的参数。默认为--strip-unneeded。若要避免剥离模块中的所有二进制文件，请将其设置为none。如需了解其他剥离模式，请参阅剥离文档。
APP_THIN_ARCHIVE
若要为项目中的所有静态库使用瘦归档，请将此变量设置为“True”。如需了解详情，请参阅Android.mk中有关LOCAL_THIN_ARCHIVE的文档。
APP_WRAP_SH
要包含在此应用中的wrap.sh文件的路径。
每个ABI都存在此变量的变体，ABI通用变体也是如此：
APP_WRAP_SH
APP_WRAP_SH_armeabi-v7a
APP_WRAP_SH_arm64-v8a
APP_WRAP_SH_x86
APP_WRAP_SH_x86_64
注意：APP_WRAP_SH_<abi>可能无法与APP_WRAP_SH结合使用。如果有任何ABI使用特定于ABI的wrap.sh，所有ABI都必须使用该wrap.sh。

1.5 Android NDK其他
1.5.1 Android版本号
版本号

API版本号

2.3.3-2.3.7

10

4.0.3-4.0.4

15

4.1.x

16

4.2.x

17

4.3

18

4.4

19

5.0

21

5.1

22

6.0

23

7.0

24

7.1

25

8.0

26

8.1

27

9

28

10

29

11

30

 

这个版本的NDK只支持API16以上的
注意

ndk-witch.cmd是适用于GCC系列的调试选择工具,对于LLVM不适用,而现在ndk又使用LLVM做编译器,弃用了GCC,那么这个调试工具之应用于GCC所构建的项目之中,因此,其也要被废弃。

1.5.2 精简内容
删除了大量的NOCTICE文件.
删除了Change文件.
删除了软连接sysroot文件夹.
删除ndk-switch脚本.
1.5.3 免责声明
本副本只是一个个人研究的版本,如若用于商业用途,请使用Android官方发布的正式版本。请勿将本版本ndk用于商业用途。

1.5.4 文档
NDK文档,教程,还有API的信息可以访问我们的网站.

NDK代码示例可以访问GitHub.

关于AndroidStudio的信息可以访问AndroidStudio网页.

1.5.5 发现GUG
只要能运行,谁在乎???

NDKbugsshouldbefiledonGitHub.

AndroidStudioandGradlebugsshouldbefiledintheAndroidStudioBugTracker.Forthefastestresponse,makesureyoufollowtheirguideonFilingBugs.

 

2. Android NDK安装调试
2.1 NDK官网下载
首先去官网下载需要安装的Android NDK版本：

1.官网NDK 下载地址

https://developer.android.google.cn/ndk/downloads/

 

2.各NDK版本直接下载地址

最新稳定版本r22

https://dl.google.com/android/repository/android-ndk-r22-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r22-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r22-linux-x86_64.zip

 

最新LTS版本r21e

https://dl.google.com/android/repository/android-ndk-r21e-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21e-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21e-linux-x86_64.zip

 

其他历史r21版本

https://dl.google.com/android/repository/android-ndk-r21d-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21d-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21d-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21c-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21c-darwin-x86_64.zip

 

https://dl.google.com/android/repository/android-ndk-r21c-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r21-linux-x86_64.zip

 

历史版本：

https://dl.google.com/android/repository/android-ndk-r20b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r20b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r20b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r20b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r19c-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r19c-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r19c-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r19c-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r18b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r18b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r18b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r18b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r17c-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r17c-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r17c-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r17c-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r16b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r16b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r16b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r15c-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r15c-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r15c-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r15c-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r14b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r14b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r14b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r14b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r13b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r13b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r13b-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r13b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r12b-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r12b-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r12b-darwin-x86_64.zip

 

https://dl.google.com/android/repository/android-ndk-r12b-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r11c-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r11c-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r11c-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r11c-linux-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r10e-windows-x86.zip

https://dl.google.com/android/repository/android-ndk-r10e-windows-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r10e-darwin-x86_64.zip

https://dl.google.com/android/repository/android-ndk-r10e-linux-x86_64.zip

 

2.2 NDK安装配置
下面以64位Windows版的android-ndk-r20b为例对NDK的安装配置进行介绍。

2.2.1 解压压缩包
先将下载的android-ndk-r20b-windows-x86_64.zip压缩包解压。



 

解压后android-ndk-r20b目录结构如下图所示：



 

2.2.2 配置环境变量
将NDK路径添加到环境变量中并点击确定（修改完成后重启电脑或者进入DOS命令提示符，输入：set PATH=C: 即可生效）。



 

 

2.3编译简单样例测试
2.3.1 新建jni文件夹
在NDK根目录下新建个jni命名的文件夹



 

2.3.2 新建编译文件
在jni命名的文件夹中新建三个文件：hello-jni.cpp, Android.mk, Application.mk



hello-jni.cpp内容：

#include <stdio.h>

int main()

{

    printf("Hello Android!\n");
     
    return 0;

}
Android.mk内容：

# 一个Android.mk file首先必须定义好LOCAL_PATH变量。

# 它用于在开发树中查找源文件。在这个例子中，宏函数’my-dir’,

# 由编译系统提供，用于返回当前路径（即包含Android.mk file文件的目录）。

LOCAL_PATH := $(call my-dir)

# CLEAR_VARS由编译系统提供，指定让GNU MAKEFILE为你清除许多LOCAL_XXX变量

#（例如 LOCAL_MODULE, LOCAL_SRC_FILES, LOCAL_STATIC_LIBRARIES, 等等...),除LOCAL_PATH 。

#这是必要的，因为所有的编译控制文件都在同一个GNU MAKE执行环境中，所有的变量都是全局的。

include $(CLEAR_VARS)

# LOCAL_MODULE变量必须定义，以标识你在Android.mk文件中描述的每个模块。名称必须是唯一的，而且不包含任何空格。

# 注意编译系统会自动产生合适的前缀和后缀，换句话说，一个被命名为'foo'的共享库模块，将会生成'libfoo.so'文件。

LOCAL_MODULE    := hello-jni

# LOCAL_SRC_FILES变量必须包含将要编译打包进模块中的C或C++源代码文件。注意，你不用在这里列出头文件和包含文件，

# 因为编译系统将会自动为你找出依赖型的文件；仅仅列出直接传递给编译器的源代码文件就好。

LOCAL_SRC_FILES := hello-jni.cpp

# BUILD_EXECUTABLE 表示以一个可执行程序的方式进行编译

# BUILD_SHARED_LIBRARY 表示动态链接库的方式进行编译

include $(BUILD_EXECUTABLE)
Application.mk内容:

#### 1.Android API 级别版本号

APP_PLATFORM := android-29

#### 2.CPU 与指令集的每种组合专属的应用二进制接口 (ABI)：

#### armeabi-v7a arm64-v8a x86 x86_64 all

APP_ABI := all

#### 3.C++标准库 libc++  c++_shared c++_static system none等

APP_STL := c++_shared 

#### 4.编译版本release 或 debug，默认为debug

APP_OPTIM := release


2.3.3 编译调试
1、进入NDK环境的build目录。



 

2、在build目录路径栏输入cmd后按Enter键，弹出build目录路径的DOS界面。





 

3、在DOS界面命令行输入ndk-build,按Enter键NDK即可运行编译hell-jni.cpp文件。如下图即为正常编译，否则报错则需分析定位解决后再运行编译。



 

2.3.4 编译生成的文件
NDK运行编译完成后会在根目录下生成libs文件夹，并在里面生成相应APP_ABI的编译文件夹如下图所示：



 

在相应APP_ABI的编译文件夹下面有编译生成的二进制文件，比如本例x86_64文件夹中如下图所示：




2.4 XXX模组编译so
2.4.1 搭建XXX模组编译so的NDK环境
如“2.3 编译简单样例测试所”所述流程，同样可搭建XXX模组编译so环境。搭建XXX模组的jni文件夹如下图所示：（略）

其他依赖的文件都统一放在jni目录下的include目录中，根据待编译源码分析添加。

目前在搭建的NDK环境中编译出的XXX模组标定数据so文件如下图所示：（略）

2.4.2 调试过程中添加打印信息的方法 
如果编译so调试过程中遇到比较复杂的问题，需要添加打印信息分析定位。在 Android.mk里打印信息有三种方法：

a. $(info string) 打印正常输入信息   
b. $(warning string) 打印警告信息   
c. $(error string) 打印错误信息
以上三种打印方式：

a. 各个打印方式的string里都可以带上变量；   
b. 如果遇到error打印，将会停止编译进程。

举例说明各个打印函数的方法,以下以packages/apps/Music/Android.mk为例，内容如下：

LOCAL_PATH:= $(call my-dir)     
$(warning "Warning Just for Test LOCAL_PATH is $(LOCAL_PATH)")   
$(info "Info Just for Test LOCAL_PATH is $(LOCAL_PATH)") 

include $(CLEAR_VARS) LOCAL_MODULE_TAGS := optional   
LOCAL_SRC_FILES := $(call all-java-files-under, src)  src/com/android/music/IMediaPlaybackService.aidl 

LOCAL_PACKAGE_NAME := Music   
LOCAL_SDK_VERSION := current   
LOCAL_CERTIFICATE := platform   
LOCAL_PROGUARD_FLAG_FILES := proguard.flags   
include $(BUILD_PACKAGE)   

# Use the folloing include to make our test apk. 
include $(call all-makefiles-under,$(LOCAL_PATH))

 

2.5 so文件烧录调试
首先得准备好Qualcomm/MTK手机进行刷机和解锁，详见：（略）

手机解锁脚本：（略）

然后将编译好的so文件烧录到手机中，进行拍照并抓取log。

 

烧录so文件命令：

adb boot

adb remount

adb push  src/xxx.so   vendor/lib64

烧录so脚本：（略）

 

导出照片命令：

adb boot

adb remount

adb pull sdcare/DCIM/Camera   dst/

导出照片脚本：（略）

 

抓取log命令：

adb boot

adb remount

adb pull data/debuglogger/mobilelog/  dst/

抓取log脚本：（略）

 

其他问题说明：

q1:为避免混乱将依赖的头文件统一放在jni目录下新建的include目录中，编译找不到头文件。 s1：Android.mk中添加LOCAL_C_INCLUDES += $(LOCAL_PATH)/include/）

q2:xxx.h依赖的yyy.h头文件找不到？s1：将xxx.h中依赖头文件yyy.h的路径修改为NDK环境下的路径。

q3:undefined reference to ‘xxx’问题  s3:在根目录Android.mk中添加LOCAL_ALLOW_UNDEFINED_SYMBOLS := true可以规避（实际上这样解决有问题，曾遇到根因是子目录Android.mk脚本中LOCAL_SRC_FILES += $($(wildcard $(LOCAL_PATH)/*.cpp):$(LOCAL_PATH)/%=%)未能实现，手动实现LOCAL_SRC_FILES 添加最终解决问题）

q4:将NDK编译好的xxx模组的so烧录手机后相应的app挂掉？s4:APP_ABI和APP_PLATFORM等系统架构变量设置要跟手机版本信息对齐匹配，将xxx模组直接相关的所有so和NDK编译生成的动态库共so全部烧录到手机vendor/lib64中，相应的app功能正常了 （首先得保证源码逻辑正确，否则先得解决源码逻辑问题否则也会导致相应的app挂掉）

 

参考

NDK官方指南：

https://developer.android.google.cn/ndk/guides

Gitee NDK介绍：

https://gitee.com/sipu/Android_NDK?skip_mobile=true#%E7%B2%BE%E7%AE%80%E5%86%85%E5%AE%B9
————————————————
版权声明：本文为CSDN博主「Abona」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Bonaventure/article/details/115265659