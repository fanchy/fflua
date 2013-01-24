FFLUA 是嵌入LUA，注册C++相关的对象到lua的封装库, FFLUA 拥有如下特性:
1. FFLUA 载入函数、设置路径更简单
    fflua_t fflua;
    try 
    {
        //! 注册C++ 对象到lua中
        fflua.reg(lua_reg);
        
        //! 载入lua文件
        fflua.add_package_path("./");
        fflua.load_file("test.lua");
    //---------
2. 获取全局变量、设置全局变量更容易
    
    //! 获取全局变量
    int var = 0;
    assert(0 == fflua.get_global_variable("test_var", var));
    //! 设置全局变量
    assert(0 == fflua.set_global_variable("test_var", ++var));
3. 
