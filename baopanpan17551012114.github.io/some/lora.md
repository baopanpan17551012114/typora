Stable Diffusion https://zhuanlan.zhihu.com/p/560226367

本地化：https://zhuanlan.zhihu.com/p/565851314



GitHub: https://github.com/bmaltais/kohya_ss



一、MCOS M1本地化

安装参考：https://zhuanlan.zhihu.com/p/624059896

1、执行./setup.sh过程中有报错，如果是requirement中各个包版本不对，需要更改镜像；

2、激活虚拟环境 source ./venv/bin/activate(通过python而不是conda创建)

3、启动，./gui.sh或者python kohya_gui.py --listen 0.0.0.0 --server_port 7861 --inbrowser --share(指定端口)

4、只训练lora启动方法python lora_gui.py

5、M1不支持cuda、xformers和fp16，training parameters中Use xformers取消勾选，Mixed precision和Save precision注意选择类型



[AI绘图]一些lora模型训练心得: https://zhuanlan.zhihu.com/p/616837063



LORA炼丹模型训练教程之线上版：https://zhuanlan.zhihu.com/p/618362134

Lora 使用与训练：AI绘画：Lora模型训练完整流程！https://blog.csdn.net/wpgdream/article/details/130607099#:~:text=AI绘画：Lora模型训练完整流程！%201%200.%20LoRA微调模型是什么？%20LoRA的全称是Low-Rank%20Adaptation%20of%20Large,经过上面的处理，素材已经搞定了%E3%80%82%20接下来就是设置训练参数%E3%80%82%20...%205%204.模型应用%20搞了那么久终于可以用了%E3%80%82%20lora模型的使用，我们之前的文章里面已经有详细的介绍了，这里就简单的演示一下%E3%80%82%20



lora训练完整：https://zhuanlan.zhihu.com/p/620583928

​							风格：https://zhuanlan.zhihu.com/p/617442001  https://new.qq.com/rain/a/20230418A01XUH00

