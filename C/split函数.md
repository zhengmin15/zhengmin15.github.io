C++标准库里面没有字符分割函数split ，但是在日常开发过程中，字符分割的场景经常有，存几个常用方法，具体如下：
方法一: **利用STL实现split 函数**
原型: vector<string> split(const string &s, const string &seperator);
可实现的功能为：输入一个包含多个分隔符的字符串，返回一个字符串向量。
代码如下展示：
```cpp
#include <vector>
#include <string>
#include <iostream>
using namespace std;

vector<string> split(const string &s, const string &seperator)
{
    vector<string> result;
    typedef string::size_type string_size;
    string_size i = 0;

    while(i != s.size())
    {
        //找到字符串中首个不等于分隔符的字母；
        int flag = 0;
        while(i != s.size() && flag == 0)
        {
            flag = 1;
            for(string_size x = 0; x < seperator.size(); ++x)
            {
                if(s[i] == seperator[x])
                {
                    ++i;
                    flag = 0;
                    break;
                }
            }
        }

        //找到又一个分隔符，将两个分隔符之间的字符串取出；
        flag = 0;
        string_size j = i;
        while(j != s.size() && flag == 0)
        {
            for(string_size x = 0; x < seperator.size(); ++x)
            {
                if(s[j] == seperator[x])
                {
                    flag = 1;
                    break;
                }
            }
            if(flag == 0)
                ++j;
        }
        if(i != j)
        {
            result.push_back(s.substr(i, j - i));
            i = j;
        }
    }
    return result;
}

int main()
{
    string s = "a,b*c*d,/e&b&&";
    vector<string> v = split(s, ",*/&"); //可按多个字符来分隔;
    for (vector<string>::size_type i = 0; i != v.size(); ++i)
        cout << v[i] << " ";
    cout << endl;
    //输出: a b c d e b 
}
```
另一种高效的实现方式，代码如下：
```cpp
#include <vector>
#include <string>
#include <iostream>
using namespace std;

/*
    s表示原始字符串，可包含多个分隔符
    v表示拆分后的字符串
    c表示分隔符字符串
    注意：这种方式不支持按多种不同间隔符拆分，必须完全匹配间隔符字符串c
*/
void SplitString(const std::string &s, std::vector<std::string> &v, const std::string &c)
{
    std::string::size_type pos1, pos2;
    pos2 = s.find(c);
    pos1 = 0;
    while (std::string::npos != pos2)
    {
        v.push_back(s.substr(pos1, pos2 - pos1));

        pos1 = pos2 + c.size();
        pos2 = s.find(c, pos1);
    }
    if (pos1 != s.length())
        v.push_back(s.substr(pos1));
}

int main()
{
    vector<string> v={0}; 
    SplitString("*a,b*c*d", v, "*");
    for (vector<string>::size_type i = 0; i != v.size(); ++i)
        cout << v[i] << " ";
    cout << endl;
    //输出: a,b c d
}
```
