练习
==============================================

编程作业
---------------------------------------------

进程创建
+++++++++++++++++++++++++++++++++++++++++++++

大家一定好奇过为啥进程创建要用 fork + execve 这么一个奇怪的系统调用，就不能直接搞一个新进程吗？思而不学则殆，我们就来试一试！这章的编程练习请大家实现一个完全 DIY 的系统调用 spawn，用以创建一个新进程。

spawn 系统调用定义( `标准spawn看这里 <https://man7.org/linux/man-pages/man3/posix_spawn.3.html>`_ )：

.. code-block:: rust

    fn sys_spawn(path: *const u8) -> isize

- syscall ID: 400
- 功能：新建子进程，使其执行目标程序。 
- 说明：成功返回子进程id，否则返回 -1。  
- 可能的错误： 
    - 无效的文件名。
    - 进程池满/内存不足等资源错误。  

TIPS：虽然测例很简单，但提醒读者 spawn **不必** 像 fork 一样复制父进程的地址空间。

实验要求
+++++++++++++++++++++++++++++++++++++++++++++
- 实现分支：ch5-lab
- 实验目录要求不变
- 通过所有测例

  在 os 目录下 ``make run TEST=1`` 加载所有测例， ``test_usertest`` 打包了所有你需要通过的测例，你也可以通过修改这个文件调整本地测试的内容。

challenge: 支持多核。

问答作业
--------------------------------------------

(1) fork + exec 的一个比较大的问题是 fork 之后的内存页/文件等资源完全没有使用就废弃了，针对这一点，有什么改进策略？

(2) [选做，不占分]其实使用了题(1)的策略之后，fork + exec 所带来的无效资源的问题已经基本被解决了，但是今年来 fork 还是在被不断的批判，那么到底是什么正在"杀死"fork？可以参考 `论文 <https://www.microsoft.com/en-us/research/uploads/prod/2019/04/fork-hotos19.pdf>`_ 。

(3) 请阅读下列代码，并分析程序的输出，假定不发生运行错误，不考虑行缓冲：
    
    .. code-block:: c 

      int main(){
          int val = 2;
          
          printf("%d", 0);
          int pid = fork();
          if (pid == 0) {
              val++;
              printf("%d", val);
          } else {
              val--;
              printf("%d", val);
              wait(NULL);
          }
          val++;
          printf("%d", val);
          return 0;
      } 


    如果 fork() 之后主程序先运行，则结果如何？如果 fork() 之后 child 先运行，则结果如何？


(4) 请阅读下列代码，分析程序的输出 ``A`` 的数量：( 已知 ``&&`` 的优先级比 ``||`` 高)

    .. code-block:: c 

      int main() {
          fork() && fork() && fork() || fork() && fork() || fork() && fork();
          printf("A");
          return 0; 
      }

    [选做，不占分] 更进一步，如果给出一个 ``&&`` ``||`` 的序列，如何设计一个程序来得到答案？

报告要求
------------------------------------------------------------

* 简单总结本次实验与上个实验相比你增加的东西。（控制在5行以内，不要贴代码）
* 完成问答问题
* (optional) 你对本次实验设计及难度的看法。