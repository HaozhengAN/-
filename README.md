# -动态规划：

基本思想：
动态规划算法通常用于求解具有某种最优性质的问题。在这类问题中， 可能会有很多可行解。没一个解都对应于一个值，我们希望找到具有最优值的解。胎动规划算法与分治法类似，其基本思想也是将待求解问题分解为若干个子问题，先求解子问题，然后从这些子问题的解得到原问题的解。与分治法不同的是，适用于动态规划算法求解的问题，经分解得到的子问题往往不是互相独立的。若用分治法来解这类问题，则分解得到的子问题数目太多，有些子问题被重复计算很多次。如果我们能保存已解决子问题的答案，而在需要时再找出已求得的答案，这样就可以避免大量的重复计算，节省时间。我们可以用一个表来记录所有已解决的子问题的答案。不管该子问题以后是否被用到，只要它被计算过，就将其结果填入表中。这就是动态规划算法的基本思路。具体的动态规划算法多种多样，但它们具有相同的填表格式。

与分治法最大的差别是：适用于动态规划求解的问题，经分解后得到的子问题往往不是互相独立的（即下一个子阶段的求解是建立在上一个子阶段的解的基础上，进行进一步的求解）

应用场景：
适用于动态规划的问题必须满足最优化原理、无后效性和重叠性。

(1) 最优化原理（最优子结构性质）：一个最优化策略具有这样的性质，不论过去状态和决策如何，对前面的决策所形成的状态而言，余下的决策必须构成最优策略。简而言之，一个最优化策略的子策略总是最优的。一个问题满足最优化原理又称其具有最优子结构性质。

(2) 无后效性：将各阶段按照一定的次序排列好之后，对于某个给定的阶段状态，它以前各阶段的状态无法直接影响它未来的决策，而只能通过当前的这个状态。换句话说，每个状态都是过去历史的一个完整总结。这就是无后向性，又称无后效性。

(3) 子问题的重叠性：动态规划将原来具有指数级时间复杂度的搜索算法改进成了具有多项式时间复杂度的算法。其中的关键在于解决冗余，这就是动态规划算法的根本目的。动态规划实质上是一种以空间换时间的技术，它在实现的过程中，不得不存储产生过程中的各种状态，所以它的空间复杂度要大于其他算法。

01背包问题：

01背包问题描述：有编号分别为a,b,c,d,e的五件物品，它们的重量分别是2,2,6,5,4，它们的价值分别是6,3,5,4,6，每件物品数量只有一个，现在给你个承重为10的背包，如何让背包里装入的物品具有最大的价值总和？

动态规划的基本思路：将该问题转换成子问题，考虑五件物品在给定承重 C 的背包下最大价值为原问题，如下表所示，即为考虑abcde，C = 10时的最大价值，假设为f[5][10]，原问题的解可以分解为两种情况，第一种情况是不考虑放入a只考虑放入bcde承重为C时的最大价值f[4][C],第二种情况是考虑放入a时的最大价值，即value[a]+f[4][10-weight[a]]。 原问题的解f[5][10]取上述两种情况中的最大值，即f[5][10] = max{f[4][10], (f[4][10-weight[a]+value[a]))}。 由此可以看出里面涉及到需要计算f[4][10]和f[4][10-weight[a]]即f[4][4]等子问题。 以此类推，自顶向下的分析可以看出原问题需要子问题的解，我们需要先计算出子问题的解，自底向上求解。求解方式如下表所示，顺序是自底向上、从左往右，或者从左往右、自底向上都可以。注意此问题中的abcde可以包含相同的物件，它们之间的顺序也可以是任意的，不影响最终的结果。

name	weight	value	0	1	2	3	4	5	6	7	8	9	10
a	2	6	0	0	6	6	9	9	12	12	15	15	15
b	2	3	0	0	3	3	6	6	9	9	9	10	11
c	6	5	0	0	0	0	6	6	6	6	6	10	11
d	5	4	0	0	0	0	6	6	6	6	6	10	10
e	4	6	0	0	0	0	6	6	6	6	6	6	6
按上述描述的递推公式计算时，我们需要初始值，f[i][0]=0(1<=i<=5), f[1][j]=(j < weight[e])?0:value[e],(1<=j<=10)。代码如下：

#include <iostream>
using namespace std;


int knapsack(int *W, int *V, int *res, int n, int C)
{
    int value = 0;
    int **f = new int*[n];
    for(int i = 0; i < n; i++)
    {
        f[i] = new int[C+1];
    }

    for(int i = 0; i < n; i++)
        for(int j = 0; j <= C;j++)
            f[i][j] = 0;

    for(int i = 0; i < n; i++)
    {
        f[i][0] = 0;
    }
    for(int i = 1; i <= C; i++)
    {
        f[0][i] = (i < W[0])?0:V[0];
    }
    for(int i = 1; i < n; i++)
    {
        for(int y = 1; y <= C; y++)
        {
            if(y >= W[i])
            {
                f[i][y] = (f[i-1][y] > (f[i-1][y-W[i]] + V[i]))?f[i-1][y]:(f[i-1][y-W[i]] + V[i]);
            } else {
                f[i][y] = f[i-1][y];
            }
        }
    }

    for(int i = 0; i < n; i++)
    {
        for(int j = 0; j < C+1;j++)
           cout << f[i][j] << " ";
        cout << endl;
    }

    value = f[n-1][C];
    int j = n-1;
    int y = C;
    while(j)
    {
        if(f[j][y] == (f[j-1][y-W[j]] + V[j]))
        {
            res[j] = 1;
            y = y - W[j];
        }
        j--;
    }
    if(f[0][y])
    {
        res[0] = 1;
    }

    for(int i = 0; i < n;i++)
    {
        delete f[i];
        f[i] = 0;
    }
    delete [] f;
    f = 0;
    return value;
}

void test1()
{
    int n, C;
    while(cin >> n >> C)
    {
        int *W = new int[n];
        int *V = new int[n];
        int *res = new int[n];
        for(int i = 0; i < n; i++)
        {
            res[i] = 0;
        }
        int w = 0, v = 0, i = 0;
        while(i < n)
        {
            cin >> w >> v;
            W[i] = w;
            V[i] = v;
            i++;
        }
        int value = knapsack(W, V, res, n, C);
        cout << value << endl;
        for(int i = 0; i < n; i++)
            cout << res[i] << " ";
        cout << endl;
        delete W;
        delete V;
        delete res;
    }
}

int main()
{
    test1();
    return 0;
}

输入示例：

5 10      //5种物品，背包承重为10
2 6       //第一种物品，重量为2，价值为1，数目1个

7
输出示例：

0 0 6 6 6 6 6 6 6 6 6         //只放入第一种物品，承重为0-10的最优值结果
0 0 6 6 9 9 9 9 9 9 9         //放入第一种和第二种物品，承重为0-10的最优值结果
0 0 6 6 9 9 9 9 11 11 14      //放入第一种、第二种和第三种物品，承重为0-10的最优值结果
0 0 6 6 9 9 9 10 11 13 14     //放入前四种物品，承重为0-10的最优值结果
0 0 6 6 9 9 12 12 15 15 15    //放入五种物品，承重为0-10的最优值结果
15              //原问题的解，放入五种物品，承重为10的最优值结果
1 1 0 0 1       //背包中放入第一种、第二种、第五种物品时价值最高，1*6+1*3+0*5+0*4+1*6 = 15

8
完全背包问题：

完全背包问题描述：有编号分别为a,b,c,d的四件物品，它们的重量分别是2,3,4,7，它们的价值分别是1,3,5,9，每件物品数量无限个，现在给你个承重为10的背包，如何让背包里装入的物品具有最大的价值总和？

完全背包问题与01背包问题的区别在于每一件物品的数量都有无限个，而01背包每件物品数量只有一个。

问题解法其实和01背包问题一样，只是初始化的值和递推公式需要稍微变化一下。初始化时，当只考虑一件物品a时，f[1][j] = j/weight[a]。 递推公式计算时，f[i][y] = max{f[i-1][y], (f[i][y-weight[i]]+value[i])}，注意这里当考虑放入一个物品 i 时应当考虑还可能继续放入 i，因此这里是f[i][y-weight[i]]+value[i], 而不是f[i-1][y-weight[i]]+value[i]。

name	weight	value	0	1	2	3	4	5	6	7	8	9	10
d	7	9	0	0	1	3	5	5	6	9	10	10	12
c	4	5	0	0	1	3	5	5	6	8	10	10	11
b	3	3	0	0	1	3	3	4	6	6	7	9	9
a	2	1	0	0	1	1	2	2	3	3	4	4	5
代码如下：

#include <iostream>
using namespace std;


int knapsack_complete(int *W, int *V, int *res, int n, int C)
{
    int value = 0;
    int **f = new int*[n];
    for(int i = 0; i < n; i++)
    {
        f[i] = new int[C+1];
    }

    for(int i = 0; i < n; i++)
    {
        for(int j = 0; j < C+1; j++)
            f[i][j] = 0;
    }
    for(int y = 1; y < C+1; y++)
    {
        f[0][y] = (y < W[0])?0:((y/W[0])*V[0]);
    }

    for(int i = 1; i < n; i++)
    {
        for(int y = 1; y < C+1; y++)
        {
            if(y < W[i])
            {
                f[i][y] = f[i-1][y];
            } else {
                f[i][y] = (f[i-1][y] > (f[i][y-W[i]]+V[i]))?f[i-1][y]:(f[i][y-W[i]]+V[i]);
            }
        }
    }

    for(int i = 0; i < n; i++)
    {
        for(int j = 0; j < C+1; j++)
            cout << f[i][j] << " ";
        cout << endl;
    }

    value = f[n-1][C];
    int j = n-1;
    int y = C;
    while(j)
    {
        if(f[j][y] == (f[j][y-W[j]]+V[j]))
        {
            res[j]++;
            y = y - W[j];
        }
        j--;
    }
    res[0] = f[0][y]/V[0];

    for(int i = 0; i < n; i++)
    {
        delete f[i];
        f[i] = 0;
    }
    delete [] f;
    f = 0;
    return value;
}
void test1()
{
    int n, C;
    while(cin >> n >> C)
    {
        int *W = new int[n];
        int *V = new int[n];
        int *res = new int[n];
        for(int i = 0; i < n; i++)
        {
            res[i] = 0;
        }
        int i = 0, w, v;
        while(i < n)
        {
            cin >> w >> v;
            W[i] = w;
            V[i] = v;
            i++;
        }
        int value = knapsack_complete(W, V, res, n, C);
        cout << value << endl;
        for(int i = 0; i < n; i++)
            cout << res[i] << " ";
        cout << endl;
        delete W; W = 0;
        delete V; V = 0;
        delete res; res = 0;
    }
}

int main()
{
    test1();
    return 0;
}

输入示例：

4 10         //4种物品，背包承重为10
2 1          //第一种物品，重量为2，价值为1，数目不限
3 3
4 5
7 9
1
2
3
4
5
6
输出示例：

0 0 1 1 2 2 3 3 4 4 5        //只放入第一种物品，承重为0-10的最优值结果
0 0 1 3 3 4 6 6 7 9 9        //放入第一种和第二种物品，承重为0-10的最优值结果
0 0 1 3 5 5 6 8 10 10 11     //放入第一种、第二种和第三种物品，承重为0-10的最优值结果
0 0 1 3 5 5 6 9 10 10 12     //放入四种物品，承重为0-10的最优值结果
12           //原问题的解，放入四种物品，承重为10的最优值结果
0 1 0 1      //第一种物品放0个，第二种物品放1个，第三种物品放0个，第四种物品放1个，0*1+1*3+0*5+1*9 = 12
1
2
3
4
5
6
7
多重背包问题：

多重背包问题描述：有编号分别为a,b,c的三件物品，它们的重量分别是1，2，2，它们的价值分别是6，10，20，他们的数目分别是10，5，2，现在给你个承重为 8 的背包，如何让背包里装入的物品具有最大的价值总和？

多重背包和01背包、完全背包的区别：多重背包中每个物品的个数都是给定的，可能不是一个，绝对不是无限个。

有两种解法，解题思路：

作为一个新问题考虑，由于每个物品多了数目限制，因此初始化和递推公式都需要更改一下。初始化时，只考虑一件物品a时，f[1][j] = min{num[1], j/weight[1]}。 计算考虑i件物品承重限制为y时最大价值f[i][y]时，递推公式考虑两种情况，要么第 i 件物品一件也不放，就是f[i-1][y], 要么第 i 件物品放 k 件，其中 1 <= k <= (y/weight[i])，考虑这一共 k+1 种情况取其中的最大价值即为f[i][y]的值，即f[i][y] = max{f[i-1][y], (f[i-1][y-k*weight[i]]+k*value[i])}。 这里为什么不能像完全背包一样直接考虑f[i][y-weight[i]]+value[i]呢？因为这样不容易判断第 i 件物品的个数是否超过限制数量 num[i]。
name	weight	value	num	0	1	2	3	4	5	6	7	8
c	2	20	2	0	6	20	26	40	46	52	58	64
b	2	10	5	0	6	12	18	24	30	36	42	48
a	1	6	10	0	6	12	18	24	30	36	42	48
代码如下：

#include <iostream>
using namespace std;


int knapsack_limitnum(int *W, int *V, int *N, int *res, int n, int C)
{
    int value = 0;
    int **f = new int*[n];
    for(int i = 0; i < n; i++)
    {
        f[i] = new int[C+1];
    }
    for(int i = 0; i < n; i++)
        for(int j = 0; j < C+1; j++)
            f[i][j] = 0;

    for(int y = 1; y < C+1; y++)
    {
        int count = min(N[0], y/W[0]);
        f[0][y] = (y < W[0])?0:(count * V[0]);
    }

    for(int i = 1; i < n; i++)
    {
        for(int y = 1; y < C+1; y++)
        {
            if(y < W[i])
            {
                f[i][y] = f[i-1][y];
            } else {
                int count = min(N[i], y/W[i]);
                f[i][y] = f[i-1][y];
                for(int k = 1; k <= count; k++)
                {
                    int temp = f[i-1][y-W[i]*k] + k*V[i];
                    if(temp >= f[i][y])
                        f[i][y] = temp;
                }
            }
        }
    }

    for(int i = 0; i < n; i++)
    {
        for(int y = 0; y < C+1; y++)
            cout << f[i][y] << " ";
        cout << endl;
    }

    value = f[n-1][C];
    int j = n-1;
    int y = C;
    while(j)
    {
        int count = min(N[j], y/W[j]);
        for(int k = count; k > 0; k--)
        {
            if(f[j][y] == (f[j-1][y-W[j]*k]+k*V[j]))
            {
                res[j] = k;
                y = y - k*W[j];
                break;
            }
        }
        j--;
    }
    res[0] = f[0][y]/V[0];


    for(int i = 0;i < n; i++)
    {
        delete f[i];
        f[i] = 0;
    }
    delete [] f;
    f = 0;
    return value;
}

void test1()
{
    int n, C;
    while(cin >> n >> C)
    {
        int *W = new int[n];
        int *V = new int[n];
        int *N = new int[n];
        int *res = new int[n];
        for(int i =0; i < n; i++)
            res[i] = 0;
        int w, v, n1, i = 0;
        while(i < n)
        {
            cin >> w >> v >> n1;
            W[i] = w;
            V[i] = v;
            N[i] = n1;
            i++;
        }
        int value = knapsack_limitnum(W, V, N, res, n, C);
        cout << value << endl;
        for(int i = 0; i < n; i++)
            cout << res[i] << " ";
        cout << endl;
        delete res; res = 0;
        delete N; N = 0;
        delete V; V = 0;
        delete W; W = 0;
    }
}


int main()
{
    test1();
    return 0;
}

输入示例：

3 8           //3件物品，背包承重最大为8
1 6 10        //第一件物品， 重量为1，价值为6， 数目为10
2 10 5
2 20 2
1
2
3
4
5
输出结果：

0 6 12 18 24 30 36 42 48    //放入第一种物品，承重限制为0-8的最优值结果
0 6 12 18 24 30 36 42 48    //放入第一种和第二种物品，承重限制为0-8的最优值结果
0 6 20 26 40 46 52 58 64    //放入三种物品，承重限制为0-8的最优值结果
64            //原问题的解，在背包承重为8中放3种物品最大价值为64
4 0 2         //最优解对应第一件物品放4个，第二件物品放0个，第三件物品放2个，即4*6+0*10+2*20 = 64
1
2
3
4
5
6
多重背包的第二种解法，由01背包的分析可知，01背包中允许放入的物品有重复，即01背包中如果考虑要放入的物品的重量和价格相同，不影响最终的结果，因为我们可以考虑把多重背包问题中限制数目的物品拆分成单独的一件件物品，作为01背包问题考虑。问题解法和01背包一致，这里不再列举出动态规划的表格了。
代码：

#include <iostream>
#include <vector>
using namespace std;


int knapsack_limitnum(vector<int> &W, vector<int> &V, vector<int> &res, int C)
{
    int value = 0;
    int **f = new int*[W.size()];
    for(int i = 0; i < W.size(); i++)
    {
        f[i] = new int[C+1];
    }
    for(int i = 0; i < W.size(); i++)
    {
        f[i][0] = 0;
    }
    for(int i = 1; i < C+1; i++)
    {
        f[0][i] = (i < W[0])?0:V[0];
    }
    for(int i = 1; i < W.size(); i++)
    {
        for(int y = 1; y < C+1; y++)
        {
            if(y < W[i])
            {
                f[i][y] = f[i-1][y];
            } else {
                f[i][y] = (f[i-1][y] > (f[i-1][y-W[i]]+V[i]))?f[i-1][y]:(f[i-1][y-W[i]]+V[i]);
            }
        }
    }
    value = f[W.size()-1][C];
    int j = W.size()-1;
    int y = C;
    while(j)
    {
        if(f[j][y] == (f[j-1][y-W[j]]+V[j]))
        {
            res[j] = 1;
            y -= W[j];
        }
        j--;
    }
    res[0] = (f[0][y])?1:0;
    for(int i = 0; i < W.size(); i++)
    {
        delete f[i];
        f[i] = 0;
    }
    delete [] f;
    f = 0;
    return value;
}

void test1()
{
    int n, C;
    while(cin >> n >> C)
    {
        vector<int> W;
        vector<int> V;
        int *N = new int[n];
        int w, v, n1, i = 0;
        while(i < n)
        {
            cin >> w >> v >> n1;
            N[i] = n1;
            while(n1--)
            {
                W.push_back(w);
                V.push_back(v);
            }
            i++;
        }
        vector<int> res;
        for(int i = 0;i < W.size(); i++)
            res.push_back(0);
        int value = knapsack_limitnum(W, V, res, C);
        cout << value << endl;
        i = 0;
        int j = 0;
        int count = 0, sum = N[i];
        while(j < res.size())
        {
            if(j < sum)
            {
                if(res[j])
                    count++;
            } else {
                cout << count << " ";
                i++;
                sum += N[i];
                count = (res[j])?1:0;
            }
            j++;
        }
        cout << count << endl;
        delete N; N = 0;
    }
}

int main()
{
    test1();
    return 0;
}

输入示例：

3 8           //3件物品，背包承重最大为8
1 6 10        //第一件物品， 重量为1，价值为6， 数目为10
2 10 5
2 20 2
1
2
3
4
5
执行结果：

64            //原问题的解，在背包承重为8中放3种物品最大价值为64
4 0 2         //最优解对应第一件物品放4个，第二件物品放0个，第三件物品放2个，即4*6+0*10+2*20 = 64
