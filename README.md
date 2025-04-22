[简体中文](README.md) [English](README_EN.md)
# MBeautifier

MBeautifier 是一个轻量级的基于 M-Script 的 MATLAB 源代码格式化工具，可以直接在 MATLAB 编辑器中使用。
![Basic working](https://cloud.githubusercontent.com/assets/12681120/20592407/904cb1d6-b22d-11e6-93dd-1637c3738e50.png)

主要特性

---

-   在运算符和关键字周围添加空白字符
-   可配置的缩进字符和级别。使用 MATLAB 编辑器的智能缩进功能
-   删除/添加连续的空行
-   在矩阵和元胞数组初始化中插入缺失的元素分隔符（逗号）
-   在矩阵和元胞数组初始化中插入缺失的连续符号行
-   将连续的多行代码合并为一行
-   格式化 MATLAB 编辑器的当前页面，或仅格式化编辑器中的选定内容或文件
-   上述所有内容均可通过单个 XML 文件进行配置

部署与配置

---

只需将根目录添加到 MATLAB 路径中。

### 配置

配置可以通过编辑`MBeautifier\resources\settings\MBeautyConfigurationRules.xml` 文件修改

#### 配置规则

目前实现了三种类型的配置规则。 `操作符规则`，`关键字规则`和`特殊规则`。

#### 操作符规则

每个 `OperatorPaddingRule` 表示单个运算符的格式化规则，包含一个键、应被替换的字符串以及用于替换的字符串。

```xml
<OperatorPaddingRule>
    <Key>NotEquals</Key>
    <ValueFrom>~=</ValueFrom>
    <ValueTo> ~= </ValueTo>
</OperatorPaddingRule>
```

上面的例子展示了“不等于”运算符的规则。`ValueFrom` 节点存储运算符 `~=`，而 `ValueTo` 节点存储预期格式：该运算符应在前后各添加一个空格字符。

#### 关键字规则

每个`KeyworPaddingRule`代表单个关键字的格式化规则，包括关键字本身以及右侧所需空白填充的数值。

```xml
    <KeyworPaddingRule>
    	<Keyword>properties</Keyword>
    	<RightPadding>1</RightPadding>
    </KeyworPaddingRule>
```

上面的例子展示了关键字`properties`的规则。`RightPadding` 节点存储了预期的右侧填充空格数量：该关键字应在其后跟随一个空格字符。

> 注意：并不是所有的关键字都被列出了 ── 仅列出那些控制填充有意义的关键字。

#### 特殊规则

这些规则实际上是 MBeautifier 某些功能的开关。

当前的特殊规则列表：

##### 关于新行的特殊规则

-   **MaximalNewLines**: 整数值。MBeautifier 将移除连续的空行。此规则可用于指定最大连续空行的数量。
-   **SectionPrecedingNewlineCount**: 整数值。定义段落注释 (`%% `) 前应有多少空行。负值表示不需要特殊格式化（最终格式由输入和 MaximalNewLines 规则决定）。对于任何大于或等于零的数字 "X"：段落注释将正好被 X 行空行前置。
-   **SectionTrailingNewlineCount**: 整数值。定义段落注释 (`%% `) 后应有多少空行。负值表示不需要特殊格式化（最终格式由输入和 MaximalNewLines 规则决定）。对于任何大于或等于零的数字 "X"：段落注释将正好被 X 行空行后置。
-   **EndingNewlineCount**: 整数值。定义输入末尾应放置多少空行。负值表示不需要特殊格式化（最终格式由输入和 MaximalNewLines 规则决定）。对于任何大于或等于零的数字 "X"：输入将正好以 X 行空行结尾。
-   **AllowMultipleStatementsPerLine**: [1|0]。如果设置为 1，MBeautifier 将允许每行多个语句 (`a = 1; b = 2;`)，否则它会将每个语句拆分为新行。默认值为 "0"。

##### 关于矩阵和元胞数组分隔符的特殊规则

-   **AddCommasToMatrices**: [1|0]. 表示是否应在矩阵中插入缺失的元素分隔符逗号。例如：`[1 2 3]` 将被格式化为 `[1, 2, 3]`。
-   **AddCommasToCellArrays**: [1|0]. 表示是否应在元胞数组中插入缺失的元素分隔符逗号。例如：`{1 2 3}` 将被格式化为 `{1, 2, 3}`。

##### 特殊规则算术运算符

-   **MatrixIndexing_ArithmeticOperatorPadding**: [1|0]. 表示在使用算术运算符对矩阵进行索引时，是否应使用空格填充算术运算符（使用操作符填充规则）。例如：`matrix(end+1) = 1` 可以在值设置为 0 时格式化为 `matrix(end+1) = 1`，或在值设置为 1 时格式化为 `matrix(end + 1) = 1`。
-   **CellArrayIndexing_ArithmeticOperatorPadding**: [1|0]. 表示与 `MatrixIndexing_ArithmeticOperatorPadding` 相同，但适用于元胞数组。

##### 关于连续行的特殊规则

-   **InlineContinousLines**: [1|0]. 如果设置为 1，MBeautifier 将在矩阵（方括号 `[]` 内）和大括号 `{}` 外的所有地方内联连续行操作符 ("...")。内联意味着：`...` 操作符将被移除，并且下一行的内容将被复制到其位置。
-   **InlineContinousLinesInMatrixes**: [1|0]. 与 **InlineContinousLines** 相同，但仅对方括号 (`[]`) 内的内容生效。
-   **InlineContinousLinesInCurlyBracket**: [1|0]. 与 **InlineContinousLines** 相同，但仅对大括号 (`{}`) 内的内容生效。

##### 关于缩进的特殊规则

-   **IndentationCharacter**: [white-space|tab]. 指定自动缩进时使用的字符：空格还是制表符。默认值为 "white-space"。
-   **IndentationCount**: 整数值。指定自动缩进的级别（每个缩进级别包含多少个 **IndentationCharacter**）。默认值为 "4"。
-   **Indentation_TrimBlankLines**: [1|0]. 指定是否通过 MBeautifier 删除（清空）仅包含空白字符的行（这些空白行是自动缩进的结果）。默认值为 "1"，因为这可能会导致文件体积更小。
-   **Indentation_Strategy**: ['AllFunctions'|'NestedFunctions'|'NoIndent']. 控制 MBeautifier 使用的 MATLAB 编辑器中的“函数缩进格式”偏好设置：更改函数体的缩进级别。可能的值包括："AllFunctions" - 为每个函数的主体添加缩进；"NestedFunctions" - 仅为嵌套函数的主体添加缩进；"NoIndent" - 所有函数体的缩进与函数关键字本身的缩进相同。

#### 指令

MBeautifier 指令是在源代码中使用的特殊构造，用于在格式化过程中控制 MBeautifier。下面的示例控制名为`Format`的指令，并将其值设置为`on`，然后设置为`off`。_应该是先设置为`off`再设置`on`── 译者注_

```matlab
    a =  1;
    % MBeautifierDirective:Format:Off
    longVariableName = 'where the assigement is';
    aligned          = 'with the next assignment';
    % MBD:Format:On
    someMatrix  =  [1 2 3];
```

指令行的标准格式为：

-   模式如下：`<ws>%<ws>MBeautifierDirective<ws>:<ws>:NAME<ws>:<ws>VALUE<ws>[NEWLINE]` 或 ` <ws>%<ws>MBD<ws>:<ws>:NAME<ws>:<ws>VALUE<ws>[NEWLINE]`，其中：
    -   `<ws>` 表示零个或多个可选的空白字符。
    -   `NAME` 表示指令名称（仅限拉丁字母，不区分大小写）。
    -   `VALUE` 表示指令值（仅限拉丁字母，不区分大小写）。
-   不得包含任何代码或尾随注释（仅允许上述指令注释）。
-   不得位于块注释内。
-   不得位于任何续行符中。
-   关键字 `MBeautifierDirective` 可与 `MBD` 互换使用。

> **注意：未出现在下列表中的指令名称，或不适用于指定指令的指令值将被忽略，并伴随 MATLAB 警告。**

##### 指令列表

###### `Format`

关于总体控制格式化过程的指令。

可能的值：

-   `on` - 启用格式化
-   `off` - 禁用格式化

示例：

在下面的代码片段中，MBeautifier 使用当前激活的配置来格式化第一行，但不会格式化第 2、3、4、5 行。最后一行将再次使用当前配置进行美化。

```matlab
a =  1;
% MBeautifierDirective:Format:Off
longVariableName = 'where the assigement is';
aligned          = 'with the next assignment';
% MBeautifierDirective:Format:On
someMatrix  =  [1 2 3];
```

The formatted code will look like (configuration dependently):
格式化后的代码将如下所示（配置相关）：

```matlab
a = 1;
% MBeautifierDirective:Format:Off
longVariableName = 'where the assigement is';
aligned          = 'with the next assignment';
% MBeautifierDirective:Format:On
someMatrix = [1, 2, 3];
```

## 用法

### 从 MATLAB 命令窗口

目前支持四种方法：

-   对 MATLAB 编辑器当前活动页面进行格式化。命令：`MBeautify.formatCurrentEditorPage()`。默认情况下，文件不会被保存，但会在编辑器中以修改后的状态保持打开。可选地，可以使用 `MBeautify.formatCurrentEditorPage(true)` 语法来保存格式化后的文件。
-   对 MATLAB 编辑器当前活动页面中选定的文本进行格式化。命令：`MBeautify.formatEditorSelection()`。与上述相同，此处也存在可选的保存机制。这在处理大文件时很有用，但无论如何都建议使用 `MBeautify.formatCurrentEditorPage()`。
-   对文件进行格式化。命令：`MBeautify.formatFile(file)`。可以（1）使用一个参数：输入文件将被格式化并在 MATLAB 编辑器中以未保存状态保持打开；（2）使用两个参数，如 `MBeautify.formatFile(file, outFile)`：如果可能，格式化后的文件将被保存到指定的输出文件中。输出文件可以与输入文件相同。
-   对目录中的多个文件进行格式化。命令：`MBeautify.formatFiles(directory, fileFilter)`。第一个参数是目录的绝对路径，第二个参数是用于 `dir` 命令的通配符表达式，用于筛选目标目录中的文件。文件将在原地（被覆盖）进行格式化。

### 快捷方式

可以为上述前三项创建快捷方式，这些快捷方式按钮将出现在 MATLAB 主窗口的“Shortcuts”选项卡下（MATLAB R2019 以下版本），以及“Favourites”和“Quick Access Toolbar”上（MATLAB R2019 及以上版本）。

要创建这些按钮，可以使用以下命令：

-   `MBeautify.createShortcut('editorpage')`: 为 `MBeautify.formatCurrentEditorPage()`创建快捷方式
-   `MBeautify.createShortcut('editorselection')`: 为 `MBeautify.formatEditorSelection()`创建快捷方式
-   `MBeautify.createShortcut('file')`: 为 `MBeautify.formatFile(sourceFile, destFile)`创建快捷方式

这些快捷方式还会将 MBeautifier 的根目录添加到 MATLAB 路径中，因此下次打开新的 MATLAB 实例时，无需准备 MATLAB 路径即可使用 MBeautifier。

支持的 MATLAB 版本

---

用于测试 MBeautifier 的最老版本的 MATLAB 是 R2013b。

计划中的未来版本

---

计划在 MATLAB 自带具有类似功能的代码格式化工具之前，维护该项目。

计划使 MBeautifier 也可在 Octave 中使用，通过启动一个新的使用 Java/Kotlin（版本 2._）的开发分支来实现。基于 MATLAB 的分支将在分支版本（1._）中开发。
