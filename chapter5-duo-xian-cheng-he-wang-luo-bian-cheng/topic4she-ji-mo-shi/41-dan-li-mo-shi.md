# singlton 模式

## 什么是单例模式？

理解就是：整个程序中，即使在多线程的运行环境中，也只有一个运行的类对象实例；

这样设计的目的：多线程的情况下，可以拿到同一份数据；
实现思路：
1、类内部定义类自身的局部静态变量
2、构造函数是private的，也可以定义对象，因为是类内部；


## 项目中见到的单例

``` C++

实现实例一：

class DataCenter {
public:
    int Init(const global_conf_t& g_conf);

    // Typical singlton design using double-check.
    static DataCenter& Instance() {
        if (!m_is_instance_created) {
            utility::MutexGuard mutex_guard(&m_mutex);
            if (!m_is_instance_created) {
                static DataCenter data_center;
                m_instance = &data_center;
                m_is_instance_created = true;
            }
        }
        return *m_instance;
    }
    
private:

    //构造函数
    DataCenter();
    ~DataCenter();
    
    // Data center implementation.
    boost::scoped_ptr<DataCenterImpl> m_data_center_pimpl;
    static utility::Mutex m_mutex;
    static bool m_is_instance_created;
    static DataCenter* m_instance;

    // uncopyable 拷贝
    DataCenter(const DataCenter&);
    DataCenter& operator=(const DataCenter&);
};

```


上面的实现思考点：单例 + 多线程保护 + double check

1、要实现单例，使用静态变量和成员函数实现？
   静态成员函数，只能使用static成员;类体外定义并初始化；
   
2、不可构造：
   构造函数和拷贝构造函数设计成delete/private;

3、多线程的情况下，需要mutex保护？double check？
   
   自己的理解：如果需要修改单例对象管理的数据，多线程的情况下，还是要考虑mutex保护；
   
   为什么需要double check？双检锁-懒汉式 单例模式
   a. 减少开销：先判断一下有没有创建对象，不用每个线程都加锁
   b...
   
     
  c++ 11 对局部静态变量实现了线程安全，所以最简洁的实现方式：
  

```c++

static DataCenter& Instance(){

  static DataCenter data_center;
  m_instance = &data_center;
  
  return *m_instance;
}

  
```  

4、智能指针的使用：
   
   ```c++
   
   boost::scoped_ptr<TYPE> s_ptr
   等价于:
   std::unique_ptr<TYPE> s_ptr
   
   //智能独占指针
   
   
   ```

4、需要另一个资源管理的类对象，来实现数据的管理? 比如两个实现类中：m_data_center_impl ，m_resources_manager 成员变量。


实现实例二：

class ResourceFrame {
public:
    /// 框架初始化
    /// 主要是初始化各个组件以及一些独立的模块
    int Initialize(const map_sug_as::conf_info_t& susvr_conf);

    /// 更新资源
    int UpdateResource();
    ///c++11 单例模式
    static ResourceFrame& Instance() {
        static ResourceFrame _instance;
        return _instance;
    }

private:
    ResourceFrame(){};

    ~ResourceFrame(){};

    ResourceFrame(const ResourceFrame&) = delete;

    ResourceFrame& operator=(const ResourceFrame&) = delete;

    ResourcesManager m_resources_manager;
};


tips：

1、这个单例的实现，是不是有问题？每次调用ResourceFrame::instance()是不是都会有一个静态成员变量：_instance??

结论：不会，每次调用instance都是同一个静态局部变量,不会再生成一个对象；

```




参考：

1、https://zhuanlan.zhihu.com/p/62014096

