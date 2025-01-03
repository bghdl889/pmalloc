### 一、功能概述
- **优化的内存布局安排**
- **持久化键值对存储（固定长度）**
- **可移植的地址支持**
- **内嵌结构化数据持久化**

### 二、如何使用

1. **设置持久化内存池的内存描述符**

   你需要通过一个文件句柄来附加到一个内存池：
   ```c
   void *md = pmalloc_attach(fd, NULL, PM_CAPACITY);
   assert(NULL != md);  // 检查是否成功附加
   ```

2. **操作持久化内存块**

   遍历存储的键值对：
   ```c
   for (i = 0; i < 16; ++i)
   {
       /* 获取持久化的键 */
       key = pmalloc_getkey(md, i);
       if (NULL != key)
           printf("Prekey... %p --> %s %lu\n", key, (char*)key, sizeof(size_t));
       else break;
   }
   ```

3. **分配指定大小的内存块**
   
   分配一个内存块并将数据存储到键值对中：
   ```c
   void *data = pmalloc(md, 0x1000 * 5);  // 分配 5KB 内存
   if (NULL != data) {
       sprintf(data, "output data row %d", i);
       /* 将其存储为键值对 */
       pmalloc_setkey(md, i, data);
   }
   ```

4. **从键值对存储中检索持久化的内存块**

   根据键检索并操作内存：
   ```c
   keydata = pmalloc_getkey(md, keyidx);
   if (NULL != keydata) {
       /* 释放已分配的内存块 */
       pmfree(md, keydata);
       pmalloc_setkey(md, keyidx, NULL);
       printf(ANSI_COLOR_GREEN "释放原始键数据 %p！\n" ANSI_COLOR_RESET, keydata);
   } else {
       printf(ANSI_COLOR_CYAN "没有为该键设置原始数据！\n" ANSI_COLOR_RESET);
   }
   ```

5. **关闭内存描述符**

   在操作完内存之后，记得关闭：
   ```c
   pmalloc_detach(md);
   ```

---

### 三、如何构建

1. **安装依赖库**

   在编译单元测试时，你需要先安装 `check` 模块：
   - 参考：[check模块安装文档](http://libcheck.github.io/check/web/install.html)

2. **创建构建目录并编译**

   执行以下命令来构建项目：
   ```bash
   $ mkdir build
   $ cd build
   $ cmake ..
   $ make
   $ sudo make install
   ```

---

### 四、如何打包

1. **打包项目**

   在 `build` 目录中，使用 CPack 工具来打包：
   ```bash
   $ cd build
   $ cpack -C CPackConfig.cmake
   ```

---

### 总结
- 你首先通过 `pmalloc_attach` 附加内存池，并在此基础上进行键值存取、内存分配与释放操作。
- 项目构建使用 CMake 工具，打包使用 CPack。
- 需要注意依赖库 `check` 的安装和配置。

这样，你就可以按照这个流程来使用、构建和打包该项目，同时保证原项目代码的独立性。
