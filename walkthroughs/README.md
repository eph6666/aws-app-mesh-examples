# App Mesh walkthroughs

The following end-to-end walkthroughs show you AWS App Mesh in action, across different compute environments, and demonstrate integrations with other AWS services such as CloudWatch and X-Ray.

## Series

The following are part of an orientation series that build on the Color App.

1. [ECS](../examples/apps/colorapp/)
2. [Fargate](./fargate/)
3. Coming next: EC2 ([pull request](https://github.com/aws/aws-app-mesh-examples/pull/102))

## EKS

The following articles are focused specifically on EKS.

* [Walkthrough: App Mesh with EKS](./eks/)

## HOWTO

The following articles demonstrate how to use an App Mesh feature

* [Service Discovery with AWS Cloud Map](./howto-servicediscovery-cloudmap)
* [HTTP Header Based Routing and Route Priority](./howto-http-headers)
* [HTTP Retry Policy](./howto-http-retries)


## 汉化

[Stage-1]

- [ ] eks (yfwang)
- [x] howto-k8s-http-headers (yfwang)
- [ ] howto-k8s-retry-policy (yfwang)
- [ ] howto-k8s-timeout-policy (yfwang)
- [ ] howto-k8s-outlier-detection (pingfan)
- [ ] howto-k8s-connection-pools (pingfan)
- [ ] howto-k8s-grpc (pingfan)
- [ ] howto-k8s-ingress-gateway (pingfan)
- [ ] howto-k8s-egress (pingfan)
- [ ] howto-k8s-tls-acm (pingfan)
- [ ] howto-k8s-cloudmap (pingfan)
- [x] howto-k8s-alb (yfwang)

[Stage-2]

- [ ] howto-k8s-cross-cluster
- [ ] howto-k8s-fargate
- [ ] howto-k8s-http2
- [ ] howto-k8s-mtls-file-based
- [ ] howto-k8s-mtls-sds-based
- [ ] howto-k8s-tls-file-based