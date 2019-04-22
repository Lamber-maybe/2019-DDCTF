# 滴~
## 第一步：分析URL
观察url，对其进行解码，发现是文件包含。直接读index.php查看源码
![Alt text](./1555401396176.png)
![Alt text](./1555401688259.png)

```php
<?php
/*
 * https://blog.csdn.net/FengBanLiuYun/article/details/80616607
 * Date: July 4,2018
 */
error_reporting(E_ALL || ~E_NOTICE);


header('content-type:text/html;charset=utf-8');
if(! isset($_GET['jpg']))
    header('Refresh:0;url=./index.php?jpg=TmpZMlF6WXhOamN5UlRaQk56QTJOdz09');
$file = hex2bin(base64_decode(base64_decode($_GET['jpg'])));
echo '<title>'.$_GET['jpg'].'</title>';
$file = preg_replace("/[^a-zA-Z0-9.]+/","", $file);
echo $file.'</br>';
$file = str_replace("config","!", $file);
echo $file.'</br>';
$txt = base64_encode(file_get_contents($file));

echo "<img src='data:image/gif;base64,".$txt."'></img>";
/*
 * Can you find the flag file?
 *
 */

?>
```

## 第二步：社工（干死出题人的脑洞）
观察源代码的注释部分，题目给了一篇博客地址，然后给了一个日期。前往那个博客，在博主的主页发现了的确有一篇在2018.7.4日发的博客。然后找到一个文件名，读他！(注意最开头的那个点号是不加的)。得到`f1ag!ddctf.php`
![Alt text](./1555402429440.png)

## 第三步：分析源码
拿到`f1ag!ddctf.php`之后，我们慢下来分析一下第一步拿到的源码。发现源码中把所有的非数字、字母、点号全部过滤掉了。然后有个字符替换将`!`替换为`config`。所以我们读`f1agconfigddctf.php`即可，拿到源码
```php
<?php
include('config.php');
$k = 'hello';
extract($_GET);
if(isset($uid))
{
    $content=trim(file_get_contents($k));
    if($uid==$content)
	{
		echo $flag;
	}
	else
	{
		echo'hello';
	}
}
?>
```
发现代码中利用`file_get_contents`函数读取字符串，其返回值应该为NULL，那么如果uid也为NULL则能使其相等
![Alt text](./1555403325750.png)
flag: DDCTF{436f6e67726174756c6174696f6e73}
