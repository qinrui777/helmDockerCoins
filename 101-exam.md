Kubernetes 101 结业测试


### 使用helm编排帮助docker币开发团队完成,应用在dev,uat,prod环境部署
- 需要使用helm完成两个charts
  - Dev,uat的helm charts传递服务名和镜像版本用于开发流程中部署单个服务
  - Prod环境的helm charts 编排整个docker币所有服务用于prod环境发布
- 其他需求
  - 在k8s环境创建三个namespace, dev,uat,prod模拟三个环境
  - Uat和prod环境部署应用需要有k8s健康检查,liveness,readiness
  - 需要编排的资源至少但不限于 ingress,service,deployment,daemonsets
  - Worker,hasher应用使用deployment部署,rng应用使用 daemonsets部署
  - dev,uat环境redis部署在k8s上,prod环境redis服务部署在192.168.33.101服务器上
  - 应用访问地址为
    - dev-webui.192.168.33.111.nip.io(DEV)
    - uat-webui.192.168.33.111.nip.io(UAT)
    - webui.192.168.33.111.nip.io(PROD)
  - 所有k8s资源包含 labels
    projectname=dockercoins,environment=<环境名称>,servicename=<服务名称>
  - Deployment的k8s资源包含环境变量,
    PROJECT_NAME=dockercoins,ENVIRONMENT=<环境名称>,SERVICE_NAME=<服务名称>
  - helm test (可选)
  - 建立一个github仓库保存helm charts,提交仓库地址
