# 運行時期的網路安全

網路安全在運行時期保護的背景下至關重要，因為它旨在保護應用程式或系統不同組件之間的通信通道。在現代基於微服務的架構中，應用程式由多個較小的服務組成，這些服務透過網路相互通信，保護網路變得更加重要。

網路安全在這種背景下至關重要的主要原因之一是防止攻擊者利用網路漏洞，在系統中擴散並獲得對敏感數據或系統的未授權訪問。例如，如果攻擊者成功攔截兩個微服務之間的通信，他們可以輕鬆竊聽它們之間交換的敏感資訊，甚至操縱數據，可能導致重大的安全漏洞。

此外，網路安全還有助於防範分散式拒絕服務（DDoS）攻擊，攻擊者用大量請求或流量壓垮網路，使應用程式或系統無法使用。通過實施網路安全控制措施，如防火牆、入侵檢測/防禦系統（IDS/IPS）和負載平衡器，組織可以降低 DDoS 攻擊的風險並確保其服務的可用性。

此外，網路安全對於符合行業和監管標準至關重要，例如支付卡行業數據安全標準（PCI DSS）、通用數據保護條例（GDPR）和健康保險流通與責任法案（HIPAA）。這些標準要求在網路傳輸過程中保護敏感數據，組織必須實施適當的安全控制措施以符合這些標準。

## 網路安全基礎

網路安全是一個廣泛的領域，但重要的是要涵蓋基礎知識，以便每個人都能保持一致。這可能包括網路拓撲、網路協議（如 TCP/IP）、防火牆以及訪問控制機制（如 ACL 和 RBAC）等主題。您還應該涵蓋 OSI 模型，這是一個描述網路協議如何工作的概念模型。理解 OSI 模型對於故障排除網路問題和實施網路安全措施至關重要。

## Kubernetes 網路架構

Kubernetes 使用複雜的網路架構來管理 Pod 和 Service 之間的網路流量。理解這個架構對於保護它很重要。在高層次上，Kubernetes 網路由集群範圍的 Pod 網路和 Service 網路組成。Pod 網路允許 Pod 相互通信，而 Service 網路為一組 Pod 提供穩定的 IP 地址和 DNS 名稱。Kubernetes 還支援 Ingress，用於將外部流量路由到集群中。Ingress 通常使用 TLS 加密和客戶端身份驗證來保護。

## 網路策略

Kubernetes 網路策略允許您控制 Pod 和 Service 之間的網路流量。您可以使用網路策略來限制集群的攻擊面，例如阻止來自不屬於您應用程式的 Pod 的流量。網路策略使用選擇器來指定它們應用於哪些 Pod 或 Service，並且可以根據各種標準（如 IP 地址、端口號或協議）配置為允許或拒絕流量。

## 容器網路

容器預設情況下彼此隔離，但它們仍然可以透過網路通信。容器網路可能很複雜，特別是在使用像 Kubernetes 這樣的容器編排平台時。理解容器網路的工作原理對於保護它很重要。Kubernetes 使用各種網路插件來啟用 Pod 之間的通信，如 Calico、Weave Net 和 Flannel。這些插件可以配置為使用不同的網路模式，如主機網路、橋接網路或覆蓋網路。

## 服務網格安全

像 Istio 和 Linkerd 這樣的服務網格在 Kubernetes 環境中變得越來越受歡迎。服務網格提供高級流量管理功能，如負載平衡、流量整形和斷路器。它們還提供安全功能，如相互 TLS 身份驗證、RBAC 和流量管理。理解服務網格安全的原則很重要，包括如何配置和管理這些功能。

## 網路監控和日誌記錄

網路監控和日誌記錄對於檢測和響應安全事件至關重要。您應該涵蓋 Kubernetes 中網路監控和日誌記錄的最佳實踐，包括使用 Falco、Prometheus 和 Grafana 等工具。Falco 是一個運行時安全工具，可用於檢測 Kubernetes 中的可疑行為，而 Prometheus 和 Grafana 可用於監控和告警。

## 零信任網路

零信任網路是一種安全模型，假設所有網路流量都是不可信的，並要求對每個連接進行身份驗證和授權。零信任網路可以在 Kubernetes 中使用像 Istio 這樣的工具來實施，它提供相互 TLS 身份驗證和 RBAC 功能。您應該涵蓋零信任網路的原則，包括如何在 Kubernetes 中實施它，以及它如何幫助改善您環境中的安全性。

# Kubernetes 原生網路策略

Kubernetes 原生網路策略是一種用於實施網路分段和控制 Kubernetes 集群中 Pod 之間通信的機制。網路策略允許管理員定義規則，指定哪些 Pod 可以根據其標籤相互通信。

網路策略使用標籤來選擇 Pod 並定義規則以允許或拒絕它們之間的流量。標籤可以應用於 Pod 本身或它們關聯的 Service。預設情況下，Kubernetes 拒絕 Pod 之間所有傳入和傳出的網路流量，網路策略允許管理員選擇性地開放特定 Pod 或 Pod 組之間的通信。

在網路安全的背景下，入口（ingress）和出口（egress）策略用於控制進入和離開網路或特定網路段的流量。在 Kubernetes 中，這些策略使用網路策略來實施。

### 入口

入口策略指定控制進入網路或網路段的流量的規則。這些策略定義允許進入網路的流量類型、來源以及通過哪些端口。在 Kubernetes 中，入口策略應用於特定的 Pod 或命名空間集合，可用於允許或拒絕來自其他 Pod 或外部來源的傳入流量。

### 出口

另一方面，出口策略控制從網路或網路段發出的流量。出口策略用於限制允許離開網路的流量類型以及目標。在 Kubernetes 中，出口策略也應用於特定的 Pod 或命名空間集合，可用於允許或拒絕發送到其他 Pod 或外部目標的傳出流量。

### 創建策略

要創建網路策略，您首先需要創建一個定義策略的 YAML 檔案。YAML 檔案應指定策略應用於的 Pod 或 Pod 選擇器，以及允許或拒絕它們之間流量的規則。以下是一個網路策略的 YAML 檔案範例，允許標籤為 `app=frontend` 的 Pod 之間的流量，並拒絕所有其他 Pod 的流量：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: frontend-policy
spec:
  podSelector:
    matchLabels:
      app: frontend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
```

在這個範例中，`podSelector` 部分指定策略應用於所有標籤為 `app=frontend` 的 Pod。`policyTypes` 部分指定策略應用於傳入流量（Ingress），入口部分指定所有 Pod 可以接收來自匹配 `podSelector` 的 Pod 的流量。

創建 YAML 檔案後，您可以使用 kubectl apply 命令將網路策略應用到您的 Kubernetes 集群：

```bash
kubectl apply -f <filename>.yaml
```

應用網路策略後，流量將根據策略中定義的規則在 Pod 之間受到限制。Kubernetes 網路策略由網路插件執行，這可能因運行 Kubernetes 的平台和環境而異。一些支援 Kubernetes 網路策略的流行網路插件包括 Calico、Cilium 和 Weave Net。請注意，如果您想在 Minikube 上測試此功能，您需要啟用 Calico CNI（`minikube start --cni calico`），因為預設的 CNI 實現不支援它。

總而言之，Kubernetes 原生網路策略是一種用於實施網路分段和控制 Kubernetes 集群中 Pod 之間通信的機制。網路策略使用標籤來選擇 Pod 並定義規則以允許或拒絕它們之間的流量。您可以通過定義指定策略規則的 YAML 檔案來創建網路策略，然後使用 kubectl apply 命令將策略應用到您的集群。

# Kubernetes 中的網路監控

有多種工具可用於監控 Kubernetes 中的網路流量，每種工具都有自己的功能和特性。以下是一些可用於監控 Kubernetes 中網路流量的流行工具：

* Cilium：Cilium 是一個強大的 Kubernetes 網路和安全解決方案，為您的 Kubernetes 集群提供可觀測性、安全性和網路服務。Cilium 可以在容器和 Pod 級別監控網路流量，並提供 Pod 和 Service 之間流量流的詳細洞察。Cilium 還提供網路策略，並可以使用 eBPF 技術在內核級別執行它們。

* Istio：Istio 是 Kubernetes 的服務網格，為您的 Kubernetes 應用程式提供流量管理、可觀測性和安全功能。Istio 包括一個名為 Envoy 的強大邊車代理，可以捕獲和監控 Kubernetes Service 之間的網路流量，並提供豐富的可觀測性功能，如分散式追蹤和指標。

* Calico：Calico 是一個流行的 Kubernetes 網路和安全解決方案，為您的 Kubernetes 集群提供網路策略、可觀測性和安全功能。Calico 包括一個強大的網路策略引擎，允許您在 Pod 和命名空間級別定義和執行策略，並提供網路流量流的詳細洞察。

* Weave Scope：Weave Scope 是一個 Kubernetes 監控和可視化工具，允許您即時監控 Kubernetes 網路和應用程式。Weave Scope 包括一個網路流量視圖，允許您可視化 Kubernetes Service 和 Pod 之間的流量流，並提供對 Kubernetes 基礎設施的豐富洞察。

這些只是可用於監控 Kubernetes 中網路流量的許多工具中的幾個範例。選擇工具時，請考慮應用程式和基礎設施的特定需求，並選擇提供最適合您需求的功能和能力的工具。

請參閱 [Day 32](day32.md)。
