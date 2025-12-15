# 工件掃描

在過去的兩天中，我們學習了為什麼以及如何掃描容器映像。

然而，通常我們的基礎設施不僅僅是容器映像。
是的，我們的服務將作為容器運行，但在它們周圍我們還可以有其他工件，例如：

- Kubernetes 清單
- Helm 模板
- Terraform 代碼

為了最大安全性，您將掃描用於環境的所有工件，而不僅僅是容器映像。

原因是即使您有最安全的 Docker 映像且沒有 CVE，
但在具有不良 Kubernetes 配置的不安全基礎設施上運行它們，
那麼您的環境將不安全。

**每個系統都與其最薄弱的環節一樣安全。**

今天我們將查看用於掃描除容器映像之外的不同工件的不同工具。

## Kubernetes 清單

掃描 Kubernetes 清單可以暴露錯誤配置和安全不良實踐，例如：

- 以 root 身份運行容器
- 在沒有資源限制的情況下運行容器
- 給容器太多和太強大的功能
- 在模板中硬編碼密鑰等

所有這些都是我們 Kubernetes 工作負載安全態勢的一部分，在安全方面有不良態勢就像在現實生活中有不良態勢一樣糟糕。

一個流行的用於掃描 Kubernetes 清單的開源工具是 [KubeSec](https://kubesec.io/)。

它輸出一系列錯誤配置。

例如，這個來自其文檔的 Kubernetes 清單有很多錯誤配置，如缺少內存限制、以 root 身份運行等。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubesec-demo
spec:
  containers:
    - name: kubesec-demo
      image: gcr.io/google-samples/node-hello:1.0
      securityContext:
        runAsNonRoot: false
```

讓我們掃描它並查看結果。

```shell
$ kubesec scan kubesec-test.yaml
[
  {
    "object": "Pod/kubesec-demo.default",
    "valid": true,
    "message": "Passed with a score of 0 points",
    "score": 0,
    "scoring": {
      "advise": [
        {
          "selector": ".metadata .annotations .\"container.seccomp.security.alpha.kubernetes.io/pod\"",
          "reason": "Seccomp profiles set minimum privilege and secure against unknown threats"
        },
        {
          "selector": ".spec .serviceAccountName",
          "reason": "Service accounts restrict Kubernetes API access and should be configured with least privilege"
        },
        {
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege"
        },
        {
          "selector": ".metadata .annotations .\"container.apparmor.security.beta.kubernetes.io/nginx\"",
          "reason": "Well defined AppArmor policies may provide greater protection from unknown threats. WARNING: NOT PRODUCTION READY"
        },
        {
          "selector": "containers[] .resources .requests .memory",
          "reason": "Enforcing memory requests aids a fair balancing of resources across the cluster"
        },
        {
          "selector": "containers[] .securityContext .runAsUser -gt 10000",
          "reason": "Run as a high-UID user to avoid conflicts with the host's user table"
        },
        {
          "selector": "containers[] .resources .limits .cpu",
          "reason": "Enforcing CPU limits prevents DOS via resource exhaustion"
        },
        {
          "selector": "containers[] .resources .requests .cpu",
          "reason": "Enforcing CPU requests aids a fair balancing of resources across the cluster"
        },
        {
          "selector": "containers[] .securityContext .readOnlyRootFilesystem == true",
          "reason": "An immutable root filesystem can prevent malicious binaries being added to PATH and increase attack cost"
        },
        {
          "selector": "containers[] .securityContext .capabilities .drop",
          "reason": "Reducing kernel capabilities available to a container limits its attack surface"
        },
        {
          "selector": "containers[] .resources .limits .memory",
          "reason": "Enforcing memory limits prevents DOS via resource exhaustion"
        },
        {
          "selector": "containers[] .securityContext .capabilities .drop | index(\"ALL\")",
          "reason": "Drop all capabilities and add only those required to reduce syscall attack surface"
        }
      ]
    }
  }
]
```

正如我們看到的，它產生了 12 個關於此清單中我們想要更改的警告。
每個警告都有一個解釋，告訴我們為什麼需要修復它。

### 其他

其他此類工具包括 [kube-bench](https://github.com/aquasecurity/kube-bench)、[kubeaudit](https://github.com/Shopify/kubeaudit) 和 [kube-score](https://github.com/zegl/kube-score)。

它們以相同或類似的方式工作。
您給它們一個資源進行分析，它們輸出一系列要修復的東西。

它們可以在 CI 設置中使用。
其中一些還可以用作 [Kubernetes 驗證 webhook](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/)，如果資源違反策略，可以阻止創建資源。

## Helm 模板

[Helm](https://helm.sh/) 模板基本上是模板化的 Kubernetes 資源，可以使用不同的值重用和配置。

有一些工具，如 [Snyk](https://docs.snyk.io/products/snyk-infrastructure-as-code/scan-kubernetes-configuration-files/scan-and-fix-security-issues-in-helm-charts)，對掃描 Helm 模板以查找錯誤配置有 *一些* 支持，就像我們掃描 Kubernetes 資源一樣。

然而，解決這個問題的最佳方法是只掃描 Helm charts 的最終模板化版本。
例如，使用 `helm template` 將模板化值替換為實際值，然後通過上面提供的工具掃描它。

## Terraform

用於掃描 Terraform 代碼中錯誤配置的最受歡迎的工具是 [tfsec](https://github.com/aquasecurity/tfsec)。

它使用靜態分析來發現代碼中的潛在問題。

它支持多個雲提供商，並指出特定於您正在使用的提供商的問題。

例如，它有檢查 [在 AWS 中使用默認 VPC](https://aquasecurity.github.io/tfsec/v1.28.1/checks/aws/ec2/no-default-vpc/)，
[在 EC2 用戶數據中硬編碼密鑰](https://aquasecurity.github.io/tfsec/v1.28.1/checks/aws/ec2/no-secrets-in-launch-template-user-data/)，
或 [允許對您的 ECR 容器映像進行公共訪問](https://aquasecurity.github.io/tfsec/v1.28.1/checks/aws/ecr/no-public-access/)。

它允許您通過內聯註釋啟用/禁用檢查並忽略警告。

它還允許您通過 [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/) 定義自己的策略。

## 總結

安全 SDLC 將包括掃描最終進入生產環境的所有工件，而不僅僅是容器映像。

今天我們學習了如何掃描非容器工件，如 Kubernetes 清單、Helm charts 和 Terraform 代碼。
我們查看的工具是免費和開源的，可以整合到任何工作流程或 CI 管道中。
在 [Day 24](day24.md) 見。
