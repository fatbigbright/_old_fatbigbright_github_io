---
layout: post
title: "一步步实现俄罗斯方块之二"
description: ""
category: 
tags: []
---
{% include JB/setup %}

第二篇随笔距离上次那篇晚了将近三周，主要因为技术问题上卡在了如何获得相对稳定的画布刷新帧频。经过多方实验与查询MSDN以及在stackoverflow问答网站上请教，竟得到了一个令人沮丧的答案：用WPF是无法获得精确、稳定的帧频的。也就是说，其实WPF并不是一个用来写游戏的理想技术选型。

但anyway，知道了不可能也算是一种收获，下面对整个研究过程做一下记录：

最初的想法是使用DispatcherTimer类的实例来当作计时器，该实例的Tick事件进行渲染处理。

说到这里，需要先乱入一个话题：为什么用的是DispatcherTimer类而不是Timer类呢？这里提到的Timer是System.Threading.Timer或它的包装类System.Timers.Timer。而DispatcherTimer类的完整命名空间是System.Windows.Threading.DispatcherTimer，后者是完全为WPF准备的。

Timer与DispatcherTimer最大的不同在于：前者是多线程的，而后者是单线程的。虽然多线程可能带来性能上的提升，但我们这里需要的是一个逐帧更新的动画（游戏应该本质上都是逐帧渲染而得到的动画），顺序执行——即下一帧的渲染不应早于上一帧渲染结束之前。因此在界面线程上运行的DispatcherTimer反而是更为相符的选择。但除此以外，如果在一个WPF文件中分别定义这两个类的实例并要在它们的Tick事件中加入更新WPF界面元素的操作，DispatcherTimer由于就是在当前绘制界面的线程上，所以直接访问并更新相应界面元素就可以了； 但Timer必须使用BeginInvoke或Invoke的方式通知界面线程去更新。关于这一点，将在下面的相关内容中再进行说明。

我做了一个WPF的demo来验证DispatcherTimer实例对帧频控制的能力，主窗口只有一个TextBox和一个Button，前者接受帧间毫秒数，即1000 / frameRate，按Button时程序将启动一个DispatcherTimer实例和一个StopWatch实例，在每一个Tick中累加一个计数器，当StopWatch计算的毫秒数大于1000时，停止Tick和计时，并输出此时的计数器值，那么如果输入为33.3，则预计的输入结果应当是30左右。——玩游戏的朋友应该都明白30FPS算是游戏帧频的基本需求——以下是这个demo的主体代码：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Controls;
    using System.Windows.Data;
    using System.Windows.Documents;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using System.Windows.Navigation;
    using System.Windows.Shapes;

    namespace TimerDemo
    {
       public partial class MainWindow : Window
       {
          private System.Windows.Threading.DispatcherTimer _timer = new System.Windows.Threading.DispatcherTimer();
          public MainWindow()
          {
             InitializeComponent();
             double ticks = 0L;
             System.Diagnostics.Stopwatch watch = new System.Diagnostics.Stopwatch();

             int frameCount = 0;

             //There is a textbox "txtTicks" which accept a millisecond value
             //And a button "btn", by clicking the button the dispatchertimer & 
             //stopwatcher are started.
             _timer.Tick += (sender, e) =>
             {
                frameCount++;
                //System.Diagnostics.Debug.WriteLine(watch.ElapsedMilliseconds);
                if (watch.ElapsedMilliseconds > 1000)
                {
                   _timer.Stop();
                   watch.Reset();
                   MessageBox.Show(string.Format("Already 1 second! FrameCount: {0}", frameCount));
                   frameCount = 0;
                }
             };

             this.btn.Click += (sender, e) =>
             {
                double.TryParse(this.txtTicks.Text, out ticks);
                if (ticks != 0.0)
                {
                   _timer.Interval = TimeSpan.FromMilliseconds(ticks);
                }
                _timer.Start();
                watch.Start();
             };
          }
       }
    }

但运行结果却和预想的完全不同：
![WTF](http://i.imgur.com/Bkp1uam.png)

结果在20上下而不是在30上下浮动！

经过多次测试（程序写得比较笨，每次测试都要重启程序），结果都在20左右徘佪。

百思不得其解之余，我向万能的StackOverflow提出了我的[问题](http://stackoverflow.com/questions/31237266/how-to-control-frame-rate-in-wpf-by-using-dispatcher-timer-accurately)，并把上述的代码贴了上去。

获得的回答的依据来自MSDN对于[DispatcherTimer](https://msdn.microsoft.com/en-us/library/system.windows.threading.dispatchertimer\(v=vs.110\).aspx)的表述：Timers are not guaranteed to execute exactly when the time interval occurs, but they are guaranteed to not execute before the time interval occurs. This is because DispatcherTimer operations are placed on the Dispatcher queue like other operations. When the DispatcherTimer operation executes is dependent on the other jobs in the queue and their priorities.

我的理解是，Timer（这个Timer应该同时包括了Timer类和DispatcherTimer类）不保证以设定的准确时间间隔执行触发Tick事件，只能保证两次Tick事件的时间间隔不会短于设定的时间间隔。也就是说，只会比预想的慢，不会比预想的快。

另外，从MSDN上的这段话中还能印证DispatcherTimer是单线程的。它的每次Tick事件中的操作也是要被放入Dispatcher队列中顺序执行的。也正因如此，触发的时机取决于其它操作任务及它们的优先级。

同时，MSDN也解释了使用DispatcherTimer而不使用Timer的原因：If a System.Timers.Timer is used in a WPF application, it is worth noting that the System.Timers.Timer runs on a different thread then the user interface (UI) thread. In order to access objects on the user interface (UI) thread, it is necessary to post the operation onto the Dispatcher of the user interface (UI) thread using Invoke or BeginInvoke. Reasons for using a DispatcherTimer opposed to a System.Timers.Timer are that the DispatcherTimer runs on the same thread as the Dispatcher and a DispatcherPriority can be set on the DispatcherTimer.

前面我们提到过，Timer要使用Invoke或BeginInvoke通知界面进程来更新界面元素，那么实际的操作仍要是放到Dispatcher的队列中等待执行。所以，然并卵，与用DispatcherTimer并无区别，却又没有后者拥有线程安全的天然优势。

至此，第一个问题算是有了结果：**DispatcherTimer虽可以用在做WPF逐帧动画中，但是使用它无法随心所欲地获得稳定的帧频。**其实，我也考虑过如果实际帧频低于理想帧频，那么就调节一下Tick事件的时间间隔使它接近理想帧频。但实际测试发现，在不同配置的电脑上，我在上面贴出的代码的运行结果平均值是有差异，也就是说DispatcherTimer的事件触发时机是和运行环境有关的，可以想像，一个配置很低的电脑，实际帧频可能会更低，因此微调事件触发间隔的想法也不是那么现实的了。（或者让程序自己计算帧频，然后根据统计结果自动进行微调……不过这个想法不再深究了罢）

本来问题到这里可以告一段落了，不过让我有兴趣继续挖掘这个问题的原因是，回答我在StackOverflow上的问题的用户Clemens提出我可以查看一下MSDN上的这篇文章[Property Animation Techniques Overview](https://msdn.microsoft.com/en-us/library/vstudio/aa970492(v=vs.100).aspx)中的“Per-Frame Animation: Bypass the Animation and Timing System”一段。（这里有个小插曲，Clemens给出的文章链接是错的，结果我还是自己在MSDN上按标题搜到的）

通过这篇文章，我知道了静态类CompositionTarget以及它的事件Rendering，顾名思义，即在整个窗体每一次渲染时触发的事件。

我意识到，每一次渲染不就是一帧吗？也就是说WPF已经早就帮我控制了帧频了，我只需要在渲染事件中加入我需要的代码就可以了？

在同一篇文章中我还发现Rendering事件的第二个参数args，可以被显式转换成RenderingArgs来使用，而这个事件参数类型有一个RenderingTime参数，看到它我不禁激动起来，难道说修改这个参数值就可以控制帧间时间间隔了吗？

结果却是比较失望的：这个参数只是一个只读属性，并且代表的是从界面开始渲染到当前时间所经过的时间间隔，用它也不过是统计帧频而已。统计帧频的示例代码如下：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Controls;
    using System.Windows.Data;
    using System.Windows.Documents;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using System.Windows.Navigation;
    using System.Windows.Shapes;

    namespace RenderingDemo
    {
        /// <summary>
        /// Interaction logic for MainWindow.xaml
        /// </summary>
        public partial class MainWindow : Window
        {
            private int _frameCount = 0;
            private double _secondCounter = 0d;
            private TimeSpan _lastRender;
            public MainWindow()
            {
                InitializeComponent();
                CustomInitializeComponent();
            }

            private void CustomInitializeComponent()
            {
                _lastRender = new TimeSpan(0, 0, 0, 0, 0);
                CompositionTarget.Rendering += (sender, args) =>
                {
                    RenderingEventArgs renderArgs = args as RenderingEventArgs;
                    Double deltaTime = (renderArgs.RenderingTime - _lastRender).TotalSeconds;

                    _lastRender = renderArgs.RenderingTime;
                    _secondCounter += deltaTime;
                    _frameCount++;

                    long frameRate = (long)(_frameCount / renderArgs.RenderingTime.TotalSeconds);
                    if (frameRate > 0)
                    {
                        this.lblFPS.Content = frameRate.ToString();
                    }
                };
            }
        }
    }

示例程序的截图如下：
![imgur](http://i.imgur.com/O9VkT7T.png)

显示的帧频接近50，可见虽然WPF号称尽量保持60帧，但并不能保证这一点。

我也尝试过在Rendering事件当中检测deltaTime，即本次渲染距离上次有效渲染的时间间隔，只有在这个间隔超过某个特定值时，才进行真正的渲染过程，并将当前时间更新为最新的一次有效渲染时间。（见下述片段中的`if(deltaTime < 0.022) return;`）

    CompositionTarget.Rendering += (sender, args) =>
    {
        RenderingEventArgs renderArgs = args as RenderingEventArgs;
        Double deltaTime = (renderArgs.RenderingTime - _lastRender).TotalSeconds;
        if (deltaTime < 0.022) return;

        _lastRender = renderArgs.RenderingTime;
        _secondCounter += deltaTime;
        _frameCount++;

        long frameRate = (long)(_frameCount / renderArgs.RenderingTime.TotalSeconds);
        if (frameRate > 0)
        {
            this.lblFPS.Content = frameRate.ToString();
        }
    };

但测试结果只能保证放慢帧频，却同样不能保证帧频准确符合预期。

最终，帧动画敲定使用实现Rendering事件的方式来创建，而控制帧频的努力就此作罢。

后记：当前Jekyll所选用的样式表，在显示多行代码时样式极渣无比，回头得调一调，起码先保证给代码块加上横向滚动条，保证代码不自动换行。另外就是代码着色啦～