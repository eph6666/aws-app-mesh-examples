# App Mesh在EKS上的可观测性: Jaeger

注意：在开始本部分之前，请确保已完成带有EKS的App Mesh的[环境搭建](base.md)。 换句话说，以下假设已配置了App Mesh的EKS群集可用，并且满足先决条件（aws，kubectl，jq等）。

Jaeger是一个端到端的分布式跟踪系统，它使用户可以监视复杂的分布式系统中的事务并进行故障排除。

本指南使用[基于HTTP header路由的示例程序](https://github.com/aws/aws-app-mesh-examples/tree/master/walkthroughs/howto-k8s-http-headers)来展示使用Jaeger进行的分布式跟踪。

### 安装

#### 选项 1: 快速开始

App Mesh提供了基本安装，可使用Helm快速设置Jaeger。要安装可与App Mesh一起使用的预先配置为Jaeger，请按照[appmesh-jaeger](https://github.com/aws/eks-charts/blob/master/stable/appmesh-jaeger/README.md) Helm charts中的说明进行操作。

注意：启用跟踪后，您将需要 _重启_ 网格内的所有正在运行的Pod，以便Envoy边车可以选择跟踪配置

#### 选项 2: 使用已经存在的Jaeger

如果您已经部署了Jaeger，则只需使用以下命令配置App Mesh Kubernetes controller即可将跟踪发送到现有Jaeger中：

```
helm upgrade -i appmesh-controller eks/appmesh-controller \
    --namespace appmesh-system \
    --set tracing.enabled=true \
    --set tracing.provider=jaeger \
    --set tracing.address=<JAEGER_ENDPOINT_ADDR / JAEGER_SERVICE_NAME> \
    --set tracing.port=<JAEGER_ENDPOINT_PORT>
```

App Mesh将配置Envoy生成[Zipkin HTTP JSON v2 format](https://www.jaegertracing.io/docs/1.16/apis/#zipkin-formats-stable)格式得跟踪信息。使用的确切跟踪配置是：
```
tracing:
 http:
  name: envoy.tracers.zipkin
  typed_config:
   "@type": type.googleapis.com/envoy.config.trace.v2.ZipkinConfig
   collector_cluster: jaeger
   collector_endpoint: "/api/v2/spans"
   collector_endpoint_version: HTTP_JSON
   shared_span_context: false
```


### 使用

对于测试或演示环境（选项1安装），您可以使用端口转发到Jaeger：

```
kubectl -n appmesh-system port-forward svc/appmesh-jaeger 16686:16686
```

访问Jaeger UI: http://localhost:16686/

您可以在左上角的下拉列表中查看服务列表，并查看其相应的跟踪记录：

![Jaeger traces](jaeger-traces-0.png)

您可以在“Dependencies”选项卡下看到依赖关系图：

![Jaeger service dependency graph](jaeger-traces-1.png)


### 清理环境

```
helm delete appmesh-jaeger -n appmesh-system
```