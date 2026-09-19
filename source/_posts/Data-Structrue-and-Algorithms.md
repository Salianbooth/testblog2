---
title: Data Structrue and Algorithms
date: 2024-11-26 15:08:53
tags:
  - Cpp
categories:
  - self-learning
---
# Tire字典树

>字典树，又称前缀树，单词查找树。主要用于匹配？就好比我们在浏览器进行搜索操作的时候，只输入“数据”后，就会弹出很多相关的词条,这就是它的一个主要应用。
 
![PixPin_2024-11-26_20-45-09.png](https://gitee.com/salianbooth/picture/raw/master/202411262045945.png)

## **基本性质**
- 根节点不包含字符，除根节点外的每一个节点都只包含一个字符
- 从根节点到某一结点，路径上经过的字符连接起来，为该节点对应的字符串
- 每个结点的所有子节点包含的字符都不相同。
![PixPin_2024-11-26_20-51-03.png](https://gitee.com/salianbooth/picture/raw/master/202411262051516.png)
在最后一个字符添加一个int  freqs,用来判断是否到结尾，并且能够记录存储该单词的数量。
```cpp
Tire Node
-> 
char data;
int freqs;
map<char,TireNode>    // 基于红黑树的map

//完整代码
class  TireTree{  
public:  
    //字典树的初始化  
    TireTree(){  
        root_ = new TireNode('\0',0);  
    }
    ~TireTree(){  
    queue<TireNode*> que;  
    que.push(root_);  
  
    while(!que.empty()){  
            TireNode* front = que.front();  
            que.pop();  
            for(auto & pair : front->nodeMap_) {  
                //将子节点加入队列  
                que.push(pair.second);  
            }  
            //释放当前结点  
            delete front;  
    }  
}
private:  
    //结点的定义  
    struct TireNode{  
        TireNode(char ch,int freqs):ch_(ch),freqs_(freqs) {}  
  
        char ch_;   //字节存储的字符数据  
        int freqs_; //单词末尾的字符存储单词的数量  
        //存储孩子结点的字符数据和结点指针的对应关系  
        map<char,TireNode*> nodeMap_;  
    };
private:  
    //定义树的类型  
    TireNode* root_;   // 指向树的根节点  
  
};
```

 **算法核心**
利用字符串的公共前缀来减少查询时间，最大限度的减少无用的字符串比较。

## **添加操作**
1. 如果孩子有该字符的数据结点，则继续移动cur指向当前结点，遍历下一字符
2. 如果没有孩子结点，创建相应的结点，移动cur指向当前结点，遍历下一个字符
```cpp
void add(const string& word){  
    TireNode* cur = root_;  
    for(int i = 0; i < word.size();i++){  
        auto childIt = cur-> nodeMap_.find(word[i]);  
        if(childIt == cur->nodeMap_.end()){  
            //相应结点没有，创建它  
            TireNode* child = new TireNode(word[i],0);  
            cur->nodeMap_.emplace(word[i],child);  
            cur = child;  
        }  
        else{  
            //相应的字符结点已经存在，移动cur指向对应的字符结点  
            cur = childIt -> second;  
        }  
    }  
    //cur指向了单词的最后一个结点  
    cur->freqs_ ++;  
}
```

## **查找操作**
```cpp
//查询单词  
int query(const string& word){  
    TireNode* cur = root_;  
    for(int i = 0 ; i < word.size();i++){  
        //查找效率很高，o(m)  m -> word.size()  
        auto childId = cur->nodeMap_.find(word[i]);  
        if(childId == cur-> nodeMap_.end()){  
            return 0;  
        }  
        //移动cur指向下一个单词的字符结点上  
        cur = childId -> second;  
    }  
    return cur->freqs_;  
}
```
## **前缀遍历**
```cpp
public:
//前序遍历字典树  
void preOrder(){  
    string word;  
    vector<string> wordlist ;  
    preOrder(root_,word,wordlist);  
    for(auto word:wordlist){  
        cout << word << endl;  
    }  
    cout << endl;  
  
}
private:  
    void preOrder(TireNode* cur, string word,vector<string> &wordlist){  
        //前序遍历VLR  
        if(cur != root_){  
            word.push_back(cur -> ch_);  
            if(cur->freqs_ > 0){  
                wordlist.emplace_back(word);  
            }  
        }  
        //递归处理孩子结点  
        for(auto pair : cur->nodeMap_){  
            preOrder(pair.second,word,wordlist);  
        }  
    }
```

## **前缀搜索**
```cpp
//串的前缀搜索  
vector<string>  queryPrefix(const string& prefix){  
    TireNode* cur = root_;  
    for(int i = 0 ; i < prefix.size();i++){  
        auto childIt = cur->nodeMap_.find(prefix[i]);  
        if(childIt == cur->nodeMap_.end()){  
            return {};  
        }  
        cur = childIt->second;  
    }  
  
    //cur就指向了前缀的最后一个字符结点了  
    vector<string > wordlist;  
    //前缀遍历中会直接把cur当前的结点数据给加入，所以要分割prefix
    string word = prefix.substr(0,prefix.size()-1);  
    //调用前缀遍历
    preOrder(cur, word, wordlist);  
    return wordlist;  
}
```

## **删除结点**
三种情况：
1. 所删除单词包含不删单词 --> pool 包含 po，删除pool只能删除ol
2. 所删除单词被包含  -- > po 属于 pool，删除po，不能将pool中po删除
3. 所删除单词和其他单词有公共前缀 -->  prepare && preview
```cpp
//删除单词  
void remove(const string& word){  
    TireNode* cur = root_;  
    TireNode* del= root_;  
    char delch = word[0];  
    for(int i = 0 ; i < word.size();i++){  
        auto childIt = cur->nodeMap_.find(word[i]);  
        if(childIt == cur->nodeMap_.end()){  
            return ;  
        }  
  
        if(cur->freqs_ > 0 || cur->nodeMap_.size() > 1){  
            //有一个独立的单词  
            del = cur;  
            delch = word[i];  
        }  
        //移动到下一个结点  
        cur = childIt->second;  
    }  
    //cur指向了末尾字符结点,出来了以后，单词存在  
    //情况一 ：  
    if(cur-> nodeMap_.empty()){  
        //开始删除  
        TireNode* child = del->nodeMap_[delch];  
        del ->nodeMap_.erase(delch);  
  
        //释放相应的结点内存  
        queue<TireNode*> que;  
        que.push(child);  
  
        while(!que.empty()){  
            TireNode* front = que.front();  
            que.pop();  
            for(auto & pair : front->nodeMap_) {  
                //将子节点加入队列  
                que.push(pair.second);  
            }  
            //释放当前结点  
            delete front;  
        }  
  
    }  
    else{  
        //情况1  
        //当前单词末尾字符后面还有字符结点,相当于删除  
        cur-> freqs_ = 0;  
    }  
  
}
```

# 回溯算法
**算法思想**：在包含问题的所有解的解空间树中，按照深度优先搜索的策略，从根节点出发深度搜索解空间树。当搜索到某一节点时，要先判断该节点是否包含问题的解，如果包含就从该节点出发继续深度搜索下去，否则逐层向上回溯。一般在搜索的过程中都会添加相应的剪枝函数，避免无效解的搜索，提高算法效率。

**解空间**：解空间就是所有解的可能取值构成的空间，一个解往往包含了得到这个解的每一步，往往就是对应解空间树中一条从根节点到叶子节点的路径。子集树和排列树都是一种解空间，它们不是真实存在的数据结构，也就是说并不是真的有这样一颗树，只是抽象出的解空间树。

我们先通过下面这个简单的代码来了解一下回溯算法：
![carbon (1).png](https://gitee.com/salianbooth/picture/raw/master/202412012045168.png)
我们通过递归调用func这个函数，来找到arr\[]的所有子集，我们可以画出下面这样的一个树，子集树，每一条路径对应着一个解。
![PixPin_2024-12-01_20-46-12.png](https://gitee.com/salianbooth/picture/raw/master/202412012046858.png)


**整数选择问题：给定一组整数，从里面挑选出一组整数，让选择的整数的和，和剩下的整数的和的差最小**
![carbon (2).png](https://gitee.com/salianbooth/picture/raw/master/202412012108703.png)
