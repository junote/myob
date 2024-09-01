

```
//转换float数据到字节数组  
unsigned char i;  
float floatVariable;  
unsigned char charArray[4];  
(unsigned char) *pdata = ((unsigned char)*)&floatVariable;  //把float类型的指针强制转换为unsigned char型  
for(i=0;i<4;i++)  
{  
    charArray[i] = *pdata++;//把相应地址中的数据保存到unsigned char数组中       
}  
  
//转换字节数组到float数据  
float   floatVariable;  
unsigned char  i;   
void   *pf;       
pf = &floatVariable;   
(unsigned char) * px = charArray;    
  
for(i=0;i<4;i++)  
{  
    *(((unsigned char)*)pf+i)=*(px+i);       
}
```