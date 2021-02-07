## find_package
  有两种模式
  - 基本签名和模块模式  
    ```
    find_package(packageName
      [version [EXACT]]
      [QUIET] [REQUIRED]
      [[COMPONENTS] component1 [component2...]]
      [OPTIONAL_COMPONENTS component3 [component4...]]
      [MODULE]
      [NO_POLICY_SCOPE]
    )
    ```
    参数说明:  
    - packageName  
      必选, 包的名称
    - version  
      可选, 包的版本
    - EXACT  
      对于version的可选参数, 如果设定该选项, 则包版本必须完全匹配, 否则是该本或更高版本
    - QUIET  
      可选, 用于抑制警告信息和包第一次找到是正常打印的状态信息
    - REQUIRED  
      可选, 如果设置该选项, 则在找不到该包或无法满足版本要求时整个cmake过程停止并打印错误信息
    - COMPONENTS  
      可选, 用于罗列包的某些部分的先导词
    - component1  
      对于COMPONENTS来说是必选的, 包的某个库的名称
    - component2...  
      对于COMPONENTS来说是可选的, 包的一些库的名称, 之间以空格隔开
    - OPTIONAL_COMPONENTS component3 \[component4...\]  
      与COMPONETS意思相同, 除了它是可选的之外
    - MODULE  
      可选, 默认值, 表明采取模块搜索规则
    - NO_POLICY_SCOPE  
      可选, 来自于CMAKE2.6的遗留问题, 现在应该避免使用它
     
  - 配置模式
    ```
    find_package(packageName
      [version [EXACT]]
      [QUIET | REQUIRED]
      [[COMPONENTS] component1 [component2...]]
      [NO_MODULE | CONFIG]
      [NO_POLICY_SCOPE]
      [NAMES name1 [name2 ...]]
      [CONFIGS fileName1 [fileName2...]]
      [HINTS path1 [path2 ... ]]
      [PATHS path1 [path2 ... ]]
      [PATH_SUFFIXES suffix1 [suffix2 ...]]
      [CMAKE_FIND_ROOT_PATH_BOTH |
      ONLY_CMAKE_FIND_ROOT_PATH |
      NO_CMAKE_FIND_ROOT_PATH]
      [<skip-options>]
      # See further below
    )
    ``` 
    与模块模式中相同参数不再重复.
    - NO_MODULE | CONFIG  
      可选, 表明采用非模块或者配置模式搜索规则, 两者是等价的   
       
  - 搜索规则  


## 常用命令
```
cmake_minimum_required(VERSION 3.10)
CMAKE_TOOLCHAIN_FILE
CMAKE_CURRENT_LIST_FILE
CMAKE_CURRENT_LIST_DIR
CMAKE_ROOT
CMAKE_PREFIX_PATH
CMAKE_FRAMEWORK_PATH
CMAKE_APPBUNDLE_PATH
project
add_executable
add_library
target_link_libraries: PUBLIC, INTERFACE, PRIVATE
BUILD_SHARED_LIBS: YES, NO
find_package
include_directories
PkgConfig
pkg_search_module
link_directories
```
