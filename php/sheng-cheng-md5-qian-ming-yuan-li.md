# 生成MD5签名原理
PHP 生成MD5签名 并 测试可用性的代码
## MD5是没办法反向破解的

在讲解这个之前一定要了解一个东西：MD5是没办法反向破解的，如果你不了解这个那这原理理解起来有点难，当然一些简单的组合通过一些网站的暴力破解，但我们的数据都是32甚至更多的MD5加密，所以这是几乎不能破解的。了解了这一点，你才能不在这个原理上走弯路。

## 双方约定：

服务方一般会给请求方一个appid和appkey， appid是请求方请求的唯一标识，这个是可以在网络上传输的，而appkey是不在网络上传输的。

## 请求方：

请求方如果需要请求，他会把请求的数据（这个请求数据中一定包含appid）按一定的规则（这个规则是双方规定好的一个算法）进行过滤排序，然后把过滤排序后的参数加上appkey进行MD5加密码（这也就是我们说的签名），然后再把请求的参数加上签名一起传给服务方。

## 服务方：

服务方收到请求，会首先把请求参数的不需要或空的参数值过滤掉，然后按照同样的算法进行过滤排序，然后拿出请求中的appid，在服务器数据库搜索中appid对应的appkey，然后把参数加上appkey进行MD5加密同样生成签名，这时拿这个签名去与请求方传的参数中的签名对比，如果相等，签名成功。

## 注意点：

在整个过程中其实就是参数加appkey进行MD5加密后的一个对比，但是appkey是不在网络上传输的，是请求方和服务方分别在本地保存的一个同样的标识，这样可以起到防止网络数据劫持的危险。

核心程序：

里面只写了md5的程序，rsa的略。

{% code-tabs %}
{% code-tabs-item title="md5\_sign.php" %}
```php
<?php
/**
 * 资料来源：支付宝
 * 签名：所有交互的数据都需要签名，除了 sign(签名)，和 sign_type(签名方式：md5,rsa)
 * md5签名
 * 1.私钥：只有你和服务器知道，用于加密数据，由英文字母和数据组成，共32位
 * 2.请求时签名：$sign=md5((待签名字符串.md5私钥));
 * 3.返回时验证：$_POST['sign']==md5((待验证字符串,md5私钥))
 * rsa签名
 * 1.私钥与公钥：openssl生成
 * 2.请求时签名：$sign = rsaSign(待签名字符串,私钥);
 * 3.返回时验证：$_POST['sign'] == rsaVerify(待验证字符串,公钥);
 */
 
/**
 * 演示md5签名过程
 */
 
/**
 * 除去数组中的空值和签名参数
 * @param $para 签名参数组
 * return 去掉空值与签名参数后的新签名参数组
 */
function paraFilter($para) {
    $para_filter = array();
    while (list ($key, $val) = each ($para)) {
        if($key == "sign" || $key == "sign_type" || $val == "")continue;
        else    $para_filter[$key] = $para[$key];
    }
    return $para_filter;
}
/**
 * 对数组排序
 * @param $para 排序前的数组
 * return 排序后的数组
 */
function argSort($para) {
    ksort($para);
    reset($para);
    return $para;
}
/**
 * 把数组所有元素，按照“参数=参数值”的模式用“&”字符拼接成字符串
 * @param $para 需要拼接的数组
 * return 拼接完成以后的字符串
 */
function createLinkstring($para) {
    $arg  = "";
    while (list ($key, $val) = each ($para)) {
        $arg.=$key."=".$val."&";
    }
    //去掉最后一个&字符
    $arg = substr($arg,0,count($arg)-2);
    //如果存在转义字符，那么去掉转义
    if(get_magic_quotes_gpc()){
        $arg = stripslashes($arg);
    }
    return $arg;
}
/**
 * 生成md5签名字符串
 * @param $prestr 需要签名的字符串
 * @param $key 私钥
 * return 签名结果
 */
function md5Sign($prestr, $key) {
    $prestr = $prestr . $key;
    return md5($prestr);
}
 
class Submit{
    public $_sign_type ='';
    public $_md5_key = '';
    public $_private_key_path = '';
 
    public function __construct($sign_type){
        $this->_sign_type = strtoupper($sign_type);
    }
    /**
     * 对参数进行过滤，然后排序
     * @param array $para_temp
     */
    function filterPara($para_temp){
        $para_filter = paraFilter($para_temp);//除去待签名参数数组中的空值和签名参数
        return argSort($para_filter);//对待签名参数数组排序
    }
    /**
     * 生成签名结果
     * @param $para_sort 已排序要签名的数组
     * @return string 签名结果字符串
     */
    function buildRequestMysign($para_sort) {
        //把数组所有元素，按照“参数=参数值”的模式用“&”字符拼接成字符串
        $prestr = createLinkstring($para_sort);
        $mysign = "";
        switch ($this->_sign_type) {
            case "MD5":$mysign = md5Sign($prestr, $this->_md5_key);break;
            case "RSA" :$mysign = rsaSign($prestr, $this->_private_key_path);break;
            default :$mysign = "";
        }
        return $mysign;
    }
    /**
     * 生成要发送给支付宝的参数数组
     * @param $para_temp 请求前的参数数组
     * @return 要请求的参数数组
     */
    public function buildRequestPara($para_temp) {
        $para_sort = $this->filterPara($para_temp);//对待签名参数进行过滤
        $para_sort['sign'] = $this->buildRequestMysign($para_sort);//生成签名结果，并与签名方式加入请求提交参数组中
        $para_sort['sign_type'] = strtoupper(trim($this->_sign_type));
        return $para_sort;
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

测试数据：

{% code-tabs %}
{% code-tabs-item title="md5\_test\_data.php" %}
```php
//1.准备要提交的字符串
$para_token = array(
        'title'=> 'app',
        'money'=>'5.60',
);
$md5 = new Submit('md5');
// 2.准备md5私钥
$md5->_md5_key =  'd41d8cd98f00b204e9800998ecf8427e';
// 3.生成要提交的数据
$a=$md5->buildRequestPara($para_token);
echo "<pre>";
print_r($a);
 
//打印结果：Array ( [money] => 5.6 [title] => app [sign] => 9ae9554fac509eea96e6e8efba6846d5 [sign_type] => MD5 )
 
//4.假设现在服务器返回以下数据，如何验证此数据是由服务发送的
$return = array(
        'title'=> 'app',
        'money'=>'5.60',
        'sign'=>'9ae9554fac509eea96e6e8efba6846d5',
        'sign_type'=>'MD5'
        );
    //5.按照加密流程：过滤数数组->把数组转为字符串->生成签名，检查是否生成同样的签名
    $md5_vertiry = new Submit('md5');
    $md5_vertiry->_md5_key =  'd41d8cd98f00b204e9800998ecf8427e';
    $return = $md5_vertiry->filterPara($return);
    echo $md5_vertiry->buildRequestMysign($return)."<br/>";
    //如果生成的签名相等，即验证成功
     
    if($a['sign']==$md5_vertiry->buildRequestMysign($return)){
        echo "验证成功";
    }else{
        echo "验证失败";
    }

```
{% endcode-tabs-item %}
{% endcode-tabs %}



