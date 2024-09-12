# Makefile

这个内容在课程文件里面在Lab4中涉及，看了半天ppt学不懂，后经搜索后发现于老师自己在B站上面专门发了一个[Makefile的讲解视频](https://www.bilibili.com/video/BV188411L7d2/?spm_id_from=333.337.search-card.all.click&vd_source=e83f6243963f730d4d2994d23f588a9d)，这个视频就牛逼多了。利用四个例子逐步给新手解释清楚了Makefile的底层逻辑。

Makefile是用来辅助编译的一个工具。四个例子的源代码是一样的，只是Makefile文件不一样。

源代码包含四个文件如下

```cpp
// factorial.cpp
#include "functions.h"

int factorial(int n){
    if (n == 1)
        return 1;
    else
        return n * factorial(n - 1);
}
```

```cpp
// printhello.cpp
#include <iostream>
#include "functions.h"
using namespace std;

void printhello(){
    int i = 0;
    cout << "Hello World!" << endl;
}
```

```c
// functions.h
#ifndef _FUNCTIONS_H_
#define _FUNCTIONS_H_
void printhello();
int factorial(int n);
#endif
```

```cpp
// main.cpp
#include <iostream>
#include "functions.h"
using namespace std;

int main(){
    printhello();

    cout << "This is main:" << endl;
    cout << "The factorial of 5 is: " << factorial(5) << endl;
    return 0;
}
```

# Version1

Makefile文件没有后缀，直接命名为"Makefile"或者"makefile"。在下载了Makefile之后，直接在命令行使用`make`​执行工作路径下的 `makefile`​ 文件

第一版的Makefile内容如下

```Makefile
## Version 1
hello: main.cpp printhello.cpp factorial.cpp
	g++ -o hello main.cpp printhello.cpp factorial.cpp
```

其中 `#`​ 以后的内容是注释。第二行和第三行是标准的Makefile基本语句。

```Makefile
targets : prerequisites
<TAB> command
```

第二行的意义为：名为hello的文件依赖于main.cpp, printhello.cpp, factorial.cpp。在执行Makefile时，系统会检查文件这些文件的最后一次修改的时间，如果hello的时间晚于main.cpp, printhello.cpp, factorial.cpp中的任意一个，那么系统就会执行第三行。

> 即如果`prerequistes`​ 中有在target之后的更新（prerequistes文件的更新时间比target更晚）或者target不存在则系统就会执行下面的`<TAB>`​ 之后的命令。如果没有可执行的内容，Makefile就会返回 `target is up to date.`​(目标已经最新)

上面这段内容就涵盖了Makefile文件的基本逻辑。剩下的内容都是Makefile的技巧和功能。

# Version2

Makefile的一个功能就是“变量定义”（虽然感觉更像宏）

```Makefile
## Version 2
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.o

$(TARGET): $(OBJ)
	$(CXX) -o $(TARGET) $(OBJ)

main.o: main.cpp
	$(CXX) -c main.cpp

printhello.o: printhello.cpp
	$(CXX) -c printhello.cpp

factorial.o: factorial.cpp
	$(CXX) -c factorial.cpp
```

本例子中，定义 `CXX`​ 为 `g++`​ ，于是在Makefile文件后面代码中，所有出现的 `$(CXX)`​ 都等价于出现 `g++`​。其它的变量功能相同。

此外，这个版本将 compile 和 link 功能分开了。

第6-7行表明了最后的可执行文件hello和object文件的依赖关系，而后面9-16行表明了object文件和.cpp文件的依赖关系。

系统在执行Makefile文件时，会自动整理依赖关系（我感觉有点像是逐行读取然后递归式的寻找）。例如本例子中，hello 依赖于三个 .o 文件，而三个 .o 文件各自依赖于各自的 .cpp 文件。

相比于Version 1，当前版本的好处是如果多个（这里是三个） .cpp 文件中，只有一个main.cpp更新了，那么系统只会编译main.cpp，然后link。因为系统检查时，发现 main.cpp 的更新时间晚于 main.o ，就会执行下面的命令生成 main.o 。然后检查到 main.o 的更新时间晚于 hello，就会执行命令生成 hello。

# Version3

```Makefile
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.OBJ

CXXFLAGS = -c -Wall
# -Wall: to display all warnings

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^
# $@ stand for target
# $^ stand for objects

%.o: %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@
# $< stand for the frist objects

.PHONY: clean
clean:
	rm -f *.o $(TARGET)
```

相较于前面两个版本，Version3增加了几个新特性

* ​`CXXFLAGS`​ 是编译参数设置，替换了前面的 `-c`​ ，同时增加了 `-Wall`​ 。这个参数的含义是渲染所有的warning。如果你实验的话，在执行make命令后，会产生warining，在printhello.cpp文件中的变量 `i`​ 只被声明而未被调用。
* 一些小的缩写tips

  * 在 `<TAB>`​ 后

    * ​`$@`​ 等价于上面一行中的目标项（即冒号之前的文件名）
    * ​`$^`​ 等价于上面一行中的依赖项（即冒号以后的所有文件名）
    * ​`$<`​ 等价于上面一行中的依赖项的第一个文件名（即冒号以后的第一个文件名）
  * ​`%...`​ 会逐个遍历匹配的内容

    * ​`%.o`​ 逐个匹配所有具有后缀.o的文件
    * ​`%.cpp`​ 逐个匹配所有具有后缀.cpp的文件
* ​`clean`​

  * 暂时先不管上面的 `.PHONY: clean`​。第18、19行的内容表示目标是clean文件，没有依赖项。要生成 clean 文件，就会执行下面的命令，效果是强制清除所有的 object 文件以及最后的 target 。但是由于 clean 没有任何依赖项，所以执行 make 命令的时候，不会为其检查更新。只有当指明执行 make clean 的时候，才会执行下面的命令。
  * ​`.PHONY: clean`​ 是产生一个伪目标，防止项目中真的存在一个名叫clean 的文件使得 `make clean`​ 被阻止执行（系统检测到 clean 存在而没有依赖项，会报告 clean 已经最新）。而 `.PHONY`​ 是一个不可能存在的文件，但是他依赖 clean，它使得即使 clean 文件已经存在，`make clean`​ 也会生效。

# Version 4

```Makefile
## Version 4
CXX = g++
TARGET = hello
CXXFLAGS = -c -Wall
SRC = $(wildcard *.cpp) 
#wildcard function will search all the matched files and stored their name
OBJ = $(patsubst %.cpp, %.o, $(SRC))
#patsubst will substitude ".cpp" to ".o"  for all files in $(SRC)

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^
# $@ stand for target
# $^ stand for objects

%.o: %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@
# $< stand for the frist objects

.PHONY: clean
clean:
	rm -f *.o $(TARGET)
```

该版本使用了两个函数（functions），更方便直接的获取了所有的 .cpp 文件名以及编译产生的 .o 文件名

* ​[`\$(wildcard pattern…)`](https://www.gnu.org/software/make/manual/html_node/Wildcard-Function.html)​: 当前目录下搜索所有的满足匹配条件的文件名

  * ​`SRC = $(wildcard *.cpp)`​ 会将所有以 .cpp 结尾的文件名全部返回给 SRC
* ​[`\$(patsubst pattern,replacement,text)`](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html)​: 将 text 中的所有匹配 pattern 的内容全部替换为 replacement 

  * ​`$(patsubst %.cpp, %.o, $(SRC))`​则会将所有的 .cpp 文件名（`$(SRC)`​ 存储了所有 .cpp 文件名）替换为 .o 文件名

# Version 5

这个版本是我自己加的，我发现 Version 4 虽然好用，但是无法处理头文件依赖项。可以看到其中没有管头文件。如果头文件发生了修改，但是在 makefile 检查依赖项时发现没有文件更新，于是不会重新编译。

头文件依赖项是较难处理的，因为头文件不是简单的看名称就可以判断依赖性，而是要看内容，代码中到底引用了哪些头文件。例如这个例子中，不仅仅只有 `printhello.cpp`​ 和 `factorial.cpp`​ 依赖 `functions.h`​ ，包括 `main.cpp`​ 也依赖 `functions.h`​ 。难道要一个个手写吗，可想而知一旦项目大起来、头文件多起来，这是一种极为不健康的行为。

好在可以使用 `-MMD`​ 或 `-MD`​ 选项来生成 `.d`​ （依赖文件），这些文件包含了源文件（如`.cpp`​文件）与其包含的头文件之间的依赖关系。

* ​`-MMD`​：生成依赖文件，但忽略系统头文件。
* ​`-MD`​：生成依赖文件，包括系统头文件。

于是就有了新的模板

```makefile
## Version 5
CXX = g++
CXXFLAGS = -c -Wall
TARGET = hello
SRC = $(wildcard *.cpp) 
# wildcard function will search all the matched files and stored their name
OBJ = $(patsubst %.cpp, %.o, $(SRC))
# patsubst will substitute ".cpp" to ".o" for all files in $(SRC)
DEP = $(patsubst %.cpp, %.d, $(SRC))
# patsubst will substitute ".cpp" to ".d" for all files in $(SRC)

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^
# $@ stand for target
# $^ stand for objects

%.o: %.cpp
	$(CXX) -c $(CXXFLAGS) -MMD -MP $< -o $@
# $< stand for the first object
# -MMD: Generate dependency file
# -MP: Create phony targets for each header file

-include $(DEP)
# Include dependency files

.PHONY: clean
clean:
	rm -f *.o *.d $(TARGET)
```
