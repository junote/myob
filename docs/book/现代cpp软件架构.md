
### RAII

RAII不仅仅用于管理内存，你还可以使 用它轻松地处理资源的所有权，例如锁、文件句柄、数据库连接，以及在RAII包装器超出作用域后应该释放的其他对象。
```
struct Resource;
/ / C API
Resource* acquireResource () ;
void releaseResource (Resource *resource) ;
/ / C++ API
using ResourceRaii = std: : unique_ptr<Resource, decltype (&releaseResource)>;
ResourceRaii acquireResourceRaii () ;

```
### C++容器接口

#### array
wraps over fixed size static array.

#### vector
Automatically resizable dynamic array.
#### deque
**D**ouble **E**nded *QUE*ue

#### list
Implementation of Doubly Linked List data structure.

#### forward_list
Singly Linked List data structure.
#### set
Collection of unique elements sorted on the basis of their values
#### map
Collection of key-value pairs sorted on the basis of the keys where no two pairs have same keys.
#### multiset
Collection of elements sorted on the basis of their values but allows multiple copies of values.
#### multimap
Collection of key-value pairs sorted on the basis of the keys where multiple pairs can have same keys.
#### unordered_set
Collection of unique elements hashed by their values.
#### unordered_map
Collection of key-value pairs that are hashed by their keys where no two pairs have same keys.
#### unordered_multiset
Collection of elements hashed by their values and allows multiple copies of values.
#### unordered_multimap
Collection of key-value pairs that are hashed by their keys where multiple pairs can have same keys.
#### stack
Adapts a container to provide stack (LIFO) data structure.
#### queue
Adapts a container to provide queue (FIFO) data structure
#### priority_queue
Adapts a container to provide heap data structure.

### 接口中使用指针

```
void A(Resource*)
void B(Resource&)
void C(std::unique_ptr<Resource>)
void A(std::unique_ptr<Resource>&)
void A(std::shared_ptr<Resource>)
void A(std::shared_ptr<Resource>&)

```
- A和B应该留给简单的参数传递，如果不对所传递对象的所有权做任何处理，则不应该使用它们
- 如果想操作指针本身，那么在进行参数传递时应该只使用智能指针，
- A应该只用于一个单一的资源，如果你知道要传递的对象不为空(null)，那么最好通过引用（例如const引用）来传递它
- C函数按值传递unique_ptr。这意味着它是一个资源接收器。换句话说，它会消耗并释放该资源。
- 只有当你想传入包含一个资源的unique_ptr，并在该unique_ptr中接收另一个资源并传出时，才应该使用D函数。
- E函数用于与被调用者共享资源所有权
- F函数与D函数类似，仅在要操作shared_ptr实例并通过此参数传入/传出变更时使用

### 使用内联命名空间
```
#ifdef NDEBUG
inline namespace release {
#else
inline namespace debug {
#endif
struct EasilyDebuggable {
/ / ...
#ifndef NDEBUG
/ / fields helping with debugging
#endif
} ;
} // end namespace

```
代码使用了内联命名空间，所以当你声明该类的对象时，用户不会看到两种构建类型之间的差异：内联命名空间的所有声明在所在的作用域中均可见



### std::optional

- 可选函数参数 `void calculate(std:optional<int> param);`
- 可选函数返回值 `std::optional<int> try_parse(std::string_veiw maybe_num);`
- 可选类成员 `struct UserProfile{ std::string name; std::optional<std::string> address};`

### 声明式编码风格


```c++
auto temperatures = std::vector<double>{ -3 ., 2 ., 0 ., 8 ., - 10 ., - 7. };

for (std::size_t i = 0; i < temperatures.size () - 1; ++i) {
	for (std::size_t j = i + 1; j < temperatures.size () ; ++j) {
		if (std::abs (temperatures [i] - temperatures [ j] ) > 5)
			return std::optional{i} ;
}
return std::nullopt;

```

``` c++
using namespace std::ranges;
auto is_even = [] (auto x) { return x % 2 == 0; } ;
auto to_string = [] (auto x) { return std :: to_string (x); };
auto my_range = views::iota (1)
| views::filter (is_even)
| views::take (2)
| views::reverse
| views::transform (to_string) ;
std::cout << std::accumulate (begin (my_range), end (my_range), ""s) << '\n';

```

