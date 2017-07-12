OpenGL ES 为缓存提供数据有如下7个步骤 以及定义的 C 语言函数
1. 生成（generate）`glGenBuffers()` -- 请求 OpenGL ES为图形处理器控制的缓存生成一个独一无二的标识符
2. 绑定（Bind）`glBindBuffer()` -- 告诉 OpenGL ES 为接下来的运算使用一个缓存
3. 缓存数据（buffer data）`glBufferData()`或者`glBufferSubData()` -- 让 OpenGL ES为当前绑定的缓存分配并初始化足够的连续内存（通常是从 CPU 控制的内存复制数据到分配的内存）。
4. 启用(Enable) `glEnableVertexAttribArray()` 或者禁止（Disable）`glDisableVertexAttribArray()` -- 告诉 OpenGL ES在接下来的渲染中是否使用缓存中的数据
5. 设置指针(set Pointers) `glVertexAttribPointer()` -- 告诉 OpenGL ES在缓存中的数据的类型和所有需要访问的数据内存偏移值
6. 绘图(Draw) `glDrawElements()` --  告诉 OpenGL ES 使用当前绑定并启用的缓存中的数据渲染整个场景的或者某个场景的一部分
7. 删除（Delete）`glDeleteBuffers()` -- 告诉 OpenGL ES 删除以前生成的缓存并释放有关的资源