# mangdin-thinkphp5.1-alipay2
thinkphp5.1 PC端支付宝支付

将本插件根目录的alipay.php拷贝到项目目录config文件夹中，并修改该文件中的配置为您自己支付接口配置。
相关支付参数可以在https://openhome.alipay.com/platform/appManage.htm#/apps地址中获取

调用支付控制器代码：
   <pre>
    public function pay(){
        $config = Config::get('alipay.'); //支付宝 手机浏览器支付 配置参数

        //商户订单号，商户网站订单系统中唯一订单号，必填
        $out_trade_no = time().rand(00000,99999);

        //订单名称，必填
        $subject = '测试订单';

        //付款金额，必填
        $total_amount = 0.01;

        //商品描述，可空
        $body = '商品藐视信息';

        //构造参数
        $payRequestBuilder = new \mangdin\alipay2\AlipayTradePagePayContentBuilder();
        $payRequestBuilder->setBody($body);
        $payRequestBuilder->setSubject($subject);
        $payRequestBuilder->setTotalAmount($total_amount);
        $payRequestBuilder->setOutTradeNo($out_trade_no);

        $aop = new \mangdin\alipay2\AlipayTradeService($config);

        /**
         * pagePay 电脑网站支付请求
         * @param $builder 业务参数，使用buildmodel中的对象生成。
         * @param $return_url 同步跳转地址，公网可以访问
         * @param $notify_url 异步通知地址，公网可以访问
         * @return $response 支付宝返回的信息
         */
        $response = $aop->pagePay($payRequestBuilder,$config['return_url'],$config['notify_url']);

//输出表单
        var_dump($response);
    }
    </pre>
    
   支付回调控制器代码：
   <pre>
    /**
     * return_url接收页面
     */
    public function return_url(){
        $config = Config::get('alipay.'); //支付宝 手机浏览器支付 配置参数
        $arr=input('get.');
        $alipaySevice = new \mangdin\alipay2\AlipayTradeService($config);
        $result = $alipaySevice->check($arr);

        /* 实际验证过程建议商户添加以下校验。
        1、商户需要验证该通知数据中的out_trade_no是否为商户系统中创建的订单号，
        2、判断total_amount是否确实为该订单的实际金额（即商户订单创建时的金额），
        3、校验通知中的seller_id（或者seller_email) 是否为out_trade_no这笔单据的对应的操作方（有的时候，一个商户可能有多个seller_id/seller_email）
        4、验证app_id是否为该商户本身。
        */
        if($result) {//验证成功
            /////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
            //请在这里加上商户的业务逻辑程序代码

            //——请根据您的业务逻辑来编写程序（以下代码仅作参考）——
            //获取支付宝的通知返回参数，可参考技术文档中页面跳转同步通知参数列表

            //商户订单号
            $out_trade_no = htmlspecialchars($arr['out_trade_no']);

            //支付宝交易号
            $trade_no = htmlspecialchars($arr['trade_no']);

            echo "验证成功<br />支付宝交易号：".$trade_no;

            //——请根据您的业务逻辑来编写程序（以上代码仅作参考）——

            /////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        }
        else {
            //验证失败
            echo "验证失败";
        }
    }

    /**
     * notify_url接收页面
     */
    public function notify_url(){
        $config = Config::get('alipay.'); //支付宝 手机浏览器支付 配置参数
        $arr=input('post.');
        $alipaySevice = new \mangdin\alipay2\AlipayTradeService($config);
        $alipaySevice->writeLog(var_export(input('post.'),true));
        $result = $alipaySevice->check($arr);

        /* 实际验证过程建议商户添加以下校验。
        1、商户需要验证该通知数据中的out_trade_no是否为商户系统中创建的订单号，
        2、判断total_amount是否确实为该订单的实际金额（即商户订单创建时的金额），
        3、校验通知中的seller_id（或者seller_email) 是否为out_trade_no这笔单据的对应的操作方（有的时候，一个商户可能有多个seller_id/seller_email）
        4、验证app_id是否为该商户本身。
        */
        if($result) {//验证成功
            /////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
            //请在这里加上商户的业务逻辑程序代


            //——请根据您的业务逻辑来编写程序（以下代码仅作参考）——

            //获取支付宝的通知返回参数，可参考技术文档中服务器异步通知参数列表

            //商户订单号

            $out_trade_no = $arr['out_trade_no'];

            //支付宝交易号

            $trade_no = $arr['trade_no'];

            //交易状态
            $trade_status = $arr['trade_status'];


            if($arr['trade_status'] == 'TRADE_FINISHED') {

                //判断该笔订单是否在商户网站中已经做过处理
                //如果没有做过处理，根据订单号（out_trade_no）在商户网站的订单系统中查到该笔订单的详细，并执行商户的业务程序
                //请务必判断请求时的total_amount与通知时获取的total_fee为一致的
                //如果有做过处理，不执行商户的业务程序

                //注意：
                //退款日期超过可退款期限后（如三个月可退款），支付宝系统发送该交易状态通知
            }
            else if ($arr['trade_status'] == 'TRADE_SUCCESS') {
                //判断该笔订单是否在商户网站中已经做过处理
                //如果没有做过处理，根据订单号（out_trade_no）在商户网站的订单系统中查到该笔订单的详细，并执行商户的业务程序
                //请务必判断请求时的total_amount与通知时获取的total_fee为一致的
                //如果有做过处理，不执行商户的业务程序
                //注意：
                //付款完成后，支付宝系统发送该交易状态通知
            }
            //——请根据您的业务逻辑来编写程序（以上代码仅作参考）——
            echo "success";	//请不要修改或删除
        }else {
            //验证失败
            echo "fail";

        }
    }
    </pre>
