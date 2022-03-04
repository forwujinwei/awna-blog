## Postman设置脚本

请求地址按照样例，将msg填为{{msg}}，sign填为{{sign}}：

![img](https://yuanbaoshuke.feishu.cn/space/api/box/stream/download/asynccode/?code=NDUxMDJiZjQ3NWM0ZmRlNjk3NzkxMjhlM2RmYzAzNTlfNTczODVTdktXNEVPTWVpeDZuNkk3aGYwZmtkbUZQR1pfVG9rZW46Ym94Y25UVlg1MDdjb1dWMEFaZmdwVFdxTXZnXzE2NDQ5ODEzNjU6MTY0NDk4NDk2NV9WNA)

Pre-request Script填写：

```
var json = CryptoJS.enc.Utf8.parse('json格式的请求参数');
var msg = CryptoJS.enc.Base64.stringify(json); pm.environment.set('msg', msg);
var todayYear = (new Date()).getFullYear();
var todayMonth = (new Date()).getMonth();
var todayDay = (new Date()).getDate();
var todayTime = (new Date(todayYear, todayMonth, todayDay, '0', '0', '0')).getTime();
var key = "_nsip_play_" + todayTime;
var sign = CryptoJS.MD5(msg + key).toString();
pm.environment.set('sign', sign);
```

