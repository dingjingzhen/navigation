# navigation
navigationbar的各种问题

第一次在github上写文章，有点小紧张哈，接下来我就把我在做iOS开发工程中遇到的一些关于navigationbar的隐藏、设置透明度等问题跟大家分享一下
很多时候我们可能会遇到下图所示的情况，在这种情况下我们又两种方式对其进行解决：设置navigationbar隐藏和设置navigationbar透明。
![image](https://github.com/dingjingzhen/navigation//raw/master/image/image0.png)

1、navigationbar的隐藏
[self.navigationController setNavigationBarHidden: YES];//设置navigationbar隐藏

这么设置是对于当前页面是没有问题的，但问题在于如果在该页面设置了隐藏，在该跳转到其他页面时也会自动的隐藏掉navigationbar，这就并不是我们想要的。我们的目的是只在这一个页面隐藏navigationbar。
解决的办法是在viewWillDisappear中添加如下代码。
- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    [self.navigationController setNavigationBarHidden:NO];
}
这样做的时候你会发现在在跳转页面的时候没有动画效果了，此时你可以加上一个动画效果，很简单，将代码改成[self.navigationController setNavigationBarHidden:NO  animated:YES];即可。

ok,试一下效果

![image](https://github.com/dingjingzhen/navigation//raw/master/image/image3.png)

可以看到从下一个页面返回当前的页面的时候，会有很明显的黑块，这是由于在做返回的时候，navigationbar的隐藏没有做动画效果，在
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [self.navigationController setNavigationBarHidden: YES animated: YES];
}
这时候再试一下

![image](https://github.com/dingjingzhen/navigation//raw/master/image/image1.png)

已经没有那些黑色了

BUT，你以为这样就完美了吗？然鹅现实是残酷的，并非你想象的那么简单。

对于从下一个页面跳转到当前页面是没有任何问题，但是如果整个工程是类似第一张图片这样的navigationcontroller嵌套在tabbarcontroller的话，从其他的tab点击“我”跳转过来，你会发现一个很明显的navigationbar上方有一个向上滑动的动画，这样会导致出现一个黑色的横条一闪而过。这又怎么办呢？经过思考，我能确认绝对是[self.navigationController setNavigationBarHidden:NO  animated:YES];导致的，也就是这个animated:YES导致的，但是如果设成NO，前面也说了会导致从下个页面跳转到本页面出现黑块，设成YES又会导致从其他tab页面跳转过来会出现黑色长条，，，，死结吗？？？？？？？？就在我一筹莫展之际，我的师傅出马了，一句话：将viewWillAppear和viewWillDisappear中隐藏和显示navigationbar的动画全部设成animated，也就是
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [self.navigationController setNavigationBarHidden: YES animated: animated];
}
OK，大功告成！！！！

BUT！！，师傅说了，其实要实现这样的功能，还有别的方法，譬如说。。。我特么给忘了，有点小尴尬，后边有时间在研究研究。

2、navigationbar设为透明或者半透明。
直接上代码
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [self.navigationController.navigationBar setBackgroundImage:[UIImage new] forBarMetrics:UIBarMetricsDefault];
    [self.navigationController.navigationBar setShadowImage:[UIImage new]];
}

- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    [self.navigationController.navigationBar setBackgroundImage:nil forBarMetrics:UIBarMetricsDefault];
    [self.navigationController.navigationBar setShadowImage:nil];
}

效果

![image](https://github.com/dingjingzhen/navigation//raw/master/image/image2.png)

可以看到导航栏从不透明慢慢变为透明的一个过程。这样的效果其实很难看，具体怎么解决我在后面再继续研究一下，当然还有有个最笨的法子，自定义navigationbar，就是不用navigationcontroller，自己写一个类似的看着一模一样的导航栏。这个就具体问题具体分析啦。


