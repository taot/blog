---
layout: post
tags:
  - haskell
  - monad
---
### 一、什么是Monad

Haskell的函数是纯函数(pure function)，无法进行很多需要副作用(side-effect)的操作，如 IO，Logging等。因此引入了Monad的概念。IO等有副作用的操作都放入Monad中。想用Haskell写出有用的程序，必须理解Monad。

<!--more-->

Monad的定义其实非常简单，以下是Real World Haskell里给的定义：

A type constructor m.

A function of type m a -> (a -> m b) -> m b for chaining the output of
one function into the input of another.

A function of type a -> m a for injecting a normal value into the chain, i.e. it wraps a type a with the type constructor m.

A function of type m a -> (a -> m b) -> m b，这个函数是用于把几个Monad链接起来。例子：

{% highlight haskell %}
(>>) :: Maybe a -> (a -> Maybe b) -> Maybe b
Nothing >> _ = Nothing
Just v  >> f = f v
{% endhighlight %}

(\>\>)就是这样一个函数。函数有两个参数，一个是Maybe a类型，另一个是类型
是a -> Maybe b的函数。返回值是Maybe b。如果第一个参数是Nothing(注意这是
个Monad)，则(\>\>)返回Nothing(也是Monad)；如果第一个参数是Just v，则返回
f v的值，注意f的返回值是Maybe b类型，也是一个Monad。

A function of type a -> m a，这个函数把一个普通的值注入Monad中，相当于
把值用Monad包起来，可以用于Monad操作中。

以下是Monad在Prelude中的定义：

{% highlight haskell %}
class Monad m where
-- chain
(>>=)  :: m a -> (a -> m b) -> m b
-- inject
return :: a -> m a
    (>>) :: m a -> m b -> m b
a >> f = a >>= \_ -> f
{% endhighlight %}

(\>\>=)就是用于链接Monad的函数。return是用于注入的函数。(\>\>)是特殊的链接，
是用(\>\>=)定义的，不同点是(\>\>)会忽略前一个Monad的返回的值。

liftM是一个有用的函数，用于方便地混合pure function和Monad

{% highlight haskell %}
liftM :: (Monad m) => (a -> b) -> m a -> m b
liftM f m = m >>= \i ->
       return (f i)
{% endhighlight %}

liftM接受两个参数，一个pure function，和一个Monad。liftM用这个pure
function对Monad包含的值求值，把结果用Monad包起来并返回。这样就可以将
pure function应用到Monad所包含的值上。


### 二、理解List Monad

Real World Haskell里讲解了List Monad，就是将list作为一个Monad来使用，需要如下定义（已经在GHC.Base中定义）

{% highlight haskell %}
instance Monad [] where
return x = [x]
xs >>= f = concat (map f xs)

xs >> f = concat (map (\_ -> f) xs)
fail _ = []
{% endhighlight %}

List monad和list comprehensive是等价的。以下两者等价：

{% highlight haskell %}
comprehensive xs ys =
  [(x,y) | x <- xs, y <- ys]  monadic xs ys = do { x <- xs; y <- ys; return (x,y) }
{% endhighlight %}

把monadic形式的展开：

{% highlight haskell %}
-- Step 1:
do
  x <- xs
  y <- ys
  return (x, y)

-- Step 2:
xs >>= \x ->
  ys >>= \y ->
    [(x, y)]

-- Step 3:
concat (map (\x ->
  ys >>= \y ->
    [(x, y)])
      xs)

-- Step 4:
concat (map (\x ->
  concat (map (\y -> [(x, y)]) ys ))
    xs)
{% endhighlight %}

实际的效果就是一个二重循环：对xs里每一个元素，都用函数 \x -> ys >>= \y -> [(x, y)] 求值一次。对ys里的每一个元素，都用函数 \y -> [(x, y)] 求值一次。

> You cannot predict how a block of monadic code will behave unless you
> know what monad it will execute in. (Real World Haskell)

在GHC中的运行结果：

{% highlight haskell %}
-- Prelude> monadic [1,2] "bar"

[(1,'b'),(1,'a'),(1,'r'),(2,'b'),(2,'a'),(2,'r')]
{% endhighlight %}
