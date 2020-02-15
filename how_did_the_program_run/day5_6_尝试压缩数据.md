#### 尝试压缩数据
* BMP格式的图像文件是没有被压缩过的。将bmp压缩成JPEG大家会模糊，是采用的非可逆压缩。

* RLE压缩算法：字符乘以重复数字 run length ecoding

* 莫尔斯编码也可以做到压缩，他讲常用的字符(一般为出版社统计之类)设为短编码，位数少，比如A为一个字符(8位)，我们可以用1位来表示达到压缩。

* 哈夫曼算法：为各个压缩对象文件构造最佳的编码体系，并以该体系进行压缩，即原文件-》哈夫曼编码信息+压缩后的数据

* 哈夫曼树生成方法：以叶子节点向根生长。步骤1按出现的频率排序，任意将两个出现频率低的相连，左枝叶为0右枝叶为1，然后从根到叶子就是当前字符的编码。

  ```c++
  #include<iostream>  
  #include<string>  
  using namespace std;
   
  struct Node
  {
      double weight;  
      string ch;  
      string code; 
      int lchild, rchild, parent;
  };
  
  void Select(Node huffTree[], int *a, int *b, int n)//找权值最小的两个a和b  
  {
      int i;
      double weight = 0; //找最小的数
      for (i = 0; i <n; i++)
      {
          if (huffTree[i].parent != -1)     //判断节点是否已经选过
              continue;
          else
          {
              if (weight == 0)
              {
                  weight = huffTree[i].weight;
                  *a = i;
              }
              else
              {
                  if (huffTree[i].weight < weight)
                  {
                      weight = huffTree[i].weight;
                      *a = i;
                  }
              }
          }
      }
      weight = 0; //找第二小的数
      for (i = 0; i < n; i++)
      {
          if (huffTree[i].parent != -1 || (i == *a))//排除已选过的数
              continue;
          else
          {
              if (weight == 0)
              {
                  weight = huffTree[i].weight;
                  *b = i;
              }
              else
              {
                  if (huffTree[i].weight  < weight)
                  {
                      weight = huffTree[i].weight;
                      *b = i;
                  }
              }
          }
      }
      int temp;
      if (huffTree[*a].lchild < huffTree[*b].lchild)  //小的数放左边
      {
          temp = *a;
          *a = *b;
          *b = temp;
      }
  }
  
  void Huff_Tree(Node huffTree[], int w[], string ch[], int n)
  {
      for (int i = 0; i < 2 * n - 1; i++) //初始过程
      {
          huffTree[i].parent = -1;    
          huffTree[i].lchild = -1;    
          huffTree[i].rchild = -1;  
          huffTree[i].code = "";
      }
      for (int i = 0; i < n; i++)       
      {
          huffTree[i].weight = w[i];  
          huffTree[i].ch = ch[i];     
      }
      for (int k = n; k < 2 * n - 1; k++)
      {
          int i1 = 0;
          int i2 = 0;
          Select(huffTree, &i1, &i2, k); //将i1，i2节点合成节点k
          huffTree[i1].parent = k;   
          huffTree[i2].parent = k;
          huffTree[k].weight = huffTree[i1].weight + huffTree[i2].weight;
          huffTree[k].lchild = i1;
          huffTree[k].rchild = i2;
      }
  }
   
  void Huff_Code(Node huffTree[], int n)
  {
      int i, j, k;
      string s = "";
      for (i = 0; i < n; i++)  
      {
          s = "";         
          j = i;                
          while (huffTree[j].parent != -1) //从叶子往上找到根节点
          {
              k = huffTree[j].parent;
              if (j == huffTree[k].lchild) //如果是根的左孩子，则记为0
              {
                  s = s + "0";
              }
              else               
              {
                  s = s + "1";
              }
              j = huffTree[j].parent; 
          }
          cout << "字符 " << huffTree[i].ch << " 的编码：";
          for (int l = s.size() - 1; l >= 0; l--)    
          {
              cout << s[l];
              huffTree[i].code += s[l]; //保存编码
          }
          cout << endl;
      }
  }
  
  string Huff_Decode(Node huffTree[], int n,string s)
  {
      cout << "解码后为：";
      string temp = "",str="";//保存解码后的字符串
      for (int i = 0; i < s.size(); i++)
      {
          temp = temp + s[i];
          for (int j = 0; j < n; j++)
          {    
              if (temp == huffTree[j].code)
              {
                  str=str+ huffTree[j].ch;
                  temp = "";
                  break;
              }    
              else if (i == s.size()-1&&j==n-1&&temp!="")//全部遍历后没有
              {
                  str= "解码错误！";
              }
          }
      }
      return str;
  }
  
  int main()
  {
      //编码过程
      const int n=5;
      Node huffTree[2 * n];
      string str[] = { "A", "B", "C", "D", "E"};
      int w[] = { 30, 30, 5, 20, 15 };
      Huff_Tree(huffTree, w, str, n);
      Huff_Code(huffTree, n);
      //解码过程
      string s;
      cout << "输入编码：";
      cin >> s;
      cout << Huff_Decode(huffTree, n, s)<< endl;;
      system("pause");
      return 0;
  }
  ```

  

