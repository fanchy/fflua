# FFLua
FFLua is a C++ lib to wrap operations of embeding lua and extending lua.
FFLUA 是嵌入LUA，注册C++相关的对象到lua的封装库, FFLUA 拥有如下特性:  
### Features
 * Onlye three header files
 * lightweight wrap
 * Efficient
 * clean code

### FFLUA make it easy load lua file [载入函数、设置路径更简单]
``` c++
    fflua_t fflua;
    try 
    {
        //! 注册C++ 对象到lua中
        fflua.reg(lua_reg);
        
        //! 载入lua文件
        fflua.add_package_path("./");
        fflua.load_file("test.lua");
``` 

### FFLUA make it easy to operate global varialbe of lua [获取全局变量、设置全局变量更容易  ]
``` c++
    int var = 0;  
    assert(0 == fflua.get_global_variable("test_var", var));  
    //! 设置全局变量  
    assert(0 == fflua.set_global_variable("test_var", ++var));  
``` 
###  FFlua make it easy to run lua string [执行lua语句更简单  ]
``` c++
    fflua.run_string("print("exe run_string!!")");  
```
###  FFlua make it easy to invoke lua function in c++ [调用lua函数更容易  ]
``` c++
    //! 调用lua函数, 基本类型作为参数  
        int32_t arg1 = 1;  
        float   arg2 = 2;  
        double  arg3 = 3;  
        string  arg4 = "4";  
        fflua.call<bool>("test_func", arg1, arg2, arg3,  arg4);  
``` 
###  FFlua make it easy to convert between lua talbe and C++ STL container. [lua table 和 C++ STL 转换更容易  ]
``` 
    //! 调用lua函数，stl类型作为参数， 自动转换为lua talbe  
    vector<int> vec;        vec.push_back(100);  
    list<float> lt;         lt.push_back(99.99);  
    set<string> st;         st.insert("OhNIce");  
    map<string, int> mp;    mp["key"] = 200;  
    fflua.call<string>("test_stl", vec, lt, st,  mp);  
``` 
###  FFlua make it easy to convert value returned by lua funciton to C++ STL container. [lua返回值转换 C++ STL 更简单  ]
``` c++
    //! 调用lua 函数返回 talbe，自动转换为stl结构  
    vec = fflua.call<vector<int> >("test_return_stl_vector");  
    lt  = fflua.call<list<float> >("test_return_stl_list");  
    st  = fflua.call<set<string> >("test_return_stl_set");  
    mp  = fflua.call<map<string, int> >("test_return_stl_map");  
``` 
###  FFlua make it easy to use c++ object pointer as argument of lua function directly. [C++ 最为参数更容易:]
``` c++
    //! 调用lua函数，c++ 对象作为参数, foo_t 必须被注册过
    foo_t* foo_ptr = new foo_t(456);
    fflua.call<bool>("test_object", foo_ptr);
``` 
###  FFlua make it easy to convert lua object returned by lua function to C++ object directly. [C++ 对象作为返回值更容易  ]
``` c++
    //! 调用lua函数，c++ 对象作为返回值, foo_t 必须被注册过   
    assert(foo_ptr == fflua.call<foo_t*>("test_ret_object", foo_ptr));  
    //! 调用lua函数，c++ 对象作为返回值, 自动转换为基类  
    base_t* base_ptr = fflua.call<base_t*>("test_ret_base_object", foo_ptr);  
    assert(base_ptr == foo_ptr);  
``` 
###  FFlua make it easy to register C++ class to lua. [注册C++ 对象更容易  ]
``` c++
    //! 注册C++ 对象到lua中  
    fflua.reg(lua_reg);  
  
``` 
###  Some Example code
``` c++
class base_t  
{  
public:  
    base_t():v(789){}  
    void dump()  
	{  
		printf("in %s a:%dn", __FUNCTION__, v);  
	}  
	int v;  
};  


    class foo_t: public base_t  
    {  
    public:  
    	foo_t(int b):a(b)  
    	{  
    		printf("in %s b:%d this=%pn", __FUNCTION__, b, this);  
    	}  
    	~foo_t()  
    	{  
    		printf("in %sn", __FUNCTION__);  
    	}  
    	void print(int64_t a, base_t* p) const  
    	{  
    		printf("in foo_t::print a:%ld p:%pn", (long)a, p);  
    	}  
      
    	static void dumy()  
    	{  
    		printf("in %sn", __FUNCTION__);  
    	}  
    	int a;  
    };  
  
    //! lua talbe 可以自动转换为stl 对象  
    void dumy(map<string, string> ret, vector<int> a, list<string> b, set<int64_t> c)  
    {  
        printf("in %s begin ------------n", __FUNCTION__);  
    	for (map<string, string>::iterator it =  ret.begin(); it != ret.end(); ++it)  
    	{  
    		printf("map:%s, val:%s:n", it->first.c_str(), it->second.c_str());  
    	}  
    	printf("in %s end ------------n", __FUNCTION__);  
    }  
      
    static void lua_reg(lua_State* ls)  
    {  
        //! 注册基类函数, ctor() 为构造函数的类型  
    	fflua_register_t<base_t, ctor()>(ls, "base_t")  //! 注册构造函数  
    					.def(&base_t::dump, "dump")     //! 注册基类的函数  
    					.def(&base_t::v, "v");          //! 注册基类的属性  
      
        //! 注册子类，ctor(int) 为构造函数， foo_t为类型名称， base_t为继承的基类名称  
    	fflua_register_t<foo_t, ctor(int)>(ls, "foo_t", "base_t")  
    				.def(&foo_t::print, "print")        //! 子类的函数  
    				.def(&foo_t::a, "a");               //! 子类的字段  
      
    	fflua_register_t<>(ls)  
    				.def(&dumy, "dumy");                //! 注册静态函数  
		/*支持注册基类
		fflua_register_t<base_t, virtual_ctor()>(ls, "base_t");
		*/
    }  
```
