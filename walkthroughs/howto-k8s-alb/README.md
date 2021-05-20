## 概述

本示例会说明如何将App Mesh的虚拟节点(vitual-nodes)注册为[ALB Ingress Controller](https://github.com/kubernetes-sigs/aws-alb-ingress-controller)的目标。

![System Diagram](./howto-k8s-alb.png "System Diagram")

## 前置条件
- [Walkthrough: App Mesh with EKS](../eks/)
- [Walkthrough: ALB Ingress Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v1.1/guide/walkthrough/echoserver/#deploy-the-alb-ingress-controller)
- 安装Docker。 示例需要构建演示应用的Docker image。

注意: 只需要部署 [ALB Ingress Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v1.1/guide/walkthrough/echoserver/#deploy-the-alb-ingress-controller)，示例中服务将替换ALB Ingress Controller链接中的echoserver

## 配置

1. 克隆此仓库，然后进入`walkthrough/howto-k8s-alb`文件夹，所有的命令都是在此文件夹下运行。
2. **你的** account id:
    ```
    export AWS_ACCOUNT_ID=<your_account_id>
    ```
3. **Region** e.g. us-west-2
    ```
    export AWS_DEFAULT_REGION=us-west-2
    ```
4. **(可选项) 指定 Envoy Image 版本** 如果要使用与[默认版本](https://github.com/aws/eks-charts/tree/master/stable/appmesh-controller#configuration)不同的Envoy 容器镜像，运行 `helm upgrade` 去覆盖 `sidecar.image.repository` 和 `sidecar.image.tag` 字段。
5. 部署
    ```.
    ./deploy.sh
    ```

## 使用

检查Ingress的事件，查看变化。

    ```
    kubectl describe ing -n howto-k8s-alb color
    ```

您应该会看到类似于以下内容。

    ```
    Name:             color
    Namespace:        howto-k8s-alb
    Address:          80113f18-howtok8salb-color-0f20-319733316.us-west-2.elb.amazonaws.com
    Default backend:  default-http-backend:80 (<none>)
    Rules:
      Host  Path  Backends
      ----  ----  --------
      *
            /color   color:8080 (192.168.16.27:8080,192.168.59.249:8080)
    Annotations:
      kubectl.kubernetes.io/last-applied-configuration:  {"apiVersion":"extensions/v1beta1","kind":"Ingress","metadata":{"annotations":{"alb.ingress.kubernetes.io/healthcheck-path":"/ping","alb.ingress.kubernetes.io/scheme":"internet-facing","alb.ingress.kubernetes.io/target-type":"ip","kubernetes.io/ingress.class":"alb"},"name":"color","namespace":"howto-k8s-alb"},"spec":{"rules":[{"http":{"paths":[{"backend":{"serviceName":"color","servicePort":8080},"path":"/color"}]}}]}}
      kubernetes.io/ingress.class:                 alb
      alb.ingress.kubernetes.io/healthcheck-path:  /ping
      alb.ingress.kubernetes.io/scheme:            internet-facing
      alb.ingress.kubernetes.io/target-type:       ip
    Events:
      Type    Reason  Age    From                    Message
      ----    ------  ----   ----                    -------
      Normal  CREATE  11m    alb-ingress-controller  LoadBalancer 80113f18-howtok8salb-color-0f20 created, ARN: arn:aws:elasticloadbalancing:us-west-2:669977933099:loadbalancer/app/80113f18-howtok8salb-color-0f20/7c45f6fd4eefa871
      Normal  CREATE  11m    alb-ingress-controller  rule 1 created with conditions [{    Field: "path-pattern",    PathPatternConfig: {      Values: ["/"]    }  }]
      Normal  MODIFY  4m12s  alb-ingress-controller  rule 1 modified with conditions [{    Field: "path-pattern",    PathPatternConfig: {      Values: ["/color"]    }  }]
     ```

检查是否可以通过ALB Ingress Controller访问该应用程序。

```
curl -v 80113f18-howtok8salb-color-0f20-319733316.us-west-2.elb.amazonaws.com/color
```

您应该看到类似于以下内容。

```
*   Trying 34.208.158.34...
* TCP_NODELAY set
* Connected to 80113f18-howtok8salb-color-0f20-319733316.us-west-2.elb.amazonaws.com (34.208.158.34) port 80 (#0)
> GET /color HTTP/1.1> Host: 80113f18-howtok8salb-color-0f20-319733316.us-west-2.elb.amazonaws.com
> User-Agent: curl/7.61.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Sat, 09 May 2020 01:30:06 GMT
< Transfer-Encoding: chunked
< Connection: keep-alive
< server: envoy
< x-envoy-upstream-service-time: 0
<
* Connection #0 to host 80113f18-howtok8salb-color-0f20-319733316.us-west-2.elb.amazonaws.com left intact
blue
```
