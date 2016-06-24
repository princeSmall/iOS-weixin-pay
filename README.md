# ios-
#1、微信开发平台的开发者账号，即微信Open

#2、注册完你的应用通过审核后，你会获得这个应用的APPID和APPSecret

#3、在开发平台的资源中心，也是文档中心，下载iOS的SDK

#4、拷贝SDK到工程文件夹中，在addfilesto“我的工程”

#5、配置你的工程

/**
     连接微信应用以使用相关功能，此应用需要引用WeChatConnection.framework和微信官方SDK
     http://open.weixin.qq.com上注册应用，并将相关信息填写以下字段
     **/
    
    //    [ShareSDK connectWeChatWithAppId:@"wx4868b35061f87885" wechatCls:[WXApi class]];
    [ShareSDK connectWeChatWithAppId:@"wxa6cb25669b7a1894"
                           appSecret:@"d4624c36b6795d1d99dcf0547af5443d"
                           wechatCls:[WXApi class]];
    

//调起微信支付
-(void) onResp:(BaseResp*)resp
{

    if([resp isKindOfClass:[PayResp class]]){

        //支付返回结果，实际支付结果需要去微信服务器端查询
        switch (resp.errCode) {
            case WXSuccess:{
                [[NSNotificationCenter defaultCenter]postNotificationName:@"微信支付成功" object:nil];
            }
                break;
                
            case WXErrCodeCommon:{

            }
                break;
                
            case WXErrCodeUserCancel:{
 
            }
                break;
                
            default:
                break;
        }
    }
}



+ (void)wxPay{

    NSString *urlString   = @"http://wxpay.weixin.qq.com/pub_v2/app/app_pay.php?plat=ios";
   
 //解析服务端返回json数据
    NSError *error;
   
    //加载一个NSURL对象
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:urlString]];
   
    //将请求的url数据放到NSData对象中
    NSData *response = [NSURLConnection sendSynchronousRequest:request returningResponse:nil error:nil];
    
    if ( response != nil) {
     
        NSMutableDictionary *dict = NULL;
       
        //IOS5自带解析类NSJSONSerialization从response中解析出数据放到字典中
        dict = [NSJSONSerialization JSONObjectWithData:response options:NSJSONReadingMutableLeaves error:&error];
       
        NSLog(@"url:%@",urlString);
       
        NSMutableString *retcode = [dict objectForKey:@"retcode"];
       
        if (retcode.intValue == 0){
            NSMutableString *stamp  = [dict objectForKey:@"timestamp"];
            PayReq* req             = [[PayReq alloc] init];
            req.partnerId           = [dict objectForKey:@"partnerid"];
            req.prepayId            = [dict objectForKey:@"prepayid"];
            req.nonceStr            = [dict objectForKey:@"noncestr"];
            req.timeStamp           = [stamp intValue];
            req.package             = [dict objectForKey:@"package"];
            req.sign                = [dict objectForKey:@"sign"];
            BOOL isSure = [WXApi sendReq:req];
            if (isSure) {
                NSLog(@"okOKO");
            }else{
                NSLog(@"nono");
            }
         
            NSLog(@"appid=%@\npartid=%@\nprepayid=%@\nnoncestr=%@\ntimestamp=%ld\npackage=%@\nsign=%@",[dict objectForKey:@"appid"],req.partnerId,req.prepayId,req.nonceStr,(long)req.timeStamp,req.package,req.sign );
        
        }
    }
}
#6、具体问题还要具体分析，参照微信开发文档，调用正确的API，一般不会出错，细心一点
