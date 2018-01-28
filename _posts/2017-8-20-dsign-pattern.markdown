---
layout: post
title:  "设计模式"
date:   2017-8-20 7:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2017.8.20   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}



# 简单工厂模式

简单工厂模式 又称 静态工厂方法模式（Static Factory Method），它不是Gof 所讲的23种设计模式之一，但是它却是我们在编码过程中经常使用的方法之一。

把new XXX给封装了，可以用id来选择new哪一种类。


## 静态工厂方法统一管理对象的创建

静态工厂方法通过传入的参数判断决定创建哪一个产品的实例，封装了对象的创建，客户端只管消费，实现了对责任（模块）的分割。

为工厂类定义一个静态函数，传入需要创建的实例的类的编号，返回创建对应编号的类的实例的指针。比如，如下实现，其中 Product0，Profuct1都是Product的子类。


## 静态工厂方法推迟了产品的实例化

通过XML配置文件就能改变具体要创建的产品实例，修改为其它的产品实例，代码不须重新编译。

## CODE

xml配置文件

```xml
<root>
	<product>1</product>
	<!--<product>0</product>-->
</root>
```

c++代码

```c++
//http://blog.csdn.net/chollima/article/details/3985570

#include <iostream>
#include <string>

#include "rapidxml\rapidxml.hpp"
#include "rapidxml\rapidxml_utils.hpp"

//if  destructor is not virtual, when we destroy the Product, only base class's destructor will be called, which is not safe.
#define VIRTUAL_DESTRUCT
using namespace std;

class Product
{
public: 
	Product()
	{
		cout << "Create Product" << endl;
	}
#ifdef VIRTUAL_DESTRUCT
	virtual ~Product()
	{
		cout << "Destroy Product" << endl;
	}
#else
	~Product()
	{
		cout << "Destroy Product" << endl;
	}
#endif

	virtual void printName(){ cout<< "Product" <<endl; }
	//void printName(){ cout << "Product" << endl; }

};

class Product0 : public Product
{
public:
	Product0()
	{
		cout << "Create Product0" << endl;
	}

#ifdef VIRTUAL_DESTRUCT
	virtual ~Product0()
	{
		cout << "Destroy Product0" << endl;
	}
#else
	~Product()
	{
		cout << "Destroy Product0" << endl;
	}
#endif

	void printName(){ cout << "Product0" << endl; }

};

class Product1 : public Product
{
public:
	Product1()
	{
		cout << "Create Product1" << endl;
	}

#ifdef VIRTUAL_DESTRUCT
	virtual ~Product1()
	{
		cout << "Destroy Product1" << endl;
	}
#else
	~Product()
	{
		cout << "Destroy Product1" << endl;
	}
#endif

	void printName(){ cout << "Product1" << endl; }

};

class simple_factory
{
public:
	static Product* CreateProduct(int numb);
};

Product* simple_factory::CreateProduct(int numb)
{
	//缺点：每次添加一个产品子类都必须在工厂类中添加一个判断分支，这样违背了 开放-封闭原则 
	//==> 催生了 工厂模式
	if (numb == 0)
	{
		return new Product0();
	}
	else if (numb == 1)
	{
		return new Product1();
	}
	else
	{
		cout << "can not create" << endl;
		return nullptr;
	}
}

bool ReadCreateConfig(string xmlConfigFile, vector<Product*> &vecProduct)
{
	rapidxml::file<> file(xmlConfigFile.c_str());
	rapidxml::xml_document<> doc;

	doc.parse<0>(file.data());
	rapidxml::xml_node<>* xmlRoot = doc.first_node(); //rootnode:overalConfig

	//first level child
	rapidxml::xml_node<>* xmlNode = xmlRoot->first_node("product");
	string productString(xmlNode->value());
	int productInt = stoi(productString);
	vecProduct.push_back(simple_factory::CreateProduct(productInt));

	//first level child END
	
	return true;
}


int main()
{
	/*Product * xx = simple_factory::CreateProduct(0);
	xx = simple_factory::CreateProduct(1);*/
	vector<Product*> vecProduct;
	ReadCreateConfig("xmlConfigFile.xml", vecProduct);
	for (auto it : vecProduct)
	{
		it->printName();
		it->~Product();
	}

	return 0;
}
```


## 执行

1. 基于xml创建 Product
2. 构造析构的顺序   
  构造函数调用顺序：父类，子类   
  析构函数调用顺序：子类，父类

```c
Create Product
Create Product1
Product1
Destroy Product1
Destroy Product
```

## 总结
1. 每往工厂中添加一个新的产品，就要在CreateProcut添加一个新的判断分支，违背了设计模式的 开放-封闭原则（对扩展是开放的，而对修改是封闭的）。 ==> 催生了 工厂模式

2. 产品子类必须是同一类型的, 拥有共同的方法。比如Product0 Product1都继承与 虚基类Product（接口）。


----------


## Reference

[0] http://www.cnblogs.com/cxjchen/p/3143633.html   
[1] http://blog.csdn.net/chollima/article/details/3985570   
[2] 《设计模式》- 刘伟  


# 抽象工厂模式

工厂模式   
简单工厂模式   
工厂模式   
抽象工厂模式   
----------------------------------------


简单工厂模式 --> 抽象工厂模式   

**简单工厂模式**
产品是一个抽象的概念。   
汽车工厂 生产 汽车。   
汽车可以是 轿车，大巴。   

通过脚本来确定工厂 生产 什么产品。    
可选择的产品类别是一样的，继承于同一父类。   


**抽象工厂模式**
有点繁琐。简单说就是不仅产品是抽象的概念， 工厂 也变成了 抽象的概念。   
汽车工厂 生产 轿车。    
宝马的工厂 生成轿车，奔驰的工厂 生产轿车。   

```c
Abstruct Factory
|		\
|	  	  \
Factory0     Factory1
| \			|  \
|   \		|    \
pa0  pb0        pa1  pb1
```

通过 脚本 确定 什么工厂 生产 什么产品。 比如 汽车工厂 生产 汽车 ==》 宝马工厂 生产 轿车。   

可选择的工厂类别是一样的，继承于同一父类。   
可选择的产品类别是一样的，继承于同一父类。   


## Reference
 
[0] http://www.cnblogs.com/cxjchen/p/3143633.html




# 观察者模式

## 观察者模式

- 也被称为
  - 发布-订阅模式 publish-subscribe
  - 模型-视图模式 model-view
  - 源-收听者模式 Source-Listener
- 是软件设计模式的一种
- 定义： 一个目标物体管理所有相依于它的观察者，并且在它本身的状态改变时主动发出通知。这通常通过呼叫观察者所提供的方法来实现。
- 常被用来实现事件处理系统。


## 观察者 Observer

观察者将自己注册到被观察者中，被观察者将观察者们存放在一个容器（container）里。

观察者将自己注册到被观察者的容器中时，被观察者不应过问观察者的具体类型，而是应该使用观察者的接口。

## 被观察者
被观察对象发生了某种变化，从容器中得到所有注册过的观察者，将变化通知观察者。


## 撤销观察
观察者告诉被观察者要撤销观察，被观察者从容器中将观察者
去除。



## CODE

```c++

//https://baike.baidu.com/item/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/5881786?fr=aladdin

//filename observer.h
#include <iostream>
#include <set>
#include <string>
using namespace std;
/////////////////////抽象模式定义
class CObservable;

//观察者，纯虚基类
class CObserver
{
public:
	CObserver(){};
	virtual ~CObserver(){};
	//当被观察的目标发生变化时，通知调用该方法
	//来自被观察者pObs, 扩展参数为pArg
	virtual void Update(CObservable* pObs, void* pArg = NULL) = 0;
};

//被观察者，即Subject
class CObservable
{
public:
	CObservable() : m_bChanged(false) {};
	virtual ~CObservable() {};
	void Attach(CObserver* pObs);   //注册观察者
	void Detach(CObserver* pObs);   //注销观察者
	void DetachAll();               //注销所有观察者
	void Notify(void* pArg = NULL); //若状态变化，则遍历观察者，逐个通知更新
	bool HasChanged();              //测试目标状态是否变化
	int GetObserversCount();        //获取观察者数量
protected:
	void SetChanged();              //设置状态变化!!!必须继承CObservable才能设置目标状态
	void ClearChanged();            //初始化目标为未变化状态
private:
	bool m_bChanged;                //状态
	set<CObserver*> m_setObs;       //set保证目标唯一性
};

/////////////////////抽象模式实现
void CObservable::Attach(CObserver* pObs){
	if (!pObs) return;
	m_setObs.insert(pObs);
}
void CObservable::Detach(CObserver* pObs){
	if (!pObs) return;
	m_setObs.erase(pObs);
}
void CObservable::DetachAll(){
	m_setObs.clear();
}
void CObservable::SetChanged(){
	m_bChanged = true;
}
void CObservable::ClearChanged(){
	m_bChanged = false;
}
bool CObservable::HasChanged(){
	return m_bChanged;
}
int CObservable::GetObserversCount(){
	return m_setObs.size();
}
void CObservable::Notify(void* pArg /* = NULL */){
	if (!HasChanged()) return;
	cout << "notify observers…" << endl;
	ClearChanged();
	set<CObserver*>::iterator itr = m_setObs.begin();
	for (; itr != m_setObs.end(); itr++){
		(*itr)->Update(this, pArg);
	}
}
/////////////////////具体应用类定义和实现
//bloger是发布者，即被观察者(subject)
class CBloger : public CObservable
{
public:
	void Publish(const string &strContent){
		cout << "bloger publish, content: " << strContent << endl;
		SetChanged();
		Notify(const_cast<char*>(strContent.c_str()));
	}
};
//portal是发布者，即被观察者(subject)
class CPortal : public CObservable
{
public:
	void Publish(const string &strContent){
		cout << "portal publish, content: " << strContent << endl;
		SetChanged();
		Notify(const_cast<char*>(strContent.c_str()));
	}
};
//RSS阅读器，观察者
class CRSSReader : public CObserver
{
public:
	CRSSReader(const string &strName) : m_strName(strName){}
	virtual void Update(CObservable* pObs, void* pArg = NULL){
		char* pContent = static_cast<char*>(pArg);
		//观察多个目标
		if (dynamic_cast<CBloger*>(pObs)){
			cout << m_strName << " updated from bloger, content: " << pContent << endl;
		}
		else if (dynamic_cast<CPortal*>(pObs)){
			cout << m_strName << " updated from portal, content: " << pContent << endl;
		}
	}
private:
	string m_strName;
};
//Mail阅读器，观察者
class CMailReader : public CObserver
{
public:
	CMailReader(const string &strName) : m_strName(strName){}
	virtual void Update(CObservable* pObs, void* pArg = NULL){
		char* pContent = static_cast<char*>(pArg);
		if (dynamic_cast<CBloger*>(pObs)){
			cout << m_strName << " updated from bloger, content: " << pContent << endl;
		}
		if (dynamic_cast<CPortal*>(pObs)){
			cout << m_strName << " updated from portal, content: " << pContent << endl;
		}
	}
private:
	string m_strName;
};

//#include "observer.h"
int main()
{
	//目标(被观察者)
	CBloger* pBloger = new CBloger();
	CPortal* pPortal = new CPortal();
	//被观察者. 一个观察者可以观察多个目标
	CRSSReader* pRssReader = new CRSSReader("rss reader");
	CMailReader* pMailReader = new CMailReader("mail reader");
	pBloger->Attach(pRssReader);  //bloger注册观察者
	pBloger->Attach(pMailReader); //bloger注册观察者
	pPortal->Attach(pRssReader);  //portal注册观察者
	pPortal->Attach(pMailReader); //portal注册观察者
	//博客发布信息
	pBloger->Publish("博客分享设计模式");
	cout << endl;
	//门户发布信息
	pPortal->Publish("门户分享设计模式");
	cout << "\nportal detached mail reader" << endl;

	pPortal->Detach(pMailReader);

	cout << "portal observers count: " << pPortal->GetObserversCount() << endl << endl;
	pPortal->Publish("门户分享设计模式");

	system("pause");
	return 0;
}


```

## Reference
[0] 设计模式学习笔记-观察者模式  CnBlog [[LINK](http://www.cnblogs.com/wangjq/archive/2012/07/12/2587966.html)]   
[1] 观察者模式和发布订阅模式有什么不同？ zhihu [[LINK](https://www.zhihu.com/question/23486749)]   
[2] **观察者模式 百度百科** [[LINK](https://baike.baidu.com/item/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/5881786?fr=aladdin)]   



简单工厂模式 http://note.youdao.com/noteshare?id=336aaa1ede22ce2b868eee6256f2e344
抽象工厂模式 http://note.youdao.com/noteshare?id=0a8965d6e9f3e454cb7d77cb6b5f1def
观察者模式 http://note.youdao.com/noteshare?id=ff90c1d641fbb203941d225a50be636d

