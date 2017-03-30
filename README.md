## The AWK Programming Language

### Ch1 AN AWK Tutorial

### 持续行动第一天

- patterns + actions = awk program 因为patterns和actions不一定都存在（但不能同时缺省），所以action需要用{}来区分两者
- awk 'program' 如果后面什么都不接，回车之后，awk 'program' 会作用在你输入的每一行上，这样能快速地检验你写的awk程序
- awk的program是用单引号括起来的，这样做的好处是，$符号不会被shell解析，同时也能使输入的长度超过一行
- awk -f progfile optional list of input files，当你的awk程序特别长的时候，最好是写到单独的文件里，用这种方式来执行。 当把awk程序写到文件里时，pattern + action 不能加单引号。单引号只是为了在命令行里输入
- awk只有两种数据类型，数字和字符串
- awk每次读取一行，然后将其分割成多个域，默认情况下，一个域是一个不包含空格或者tab的字符串； \$1\$2\$3...分别代表第一个域，第二个域，第三个域，以此类推。\$0代表整个一行；每一行的域的个数由这行的内容决定
- print后面接表达式，如果多个表达式用逗号隔开，输出时，默认是中间加一个空格输出。print输出每一行时，默认是带换行符。这些默认设置可以被修改
- NF是内置的变量，number of fields of current line， \$NF表示最后一个field
- NR是另外一个内置变量，表示当前是第几行
- 可以在输出中加入除fieds之外的字符串 '{ print "any string", $1, "is", \$2 * \$3}'
- print是简易版本的输出打印工具，如果想要复杂的输出格式，需要使用printf
- patterns可以写成组合形式，涉及到 && || ! ，逻辑运算 与 或  非
- 字符串拼接: { names = names $1 " " }


### 持续行动第二天

- BEGIN 和 END都属于特殊的pattern，BEGIN在第一个输入文件的第一行读入之前匹配，END是在最后一个文件的最后一行处理完后匹配
- 在Awk中定义的数字类型的变量，初始值为0；字符串类型的变量，初始值为null
- while循环；for循环
- Array

### 持续行动第三天

## Ch2 THE AWK LANGUAGE

### 持续行动第四天

- 对于 pattern + action组合，缺省的pattern会匹配每一行；如果 pattern + action中的action缺省，那么匹配到pattern的一行会被打印出来

- 程序的格式：action开始的'{'必须和pattern在同一行，action剩下的部分以及结尾的'}'可以在接下来的几行上。为了增加可读性，在语句前或语句后可以插入空行，同样，可以在操作符或者操作数前或后加入空格或tab。注释要放到每一行的最后面，以'#'开头。一个比较长的语句，可以用反斜杠 + 换行 折开

  { print \\

  ​	    $1,         # country name

  ​	    $2,         # area in thousands of square miles

  ​	    $3  }	  #  population in millions

- 对于短的程序，可能format不是很重要，但是对于比较大型的程序，一致性(consistency)和可读性(readability)对于程序的管理和维护很重要

- Patterns，此处总共分为六个类别

  - BEGIN { statements } : The statements are executed once before any input has been read

  - END { statements } : The statements are executed once after all input has been read

  - expression { statements } : The statements are executed at each input line where the expression is ture, that is, nonzero or nonnull.

  - /regular expression/ { statements } : The statements are executed at each input line that contains a string matched by the regular expression.

  - compound pattern { statements } : A compound pattern combines expressions with && , || , ! , and parentheses; the statements are excuted at each input line where the compound pattern is true.

  - pattern1, pattern2 { statements } : A range pattern matches each input line from a line matched by pattern1 to the next line matched by pattern2, inclusive; the statements are executed at each matching line.

    注解：BEGIN 和 END 不能和其它的pattern组合；range pattern不能作为任何其它pattern的一部分；BEGIN 和 END 是唯二的两个必须后面接action的pattern

  下面是对这六类pattern的详细说明：

  - BEGIN和END不会去匹配输入，BEGIN对应的action是在读入第一个文件的第一行之前执行，END对应的action是在所有文件的所有输入被处理后，再执行；所以BEGIN很适合做初始化的工作；END很适合做善后的工作。当有多个BEGIN 和 多个END时，按程序的顺序依次执行；虽然BEGIN和END的先后顺序没有强制要求，但一般是BEGIN放在最开头，END放在结尾。 BEGIN一个比较常见的用法是用来修改输入行的切分方式，域的切分符是由内置的变量FS控制的，默认情况下，域会按照 空格 和 tab 来切，此时，FS被设置成blank。
  - 表达式作为pattern：awk支持字符串间的操作。任何表达式都可以作为任意操作符的操作数，当某个操作符需要数字类型的操作数，但表达式为字符串类型时，表达式的值自动转成数字类型；反之亦然。任何表达式都可以被用来作为pattern，对于当前输入行来说，如果表达式的值非零(数值类型)或者非空(字符串类型)时，则表示这一行与pattern匹配。典型的表达式模型是数字或者字符串间的比较，比较表达式涉及到六种关系操作符，以及两种字符串匹配操作符(～，！～)。在关系运算种，如果两个操作数均为数值型，那么将进行数值型比较；否则，任何一个数字型操作数将转成字符串型。字符串比较时，依次比较对应的字符，比较的规则按照机器提供的字符顺序，一般是ASCII顺序。按照这种排序规则，当字符串A比字符串B先出现时，就说A小于B。“Canada” < "China"; "Asia" < "Asian". $0 >= "M"将选出以M，N，O...开头的每一行
  - 字符串匹配Pattern (String-Matching Patterns)。Awk提供正则表达式，字符串匹配Pattern检验一个字符串是否包含某个正则表达式匹配上的子串。(A string-matching pattern tests whether a string contains a substring matched by a regular expression)。最简单的正则表达式是一串字母和数字，用来匹配它本身。将一个正则表达式变成一个字符串匹配pattern的方法是用‘//’把正则表达式包起来，需要注意的是正则表达是对空格敏感，/Asia/ 和 / Asia /匹配效果是不同的。总共有三种类型的String-Matching Pattern，/regexpr/, expression ~ /regexpr/, expression !~ /regexpr/; /regexpr/实际上等价于 $0 ~ /regexpr/
    - 此处穿插正则表达式的概念：
      - 正则表达式的元字符包括：'\' '^' '$' '.' '[' ']' '|' '(' ')' '*' '+' '?'
  - 复合pattern, 此pattern使用括号和逻辑运算符 || && ! 来复合其它pattern，当表达式真时，匹配当前行成功。&& 和 || 从左往右判断操作数，一旦能确定整个表达式的真假，立即停止计算
  - Range pattern
    - 匹配从第一行匹配到pattern1开始，知道第一个匹配到pattern2的行结束，如果没有发现能匹配到pattern2的行，则从第一个匹配到pat1开始，一直匹配到输入的最后一行
    - 疑问：awk '/North America/, /Asia/' countries为什么不只匹配前两行，而是匹配了八行，难道不是第一次遇到匹配到pat2的行就停止吗？
    - 例子： FNR == 1, FNR == 5 { print FILENAME ":  " $0 } 输出每个文件的强五行，加上这个文件的文件名作为前缀。(注：FNR指对应当前文件的第几行；FILENAME指当前文件的文件名，二者都是内置变量)

### 持续行动第五天

- 检验字符串是否和正则表达式匹配的方法： 在命令行输入 awk '\$1 ~ \$2' 回车，然后输入一个字符串接一个正则表达式，看是否会返回
### 持续行动第六天

- 内置的字符串函数：
  - index(s,t) ： 返回t出现在s中的最左端位置，当t不在s中出现时，返回0。字符串中第一个字符的位置是1
  - match(s,r) ： 找到字符串s中与正则表达式匹配的从左端起最长的子串，并返回索引。如果没有匹配的子串，则返回0；同时，将这个索引值赋给内置的变量**RSTART**；把匹配到的子串赋给内置变量**RLENGTH**
  - split(s,a,fs) ： 将字符串s按照分隔符fs分隔，存到数组a中，返回域的个数
  - sprintf(format, expr1, expr2, ..., exprn) ： 将表达式exprn按照format的格式连起来，作为返回值
  - sub 和 gsub 来自Unix下的文本编辑器ed的替换命令
    - sub(r,s,t) ： 首先在目标操作字符串t中找到从最左端开始匹配到正则表达式r的最长的子串，然后用字符串s替换。和编辑器ed下一样，"leftmost longest" means that "the leftmost match is found first, then extended as far as possible"。 ex: 对于"banana" (an)+ 的 "leftmost longest match" 是 "anan";  而(an)* 的 "leftmost longest match" 是 字符'b'之前的空串null
      - sub的返回值是替换次数；sub(r,s) = sub(r,s,$0)
    - gsub(r,s,t) ： 和sub(r,s,t)类似，除了一点，即，successively replaces the leftmost longest nonoverlapping (不明白) substrings matched by r with s in t;
    - 无论sub(r,s,t)还是gsub(r,s,t)，s中出现的符号&都会被匹配到的子串替代；可以用\&关掉此功能
    - substr(s,p) ： 返回从p处开始的子串；substr(s,p,n) 返回前n个从p处开始的子串
- 数字还是字符串？
  - 两个习惯语法强制转换两种数据类型
    - number "" 在数字后面拼接一个null串，强制把数字转成字符串
    - string + 0  字符串加上0，强制把字符串转成数字
    - 因此，当希望以字符类型比较两个field时，$1 ""  ==  \$2 ; 当希望以数字类型比较两个field时，\$1 + 0 == \$2 + 0 。内置变量 OFMT 控制 字符变量和数字变量之间转换时有关格式的规则

### 持续行动第七天

- Control Flow Statement
  - next: causes awk to fetch the next input line and begin matching patterns starting from the first pattern-action statement
  - exit: In an END action, the exit statement causes the program to terminate; In any other action, it causes the program to behave as if the end of the input had occured; no more input is read, and the END actions, if any, are excuted. If an exit statement contains an expression (exit expr), it causes awk to return the value of expr as its exit status unless overridden by a subsequent error or exit. If there is no expr, the exit status is zero.

- Arrays
- Array的索引是字符串，因此arrays in awk are called "associative arrays"
- 判断某个索引在array中是否存在 : subscript in A；A[subscript]存在时返回1，否则返回0。不要使用if (pop["Africa"] != " ")判断，这样会创建pop["Africa"]
- 删除数组元素：del  array[subscript]
- split(str, arr, fs) 函数返回值是field的个数。
- 当使用数字作为索引时，是反直觉的，10作为字符类型，会出现在字符类型的2的前面

### 持续行动第八天
- 持续行动果然好踏马难啊。。。
