#### 获取 Unix 时间戳（自 1970 年 1 月 1 日以来的秒数）
```
#include <iostream> 
#include <chrono> 

int main() 
{ 
// 获取当前时间点 
auto now = std::chrono::system_clock::now(); 
// 转换为时间戳（秒） 
auto timestamp = std::chrono::system_clock::to_time_t(now); 
std::cout << "Unix Timestamp (seconds): " << timestamp << std::endl; return 0; 
}
```
#### 获取高精度时间戳（毫秒、微秒或纳秒）

```
#include <iostream> 
#include <chrono> 

int main() 
{ 
// 获取当前时间点（高精度） 
auto now = std::chrono::high_resolution_clock::now(); 
// 转换为毫秒 
auto ms = std::chrono::time_point_cast<std::chrono::milliseconds>(now); 
auto epoch = ms.time_since_epoch(); 
auto value = std::chrono::duration_cast<std::chrono::milliseconds>(epoch); std::cout << "Timestamp (milliseconds): " << value.count() << std::endl;
return 0; 
}
```