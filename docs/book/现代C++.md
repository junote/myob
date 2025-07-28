### int

![[Pasted image 20250405214258.png]]

![[Pasted image 20250405214623.png]]


### char
❑ char：默认类型，总是1个字节。可能是也可能不是有符号的（例如：ASCII）​。
❑ char16_t：用于2字节的字符集（例如：UTF-16）​。
❑ char32_t：用于4字节的字符集（例如：UTF-32）​。
❑ signed char：与char相同，但保证是有符号的。
❑ unsigned char：与char相同，但保证是无符号的。
❑ wchar_t：足够大以包含实现平台地区环境语言设置中的最大字符（例如：Unicode）​。

![[Pasted image 20250405215017.png]]

### byte

它定义在<cstddef>头文件中。std::byte类型允许按位进行逻辑运算。


### size_t

在<cstddef>头文件中,在实践中，它通常与64位架构系统的unsigned long long相同。
一元运算符sizeof接受一个类型并返回该类型的大小（以字节为单位）​。sizeof运算符总是返回一个size_t对象


### void

void类型表示一个空的值集合。因为void对象不能拥有值，所以C++不允许使用void对象。我们只在特殊情况下使用void，比如用作不返回任何值的函数的返回类型




### type_traits
![[Pasted image 20250406112058.png]]
![[Pasted image 20250406112138.png]]


### attribute
![[Pasted image 20250406113130.png]]


### paremeter_template
![[Pasted image 20250406114230.png]]
![[Pasted image 20250406114325.png]]

### function pointer
![[Pasted image 20250406185421.png]]


### lambada
lambda表达式有五个组件：
❑ 捕获列表(captures)：函数对象的成员变量（即局部应用的参数）​。
❑ 参数(parameters)：调用函数对象所需的参数。
❑ 表达式体(body)：函数对象的代码。
❑ 修饰符(modifiers)：constexpr、mutable、noexcept和[​[noreturn]​]等元素。
❑ 返回类型(return-type)：函数对象返回的类型。

![[Pasted image 20250406190803.png]]

![[Pasted image 20250406192818.png]]


### std::fuction

<functional>头文件中的std::function类模板是可调用对象的多态包装器
![[Pasted image 20250406193228.png]]

### catch
最简单的单元测试框架之一是Phil Nash的Catch，它可从https://github.com/catchorg/Catch2/获得。因为Catch是一个只有头文件的库

![[Pasted image 20250406194728.png]]


### gtest
![[Pasted image 20250406194316.png]]
![[Pasted image 20250406194347.png]]

### boost test

![[Pasted image 20250406194605.png]]

### gmock

###  HippoMocks
一个只有头文件的模拟库
GitHub(https://github.com/dascandy/hippomocks/)

### FakeIt和Trompeloeil
FakeIt（由EranPe’er提供，可在https://github.com/eranpeer/FakeIt/获得
Trompeloeil（由Björn Fahller提供，可在https://github.com/rollbear/trompeloeil/获得

FakeIt类似于HippoMocks，并且它是一个只包含头文件的库。它的不同之处在于它在构建期望时遵循默认记录模式。FakeIt不会预先指定期望值，而是在测试结束 时验证模拟对象的方法是否被正确调用。

Trompeloeil（来自法语trompe-l’œil，意为“欺骗眼睛”​）可以被视为Google Mock的现代替代品


### uniq_ptr

![[Pasted image 20250406200738.png]]
![[Pasted image 20250406201013.png]]

![[Pasted image 20250406200755.png]]
![[Pasted image 20250406200814.png]]


### shared_ptr
我们为指向类型指定一个模板参数int❶。在第一个参数中，分配并初始化一个int对象❷。接着是一个自定义删除器❸。作为第三个参数，我们传入一个std::allocator❹。
![[Pasted image 20250406201426.png]]

![[Pasted image 20250406201605.png]]


### weak_ptr
弱指针是一种特殊的智能指针，它对所引用的对象的所有权。弱指针允许跟踪对象并仅在被跟踪对象仍然存在时将弱指针转换为共享指针。

弱指针通过调用它们的lock方法来获得其跟踪对象的临时所有权。lock方法总是创建一个共享指针

![[Pasted image 20250406201903.png]]
![[Pasted image 20250406201912.png]]

### optional
optional是一个类模板，它包含一个可能存在也可能不存在的值。
<optional>头文件中提供了std::optional，

![[Pasted image 20250406202619.png]]

### pair

pair是一个类模板，一个pair对象中可包含两个不同类型的对象。对象是有序的，我们可以通过成员first和second访问它们
stdlib的<utility>头文件中有std::pair
![[Pasted image 20250406202917.png]]

### tuple

tuple（元组）是一个类模板，包含任意数量的不同类型元素,需要我们使用非成员函数模板get来提取元素。

stdlib的<tuple>头文件中有std::tuple和std::get，

![[Pasted image 20250406203127.png]]
![[Pasted image 20250406203143.png]]

### any

any是存储任意类型的单个值的类。它不是类模板。要将any转换为具体类型，请使用any强制转换(cast)，它是一个非成员函数模板。任何强制转换

stdlib的<any>头文件中有std::any

![[Pasted image 20250406203431.png]]

### variant

variant（变体）是存储单个值的类模板，值的类型仅限于作为模板参数提供的用户自定义列表。
variant要求显式枚举将要存储的所有类型。
stdlib的<variant>头文件中有std::variant

![[Pasted image 20250406203802.png]]


### chrono

stdlib Chrono库在<chrono>头文件中提供了各种时钟

Chrono库中提供了三个时钟；每个都提供不同的保证，并且都位于std::chrono命名空间中：
❑ std::chrono::system_clock是系统范围的实时时钟。它有时也称为挂钟(wall clock)，即自特定实现开始日期以来经过的实时时间。大多数实现都将Unix的开始日期指定为1970年1月1日午夜。
❑ std::chrono::steady_clock保证它的值永远不会减少。这似乎很荒谬，但时间测量比看起来的更复杂。例如，系统可能不得不应对闰秒或不准确的时钟。
❑ std::chrono::high_resolution_clock具有最短的可用tick周期：tick是时钟可以测量的最小原子改变。

Chrono使用std::chrono::time_point类型对时间点进行编码。
std::chrono::duration表示两个time_point对象之间的时间。
std::literals::chrono_literals命名空间，因此可以访问持续时间字面量
std::chrono::duration_cast以将持续时间从一种单位转换为另一种单位
stdlib在<thread>头文件中提供了并发原语，其中包含非成员函数std::this_thread::sleep_for


### Numerics
![[Pasted image 20250406204943.png]]
![[Pasted image 20250406205021.png]]

stdlib在<complex>头文件中提供了std::complex类模板。
![[Pasted image 20250406205128.png]]

stdlib在<limits>头文件中提供了类模板std::numeric_limits
![[Pasted image 20250406205432.png]]

stdlib<ratio>头文件中的std::ratio是一个类模板，使用它能够在编译时进行有理数计算
![[Pasted image 20250406205713.png]]


### array

array<T, S>类模板接受两个模板参数：❑ 包含的类型T。❑ 数组S的固定大小。
❑ operator[​]。❑ at。❑ get。
![[Pasted image 20250406210849.png]]
![[Pasted image 20250406210922.png]]

### vector 
STL的<vector>头文件中的std::vector是一个顺序容器，可以保存动态大小的、连续的一系列元素。

![[Pasted image 20250406211543.png]]
![[Pasted image 20250406211556.png]]

### deque

双端队列（deque，读作“deck”​）是一个顺序容器，具有从前后快速插入和删除的操作

STL实现的std::deque可从<deque>头文件中获得。
双端队列的内存通常是分散的，就像向量和列表(list)的混合体

![[Pasted image 20250406212339.png]]

### list

列表(list)是一个顺序容器，它可在任意位置快速插入元素或删除元素，但无法随机访问元素。STL实现std::list可从<list>头文件中获得。

![[Pasted image 20250406212441.png]]

STL还在<forward_list>头文件中提供了一个std::forward_list，它是一个单向链表，只允许在一个方向上进行迭代

### stack
最后被压入栈的元素是第一个被弹出的元素。
STL在<stack>头文件中提供了std::stack

![[Pasted image 20250406212701.png]]

### queue
STL在<queue>头文件中提供了std::queue

队列是先进先出的。当将一个元素推入队列时，其实是将元素插入队列的末端。当从队列中弹出一个元素时，其实是将元素从队列的开头移除

### heap

优先级队列(priority queue)也称为堆(heap)，是一种支持push和pop操作的数据结构，可以根据用户指定的比较器对象保持元素有序.priority_queue根据比较器比较结果决定弹出元素。

STL在<queue>头文件中提供了std::priority_queue

![[Pasted image 20250406213247.png]]
![[Pasted image 20250406213257.png]]

### bitset
bitset（位集）是一种存储固定大小的位(bit)序列的数据结构。我们可以操纵每一位。

STL在<bitset>头文件中提供了std::bitset。
![[Pasted image 20250406213440.png]]

### set
STL的<set>头文件中可用的std::set是一个关联容器，这种容器包含称为键(key)的已排序的唯一元素。
![[Pasted image 20250406214135.png]]
![[Pasted image 20250406214154.png]]

#### multiset
STL的<set>头文件中可用的std::multiset是一个关联容器，这种容器包含已排序的非唯一键。multiset支持与普通集合相同的操作，

multiset的count方法可以告诉我们有多少元素与给定键匹配。
equal_range可以返回包含多个元素的半开半闭区间

#### unordered_set
<unordered_set>头文件中可用的std::unordered_set也是一个关联容器，这种容器包含未排序的唯一键, unordered_set通常实现为哈希表

STL在<functional>头文件中提供了哈希器类模板std::hash<T>，其中包含基本类型、枚举类型、指针类型、optional、variant、智能指针等的特化。

#### unordered_multiset
unordered_multiset支持与unordered_set相同的构造函数和操作，但它会存储冗余元素。

### map

类模板map<Key, Value, Comparator, Allocator>接受四个模板参数。第一个是键类型Key，第二个是值类型Value，第三个是比较器类型（默认为std::less）​，第四个是分配器类型（默认为std::allocator<T>）​。

![[Pasted image 20250406215207.png]]
![[Pasted image 20250406215302.png]]

#### multimap
multimap不支持map支持的关联数组功能，也就是说，不支持operator[​]和at。与multiset一样，multimap主要通过equal_range方法提供元素访问功能

#### unordered_map
std::unordered_map和std::unordered_multimap都在STL的<unordered_map>头文件中声明。这些关联容器通常像对应的集合一样使用红黑树。它们也需要哈希函数和相等函数，并且支持桶接口。

#### unordered_multimap

### initializer_list
STL的<initializer_list>头文件中的std::initializer_list容器来实现

### 迭代器

![[Pasted image 20250406220304.png]]
![[Pasted image 20250406220313.png]]

std::advance辅助函数允许按所需的数量递增或递减。
std::next和std::prev这两个迭代器辅助函数是计算从给定迭代器出发的偏移量的函数模板。
std::distance迭代器辅助函数可以计算两个输入迭代器itr1和itr2之间的距离
std::iter_swap迭代器辅助函数允许交换两个前向迭代器itr1和itr2所指向的值



### string

❑ char的std::string用于ASCII之类的字符集。
❑ wchar_t的std::wstring足够大，可以包含实现语言环境的最大字符。
❑ char16_t的std::u16string用于UTF-16之类的字符集。
❑ char32_t的std::u32string用于UTF-32之类的字符集。

![[Pasted image 20250406221033.png]]
![[Pasted image 20250406221047.png]]

![[Pasted image 20250406221116.png]]
![[Pasted image 20250406221139.png]]

![[Pasted image 20250406221309.png]]
![[Pasted image 20250406221324.png]]

![[Pasted image 20250406221351.png]]

![[Pasted image 20250406221441.png]]

#### string_view
字符串视图(string view)是一个对象，它表示一个恒定的、连续的字符序列。
虽然string_view也支持复制构造和复制赋值，但它不支持移动构造和移动赋值。

#### 正则表达式(regular expression

![[Pasted image 20250406221741.png]]
![[Pasted image 20250406221808.png]]

STL的<regex>头文件中的std::basic_regex类模板表示从模式构造的正则表达式。
![[Pasted image 20250406221923.png]]


### stream

![[Pasted image 20250407174040.png]]


### filesystem

#### path
std::filesystem::path是文件系统库中用于构造路径的类，包含在<filesystem>头文件
![[Pasted image 20250407174901.png]]
![[Pasted image 20250407174914.png]]
![[Pasted image 20250407175043.png]]

### algorithm

#### 非修改序列操作
- all_of算法确定序列中的每个元素是否符合用户指定的标准
- any_of算法确定序列中是否有元素符合用户指定的标准。
- none_of算法确定序列中是否没有元素符合用户指定的标准。
- for_each算法将用户定义的函数应用于序列中的每个元素。
- for_each_n算法将用户定义的函数应用于序列中的每个元素。
- find、find_if和find_if_not算法在序列中寻找与用户定义的标准相匹配的第一个元素。
- find_end算法查找子序列最后一次出现的位置。
- find_first_of算法寻找序列1中第一次出现的等于序列2中的某个元素的元素。
- adjacent_find算法寻找序列中的第一对相邻重复元素.
- count算法对序列中匹配某些用户定义的标准的元素进行计数。
- mismatch算法在两个序列中寻找第一个不匹配的子序列。
- equal算法确定两个序列是否相等。
- is_permutation算法确定两个序列是否互为排列，排列意味着它们包含相同的元素但可能顺序不同。
- search算法可以定位子序列。该算法在序列1中定位序列2。
- search_n算法定位包含相同连续值的子序列。该算法在序列中搜索count个连续值并返回一个指向第一个值的迭代器

#### 可变序列操作
- copy算法将一个序列复制到另一个序列。
- copy_n算法将一个序列复制到另一个序列。该算法将目标序列复制到result中并返回接收序列的结束迭代器。
- copy_backward算法将一个序列反向复制到另一个序列中。
- move算法将一个序列移动到另一个序列中。
- move_backward算法将一个序列反向移动到另一个序列中。
- swap_ranges算法将元素从一个序列交换到另一个序列。
- transform算法修改一个序列的元素并将它们写入另一个序列。
- replace算法用一些新的元素替换序列中的某些元素。
- fill算法用一些值填充序列。
- generate算法通过调用函数对象来填充序列。
- remove算法从一个序列中删除某些元素。
- unique算法从序列中去除多余的元素。
- reverse算法是将序列的顺序颠倒过来
- sample（采样）算法产生随机的、稳定的子序列。
- shuffle算法产生随机排列。

#### 排序
- sort（排序）算法对序列进行排序
- stable_sort算法对序列进行稳定的排序。
- partial_sort算法将序列分为两组。
- is_sorted算法判断序列是否有序。
- nth_element算法将序列中的特定元素放入其正确的排序位置。

#### 搜索
- lower_bound算法在已排序的序列中查找使其分区的元素。
- upper_bound算法在有序序列中查找使其分区的元素。
- equal_range算法在有序序列中查找特定元素的区间。
- binary_search算法在有序序列中查找特定元素。

#### partition
一个分区的序列包含两个连续的、不同的元素组。这些组的元素不重合，第二个组的第一个元素被称为分区点

- is_partitioned算法确定序列是否已分区。
- 一个分区的序列包含两个连续的、不同的元素组。这些组的元素不重合，第二个组的第一个元素被称为分区点
- partition_copy算法对序列进行分区。该算法通过对每个元素进行pred评估来划分目标序列。
- stable_partition算法对序列进行稳定分区。


#### merge
合并算法可以合并两个排序的目标序列，使得输出序列包含两个目标序列的副本并且也是有序的。

- merge算法合并两个排序的序列。该算法将两个目标序列复制到一个输出序列中。

#### 极值
- min或max算法可以确定序列的极值。
- min_element或max_element算法确定序列的极值。
- clamp算法对一个值进行限定。该算法使用operator或comp来确定obj是否在从low到high的范围内。

#### 数值运算
❑ plus<T>实现了加法x+y。
❑ minus<T>实现了减法x-y。
❑ multiplies<T>实现了乘法x*y。
❑ divides<T>实现了除法x/y。
❑ modulus<T>实现了模运算x%y。

- iota算法用增量值填充序列。该算法将从start开始的增量值赋值给目标序列。
- accumulate算法折叠序列,该算法将op应用于start和目标序列的第一个元素。它获取结果和目标序列的下一个元素，并再次应用op，不断以这种方式进行，直到它访问了目标序列中的每个元素。
- reduce算法也折叠序列
- inner_product算法计算两个序列的内积。
- adjacent_difference算法生成邻差（即相邻元素的差）​。
- partial_sum算法生成部分元素的和。


#### thread

##### async
![[Pasted image 20250407200437.png]]

互斥锁(mutex)是一种防止多个线程同时访问资源的机制
<mutex>头文件公开了几个互斥锁选项：
❑ std::mutex提供了基本的互斥锁。
❑ std::timed_mutex提供了带有超时机制的互斥锁。
❑ std::recursive_mutex提供了互斥锁，允许同一线程进行递归锁定。
❑ std::recursive_timed_mutex提供了互斥锁，允许同一线程进行递归锁定并有超时机制。

<shared_mutex>头文件提供了两个额外的选项：
❑ std::shared_mutex提供了共享互斥锁，这意味着几个线程可以同时拥有该互斥锁。
❑ std::shared_timed_mutex提供了共享互斥锁，并实现了有超时机制的锁定。

❑ std::lock_guard是一个不可复制、不可移动的RAII包装器，它在构造函数中接受一个互斥锁对象，在那里调用lock。然后在析构函数中调用unlock。
❑ std::scoped_lock是一个避免死锁的RAII包装器，用于多个互斥锁。
❑ std::unique_lock实现了一个可移动的互斥锁所有权包装器。
❑ std::shared_lock实现了一个可移动的共享互斥锁所有权包装器。


使用<atomic>头文件中的std::atomic类模板，
![[Pasted image 20250407200819.png]]
![[Pasted image 20250407200835.png]]
![[Pasted image 20250407200934.png]]

条件变量(condition variable)是一个同步原语，它可以阻塞多个线程，直到收到通知。另一个线程可以通知该条件变量。在收到通知之后，条件变量可以解除对多个线程的锁定，从而使它们继续执行。
![[Pasted image 20250407201035.png]]

- std::this_thread::yield函数不接受任何参数，返回void。yield的具体行为取决于环境，但一般来说，它提供了一个提示，即操作系统应该给其他线程一个运行的机会。
- std::this_thread::get_id函数不接受任何参数，返回一个s t d::t h r e a d::i d类型的对象，它是一个轻量级线程，
- std::this_thread::sleep_for函数接受一个std::chrono::duration参数，阻止当前线程的执行，至少直到指定的duration过去，然后返回void。
- std::this_thread::sleep_until接受一个std::chrono::time_point并返回void。它完全类似于sleep_for，只是它至少要阻塞线程到指定的time_point。

#### asio
Asynchronous IO

#### system

- 使用<cstdlib>头文件中的std::system函数启动一个单独的进程，该函数接受与要执行的命令相对应的C风格字符串，并返回与命令的返回码相对应的int。
- cstdlib>头文件包含std::getenv函数，它接受一个与想要查找的环境变量名称相对应的C风格字符串，并返回一个包含相应变量内容的C风格字符串。

❑ SIGTERM代表终止请求。
❑ SIGSEGV代表无效的内存访问。
❑ SIGINT代表外部中断，如键盘中断。
❑ SIGILL代表无效的程序镜像。
❑ SIGABRT代表异常的终止条件，例如std::abort。
❑ SIGFPE代表浮点错误，例如除以零。


