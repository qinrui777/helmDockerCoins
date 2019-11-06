### 第5次  使用ingress和service，为webui应用配置蓝绿部署

- 1.更新webui代码 修改标题 表示新版本， 打包docker镜像为 v0.2 版

```bash
#做一个新镜像，修改前端页面的显示
cp -r webui webui_v2
cd webui_v2
vim index.html
docker build -t webui:v2.0 .
docker tag webui:v2.0 127.0.0.1:30587/webui:v2.0
docker push 127.0.0.1:30587/webui:v2.0
```

- 2.使用v0.2 版本镜像，创建一个新的deployment 名为 webui-blue， 创建 service 名为 webui-blue-svc， 创建ingress host地址为 webui-blue.192.168.33.101.nip.io 指向service
```bash
#创建deploy: webui-blue
kubectl run webui-blue --image=127.0.0.1:30587/webui:v2.0

#创建service
kubectl expose deploy/webui-blue --type=NodePort --port=80

#创建 webui-blue ingress
cp webui-ingress.yaml webui-blue-ingress.yaml

sed -i 's/webui/webui-blue/g' webui-blue-ingress.yaml  

kubectl apply -f /vagrant/apps/kubercoins/webui-blue-ingress.yaml
```

- 3.测试 webui-blue.192.168.33.101.nip.io 应用正常后  
`浏览器打开 webui-blue.192.168.33.101.nip.io 查看下`

- 4.执行发布，修改旧的webui.192.168.33.101.nip.io 线上版本的ingress，使其指向 webui-blue-svc 的service
```bash
kubectl edit ingress webui-ingress


      - backend:
          serviceName: webui
          servicePort: 80
↓↓↓          
      - backend:
          serviceName: webui-blue
          servicePort: 80        
```
- 5.测试 webui.192.168.33.101.nip.io 发布正常  
`浏览器打开 webui.192.168.33.101.nip.io 查看下`

回到解放前
```bash
kubectl delete deploy webui-blue
kubectl delete svc webui-blue
kubectl delete ingress webui-blue-ingress
kubectl edit ingress webui-ingress
```

> 介绍：
https://www.zhihu.com/question/50713290
http://webui.192.168.33.101.nip.io/index.html
http://192.168.33.101:8080/dashboard/




---
新建pod
`kubectl run webuiv2 --image=127.0.0.1:30587/webui:v2.0`

`kubectl label pods webuiv2-7bd65cc95d-2fpj2 run=webui --overwrite`  //会新加一个带有新标签的pod



添加 label version=xx
```bash
kubectl label pods webui-746df859ff-z5fwt version=v1
kubectl label pods webuiv2-7bd65cc95d-2fpj2 version=v2
```

减label 
修改 service 配置
`kubectl edit svc webui`

添加 `version: v2`
