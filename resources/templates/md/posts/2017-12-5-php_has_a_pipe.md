{
:title "Php Has a Pipe"
:layout :post
:tags ["language"]
}

<img src="https://github.com/wlisper/myblog/blob/master/resources/templates/img/php-pipe.png?raw=true" alt=""/>

PHP 是一根管子，你通常会把它的一端接到汽车的排气管，另一端插进车窗里，然后你坐进车里，开动引擎。

这是以前在网上看到的一则比喻，当时不懂其意；最近在工作中需要用到php，也踩了一些坑，才知道管子的比喻实在生动。

才开始接触php，你会发觉php的词法环境是有悖于大多数程序语言的设计的，一些你认为应该发生的，它不会让你如愿；一些你不希望发生的，它会悄悄的帮你做了。

比如下面的代码：

    $env  = 'linux';
    function fun1() {
        if ($env == 'linux') {
            do_something();
        }
    }

你可能以为在env为linux的情况下，会做些事情。然而php并不会帮你做的，在php看来env不是一个全局变量，php有它的解决方案，你可以在函数中申明env为global的，就像下面这样：

    $env = 'linux';
    function fun2() {
        global $env;    //  方便吧？
        if ($env == 'linux') {
            do_something();
        }
    }

有时你不希望做的一些事，php会帮你做的。例如下面的代码:

    function fun3() {
    
        $env = 'windows';   // 我的系统类型
        $allenv = array(
            'linux', 'windows', 'mac'
        );
        foreach ($allenv as $env) {
            do_system_specific($env);   // 根据不同的系统，做一些特定的工作
        }
    
        do_my_system_work($env);   // 开始做自己系统相关的工作。
    }

你可能以为在做完各种系统相关的工作后，还可以回来做自己系统相关的工作；然而你自己的系统已经变为mac了，后面的工作就全错了。发生这个的原因是php的foreach在迭代中修改了env的值，而且是
'外层’的env, 这就好像内层产生了废气, php不帮你回收清理, 反而将这些废气排到外面, 污染了外层环境, 增加出现bug的可能

