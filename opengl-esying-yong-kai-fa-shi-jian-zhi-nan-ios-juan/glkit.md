#GLKiewController
GLKiewController类是支持 OpenGL ES特有的行为和动画计时的 UIViewController的内建子类

#GLKView
GLKView 是 cocoa Touch UIVeiw 类的内建子类。GLKView简化了通过用 Core Animation层来自动创建并管理帧缓存和渲染缓存共享内存所需要做的工作

#GLKBaseEffect
GLKBaseEffect是 GLKit 提供的另一个内建子类。GLKBaseEffect的存在是为了简化 OpenGL ES 的许多常用操作，GLKBaseEffect隐藏了 iOS 设置支持的多个 OpenGL ES 版本之间的差异。
GLKBaseEffect类提供了不依赖于所使用的 OpenGL ES 版本的控制 OpenGL ES 渲染的方法，（因为 OpenGL ES1.1和 OpenGL ES2.0的内部工作机制非常不同，2.0版本为 GPU 执行专门定制了程序， 如果没有 GLKit和 GLKBaseEffect 类，完成一个简单的绘图就需要使用 OpenGL ES2.0的‘Shading Language’编写一个小的 GPU 程序，）。
GLKBaseEffect会在需要的时候自动的构建 GPU 程序


#GLKVector3
GLKVector3是一个包含了x,y,z的位置的结构体
#GLKVector2
GLKVector2是一个包含了纹理坐标的结构体
#GLKTextureInfo
GLKTextureInfo类封装了与纹理缓存的相关的信息，包括尺寸以及是否包含 MIP贴图。

#EAGLContext
EAGLContext 封装一个特定于某个平台的 OpenGL ES 上下文 
