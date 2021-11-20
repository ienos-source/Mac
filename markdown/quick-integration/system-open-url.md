# System Open URL

```

UIApplication *application = [UIApplication sharedApplication];

/// 发短信
[application openURL:[NSURL URLWithString:@"sms://10086"]];

/// 打电话
[application openURL:[NSURL URLWithString:@"tel://15018615033"]];

/// 发邮件
[application openURL:[NSURL  URLWithString:@"mailto: liuhaiyang@lanou3g.com"]];

/// 打开网址
[application openURL:[NSURL URLWithString:@"http://www.baidu.com"]];

/// 打开appstore并且链接到某个应用程序 / 打开第三方应用程序
[application openURL:[NSURL URLWithString:@""]];

```
