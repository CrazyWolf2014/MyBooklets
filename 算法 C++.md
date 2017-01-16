# 字符串
## 1. 字符串转换
### TEX Quotes [UVA-272]
输入一个多行文本，将其中一对双引号```"text"```的左右部分区分开来，分别用两个backquote``` `` ```和两个单引号```''```表示。在这里没有双引号嵌套的问题。
```cpp
#include <stdio.h>
int main() {
    int c, q = 1;
    while((c = getchar()) != EOF) {       // 用getchar()才能保证能读入空白符
        if(c == '"') { printf("%s", q ? "``" : "''"); q = !q; }
        else printf("%c", c);
    }
    return 0;
}

```
### WERTYU [UVA-10082]
输入多行文本，将里面的字符替换为键盘上该字符的前一个字符。包含数字、大写字母、多个符号。不考虑需要按Shift输入的符号，且去除左上角的backquote，每行键盘的第一个字符及包含文字的Enter、Backspace等控制字符。
**In:**
```O S, GOMR YPFSU/```
**Out:**
```I AM FINE TODAY.```
```cpp
#include <stdio.h>
#include <string.h>
char s[] = "`1234567890-=QWERTYUIOP[]\\ASDFGHJKL;'ZXCVBNM,./";   // 注意'\'是转义字符，需要用两个
int main()
{
    char c[256] = {0};
    int slen = strlen(s);
    for(int i=1; i<slen; ++i) c[s[i]] = s[i-1];     // 构建一个字符查找表

    char p;
    while((p = getchar()) != EOF) {
        if(c[p]) putchar(c[p]);
        else putchar(p);
    }
    return 0;
}
```

### Palindromes [UVA-401]
输入一个只包含大写字母和数字1-9的不超过20个字符的字符串，判断是否是回文串和镜像串。镜像串是指不仅翻转字符的位置，还反转每个字符（比如3翻转后是E，但C翻转后是非法字符）后仍然相等的串（如“3AIAE”）。
其中合法的翻转字符包括：
```
ABCDEFGHIJKLMNOPQRSTUVWXYZ123456789
A   3  HIL JM O   2TUVWXY51SE Z  8
```
**In:**
```
NOTAPALINDROME
ISAPALINILAPASI
2A3MEAS
ATOYOTA
```
**Out:** （注意每行输出多一个空行）
```
NOTAPALINDROME -- is not a palindrome.

ISAPALINILAPASI -- is a regular palindrome.

2A3MEAS -- is a mirrored string.

ATOYOTA -- is a mirrored palindrome.

```
```cpp
#include <stdio.h>
#include <string.h>
const char sa[] = "A   3  HIL JM O   2TUVWXY5";
const char s1[] = "1SE Z  8 ";
const char *msg[] = {" not a palindrome.", " a regular palindrome.",
                    " a mirrored string.", " a mirrored palindrome."};
int main()
{
    char mp[256] = {0};
    for(int i=0; i<26; ++i) mp[i+'A'] = sa[i];
    for(int i=0; i<9; ++i) mp[i+'1'] = s1[i];
    char c[50];
    while(scanf("%s", c) != EOF) {
        int p=1, m=1;
        int clen = strlen(c);
        for(int i=0; i<=clen/2; ++i){ // 注意长度为1的情况
            if(c[i] != c[clen-1-i]) p = 0;
            if(c[i] != mp[c[clen-1-i]]) m = 0;
        }
        printf("%s -- is%s\n\n", c, msg[m*2+p]);
    }
    return 0;
}
```
### Master-Mind Hints [UVA-340]
实现一个猜数字游戏。包含多组输入数据，每组数据包含长度n，n长度的原始序列，多组猜测，以全0结束。整个输入以开头的0结束。n < 1000，序列元素只包含1-9的数字。输出为两个数字，位置正确的元素个数和出现了但位置不正确的元素个数。
**In:**
```
4
1 3 5 5
1 1 2 3
4 3 3 5
6 5 5 1
6 1 3 5
1 3 5 5
0 0 0 0
10
1 2 2 2 4 5 6 6 6 9
1 2 3 4 5 6 7 8 9 1
1 1 2 2 3 3 4 4 5 5
1 2 1 3 1 5 1 6 1 9
1 2 2 5 5 5 6 6 6 7
0 0 0 0 0 0 0 0 0 0
0
```
**Out:**
```
Game 1:
(1,1)
(2,0)
(1,2)
(1,2)
(4,0)
Game 2:
(2,4)
(3,2)
(5,0)
(7,0)
```
```cpp
#include <stdio.h>
#include <string.h>
int main()
{
    int n, a[1005], tmp[1005];
    int cnt = 0;
    while(scanf("%d", &n) != EOF && n){
        printf("Game %d:\n", ++cnt);
        int sum_a[10] = {0};
        for(int i=0; i<n; ++i){
            scanf("%d", &a[i]);
            ++sum_a[a[i]];      // 统计各个字符的出现次数
        }

        while(true){
            int sum_tmp[10] = {0};  // 放在这里，使每次读入一个新序列都会初始化
            int a1=0,a2=0;
            for(int i=0; i<n; ++i){
                scanf("%d", &tmp[i]);
                ++sum_tmp[tmp[i]];
                if(tmp[i] == a[i]) ++a1;
            }
            if(tmp[0] == 0) break;
            for(int i=1; i<10; ++i) a2 += (sum_tmp[i] > sum_a[i])? sum_a[i] : sum_tmp[i];   // 两个序列中共有元素个数

            printf("    (%d,%d)\n", a1, a2-a1);     // 共同拥有的元素个数减去完全匹配的，得到存在但位置不正确的
        }
    }
    return 0;
}
```
### Digit Generator [UVA-1583]
如果x加上x的各个数字之和得到y，就说x是y的生成元。 给出n（1≤n≤100000），求n的最小生成元。 无解输出0。
**In:**
```
3 
216 
121 
2005
```
**Out:**
``` 
198 
0 
1979
```
```cpp
#include <stdio.h>
#include <string.h>
int mp[100100];     // 直接计算所有元素的最小生成元
int main()
{
    for(int i=99999; i>=1; --i){    // 从后往前计算，则小的生成元覆盖大的生成元
        int t = i, x = i;
        while(x > 0) { t += x % 10; x /= 10; }
        mp[t]=i;
    }
    int n,t;
    scanf("%d", &n);
    while(n--){
        scanf("%d", &t);
        printf("%d\n", mp[t]);
    }

    return 0;
}
```
### Circular Sequence [UVA-1584]
对一个长度小于100的字符环状序列，返回其字典序最小的序列。
**In:**
```
2
CGAGTCAGCT
CTCC
```
**Out:**
```
AGCTCGAGTC
CCCT
```
```cpp
#include <stdio.h>
#include <string.h>
int main()
{
    int n;
    scanf("%d", &n);
    char a[210];
    while(n--){
        scanf("%s", &a);
        int alen = strlen(a), b = 0;
        memcpy(&a[alen], &a, alen * sizeof(char)); // 注意此时数组a的最后没有'\0'
        for(int i=1; i<alen; ++i){
            if(memcmp(&a[i], &a[b], alen*sizeof(char)) < 0) b = i;
        }
        a[b+alen] = '\0';
        printf("%s\n", &a[b]);
    }
    return 0;
}
```
### Score [UVA-1585]
给出一个由O和X组成的串（长度为1～80），统计得分。 每个O的得分为目前连续出现的O的个数，X的得分为0。 例如，OOXXOXXOOO的得分为1+2+0+0+1+0+0+1+2+3。
```cpp
#include<stdio.h>
#include <string.h>
char a[100];
int main() {
    int n;
    scanf("%d", &n);
    while(n--){
        scanf("%s", a);
        int cnt = 0, cur = 0, alen = strlen(a);
        for(int i=0; i<alen; ++i){
            if(a[i] == 'X') cur = 0;
            else{
                cnt += ++cur;
            }
        }
        printf("%d\n", cnt);
    }
}
```
### Molar Mass [UVA-1586]
给出一种物质的分子式（不带括号，总长度小于80），求分子量。 本题中的分子式只包含4种原子，分别为C, H, O, N，原子量分别为12.01, 1.008, 16.00, 14.01（单位：g/mol），分子式的数字范围n(2<=n<=99)，n=1时省略。例如，C6H5OH的分子量为94.108g/mol
```cpp
#include <stdio.h>
#include <string.h>
#include <ctype.h>
char a[100];
int main() {
    int n;
    scanf("%d", &n);
    double mp[26] = {0};
    mp['C' - 'A'] = 12.01;
    mp['H' - 'A'] = 1.008;
    mp['O' - 'A'] = 16.00;
    mp['N' - 'A'] = 14.01;
    while(n--){
        scanf("%s", a);
        double cnt = 0;
        int pre = 0, cur = 0, alen = strlen(a);     //pre=0，这样可以让首次计算时mp[pre] = 0
        // 可以先赋值给pre，这样可以避免计算首元素之前的元素
        //int pre = a[0], cur = 0, alen = strlen(a);
        //for(int i=1; i<alen; ++i){
        for(int i=0; i<alen; ++i){
            if(isdigit(a[i])){
                cur *= 10;
                cur += a[i] - '0';
            }
            else{
                cnt += mp[pre] * (cur == 0 ? 1 : cur);  // 计算当前元素之前的元素，注意考虑n=1省略的情况
                cur = 0;
                pre = a[i] - 'A';
            }
        }
        cnt += mp[pre] * (cur == 0 ? 1 : cur);  // 结束后计算最后一个元素
        printf("%.3f\n", cnt);
    }
}
```
### Periodic Strings [UVA-455]
如果一个字符串可以由某个长度为k的字符串重复多次得到，则称该串以k为周期。 例如，abcabcabcabc以3为周期（注意，它也以6和12为周期）。
输入一个长度不超过80的字符串，输出其最小周期，输出之间包含一个空行。

```cpp
#include <stdio.h>
#include <string.h>
#include <ctype.h>
char a[110];
int main() {
    int n;
    scanf("%d", &n);
    while(n--){
        scanf("%s", a);
        int alen = strlen(a);
        for(int i=1; i<=alen; ++i){     // 注意可以以alen为周期
            if(alen % i != 0) continue;
            bool f = true;
            for(int t = i; t < alen; t += i){
                if(memcmp(&a[0], &a[t], i) != 0){
                    f = false;
                    break;
                }
            }
            if(f){
                printf("%d\n", i);
                if(n) printf("\n");     // 注意输出需要包含空行
                break;
            }
        }
    }
    return 0;
}
```

### Puzzle [UVA-227]
有一个5*5的网格，其中恰好有一个格子是空的，其他格子各有一个字母。 一共有4种指令：A, B, L, R，分别表示把空格上、 下、 左、 右的相邻字母移到空格中。 输入初始网格和指令序列（可能有多行，以数字0结束），输出指令执行完毕后的网格。 如果有非法指令，应输出“This puzzle has no final configuration.”
**In:**
```
ABCDE
FGHIJ
KLMNO
PQRS 
TUVWX
AAA
LLLL0
Z
```
**Out:**
```
Puzzle #1:
A B C D
F G H I E
K L M N J
P Q R S O
T U V W X
```

```cpp
#include <stdio.h>
#include <string.h>
char a[5][5];
int main() {
    int m=0, n=0;
    int cnt = 0;
    while(true){
        for(int i=0; i<5; ++i){
            for(int j=0; j<5;++j){
                a[i][j] = getchar();
                if(a[i][j] == 'Z') return 0;
                if(a[i][j] == ' ') {m=i; n=j;}
            }
            getchar();      // 注意去掉每行末尾的换行符
        }
        if(cnt) printf("\n");   // 控制输出格式，除了第一个，其他的都需要一个空行
        printf("Puzzle #%d:\n", ++cnt);
        bool legal = true;
        while(true){
            char c = getchar();
            if(c == '\n') continue;     // 移动序列可能有换行
            if(c == '0') {
                if(legal){
                    for(int i=0; i<5; ++i){
                        for(int j=0; j<5;++j){
                            if(j) putchar(' ');
                            putchar(a[i][j]);
                        }
                        putchar('\n');
                    }
                }
                else printf("This puzzle has no final configuration.\n");
                getchar();      // 去除移动序列结束后的换行符
                break;
            }
            if(!legal) continue;
            else if(c == 'A'){
                if(m==0) legal = false;
                else {a[m][n] = a[m-1][n]; a[m-1][n]=' '; m -= 1;}
            }
            else if(c == 'B'){
                if(m==4) legal = false;
                else {a[m][n] = a[m+1][n]; a[m+1][n]=' '; m += 1;}
            }
            else if(c == 'L'){
                if(n==0) legal = false;
                else {a[m][n] = a[m][n-1]; a[m][n-1]=' '; n -= 1;}
            }
            else if(c == 'R'){
                if(n==4) legal = false;
                else {a[m][n] = a[m][n+1]; a[m][n+1]=' '; n += 1;}
            }
        }
    }
    return 0;
}
```

### Crossword Answers [UVA-232]
输入一个r行c列（1≤r，c≤10）的网格，黑格用“*”表示，每个白格都填有一个字母。 如果一个白格的左边相邻位置或者上边相邻位置没有白格（可能是黑格，也可能出了网格边界），则称这个白格是一个起始格。
首先把所有起始格按照从上到下、 从左到右的顺序编号为1, 2, 3,…。
接下来要找出所有横向单词（Across）。 这些单词必须从一个左边不是白格的起始格开始，向右延伸到一个黑格的左边或者整个网格的最右列。 最后找出所有竖向单词（Down）。 这些单词必须从一个上面不是白格的起始格开始，向下延伸到一个黑格的上边或者整个网格的最下行。
**In:**
```6 7
AIM*DEN
*ME*ONE
UPON*TO
SO*ERIN
*SA*OR*
IES*DEA
0
```
**Out:**
```
puzzle #1:
Across
  1.AIM
  4.DEN
  7.ME
  8.ONE
  9.UPON
 11.TO
 12.SO
 13.ERIN
 15.SA
 17.OR
 18.IES
 19.DEA
Down
  1.A
  2.IMPOSE
  3.MEO
  4.DO
  5.ENTIRE
  6.NEON
  9.US
 10.NE
 14.ROD
 16.AS
 18.I
 20.A
```
```cpp
#include <stdio.h>
#include <string.h>
char a[12][12];
int main() {
    int r,c;
    int pz = 0;
    while(true){
        scanf("%d", &r);
        if(r==0) break;
        scanf("%d", &c);
        for(int i=0; i<r; ++i) scanf("%s", a[i]);
        int d[12][12] = {0};
        int cnt = 0;
        for(int i=0; i<r; ++i){
            for(int j=0; j<c; ++j){
                if(a[i][j] != '*' && (i==0 || j==0 || a[i-1][j]=='*' || a[i][j-1]=='*')){
                    d[i][j] = ++cnt;        // 标注所有起始格
                }
            }
        }
        if(pz) printf("\n");        // 控制结果的空行
        printf("puzzle #%d:\n", ++pz);
        printf("Across\n");
        for(int i=0; i<r; ++i)
        for(int j=0; j<c; ++j){
            if(d[i][j] != 0 && (j==0 || a[i][j-1] == '*')){     // 必须是起始格，且左边不能是白格
                printf("%3d.", d[i][j]);                        // 输出是一个3个字符宽度的整数
                for(int k=j; k<c; ++k){
                    if(a[i][k] != '*') putchar(a[i][k]);        // 连续的白格组成单词
                    else break;
                }
                putchar('\n');
            }
        }
        printf("Down\n");
        for(int i=0; i<r; ++i)
        for(int j=0; j<c; ++j){
            if(d[i][j] != 0 && (i==0 || a[i-1][j] == '*')){     // 必须是起始格，且上面不能是白格
                printf("%3d.", d[i][j]);
                for(int k=i; k<r; ++k){
                    if(a[k][j] != '*') putchar(a[k][j]);        // 连续的白格组成单词
                    else break;
                }
                putchar('\n');
            }
        }
    }
    return 0;
}
```

### DNA Consensus String [UVA-1368]
输入m个长度均为n的DNA序列，求一个DNA序列，到所有序列的总Hamming距离尽量小。 两个等长字符串的Hamming距离等于字符不同的位置个数，例如，ACGT和GCGA的Hamming距离为2（左数第1, 4个字符不同）。
输入整数m和n（4≤m≤50, 4≤n≤1000），以及m个长度为n的DNA序列（只包含字母A，C，G，T），输出到m个序列的Hamming距离和最小的DNA序列和对应的距离。 如有多解，要求为字典序最小的解。 
**In:**
```
1
5 8
TATGATAC
TAAGCTAC
AAAGATCC
TGAGATAC
TAAGATGT
```
**Out:**
```
TAAGATAC
7
```
```cpp
#include <stdio.h>
#include <string.h>
int s[1010][4];
int geti(char c){
    if(c=='A') return 0;
    if(c=='C') return 1;
    if(c=='G') return 2;
    if(c=='T') return 3;
    else return -1;
}

char getc(int i){
    if(i==0) return 'A';
    if(i==1) return 'C';
    if(i==2) return 'G';
    if(i==3) return 'T';
    else return -1;
}
int main() {
    int cnt;
    scanf("%d", &cnt);

    while(cnt--){
        int m,n;
        scanf("%d%d", &m, &n);
        getchar();                  // 注意scanf后面的换行符没有删除
        for(int i=0; i<n; ++i){s[i][0]=s[i][1]=s[i][2]=s[i][3]=0;}

        for(int i=0; i<m; ++i){
            for(int j=0; j<n; ++j){
                ++(s[j][geti(getchar())]);
            }
            getchar();
        }

        int sum = 0;
        for(int i=0; i<n; ++i){
            int mx=0;
            for(int j=1; j<4; ++j){
                if(s[i][j] > s[i][mx]) mx = j;      // 保持字典序最小的
            }
            putchar(getc(mx));
            sum += m - s[i][mx];
        }
        putchar('\n');
        printf("%d\n", sum);
    }
    return 0;
}
```

# 1. Two Sum [Easy]
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.
Example:
```
Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

### 分析
方法1：暴力，复杂度O(n)，会超时
方法2：hash。用一个哈希表，存储每个数对应的下标，复杂度 O(n)
方法3：先排序，然后左右夹逼，排序O(nlog n)，左右夹逼 O(n) ，最终O(nlog n)。但是注意，这题需要返回的是下标，而不是数字本身

### C++
方法2：
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mm; 	\\ unrodered_map比map快
        vector<int> ans(2);
        for(int i = 0; i < nums.size(); ++i){	\\ 一边检索数组，一边判断
            int t = target - nums[i];
            if(mm.find(t) != mm.end()){
                ans[0] = mm[t];
                ans[1] = i;
                break;
            }
            else{
                mm.insert(make_pair(nums[i], i));
            }
        }
        return ans;
    }
};
```
方法3：
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<array<int, 2>> mm(nums.size());	\\ 内层使用array比用vector快
        vector<int> ans(2);

        for(int i = 0; i < nums.size(); ++i){	\\ 内层array分别存放值和下标
            mm[i][0] = nums[i];
            mm[i][1] = i;
        }     
        sort(mm.begin(), mm.end(), [](array<int, 2> &arr1, array<int, 2> &arr2){return arr1[0] < arr2[0];});
        
        auto bi = mm.begin();
        auto ei = mm.end();
        --ei;
        while(bi < ei){
            int tmp = (*bi)[0] + (*ei)[0];
            if(tmp == target){
                ans[0] = (*bi)[1];
                ans[1] = (*ei)[1];
                if(ans[0] > ans[1]) swap(ans[0], ans[1]);
                break;
            }
            else if(tmp < target){
                ++bi;
            }
            else{
                --ei;
            }
        }
        return ans;
    }
};
```

### Python
方法2：
```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        dic = {}
        for i, x in enumerate(nums):
            k = target - x
            if k in dic:
                return [dic[k], i]
            dic[x] = i
```
方法3：
```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        anums = [(i+1, x) for i, x in enumerate(nums)]
        anums.sort(key=lambda t: t[1])
        start = 0
        end = len(anums) - 1
        while start < end:
            sum = anums[start][1] + anums[end][1]
            if sum > target:
                end -= 1
            elif sum < target:
                start += 1
            else:
                break
        s = anums[start][0]
        e = anums[end][0]
        return [s, e] if s < e else [e, s] 
```