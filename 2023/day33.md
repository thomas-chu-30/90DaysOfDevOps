# 應用程式運行時策略

## 簡介

應用程式運行時策略是一組規則和控制措施，決定應用程式在運行時的行為。實施這些策略是為了確保應用程式安全地運行並在其預期用途的約束範圍內。

一些常見的運行時策略範例包括訪問控制和網路限制。訪問控制決定誰可以訪問應用程式的各個部分以及他們擁有的訪問級別。應用程式策略規定應用程式可以消耗哪些系統資源。網路限制可以控制應用程式可以訪問哪些網路資源，並限制它可以發送或接收的網路流量類型。

運行時策略可以通過多種方式實施，包括使用安全框架、應用程式特定的配置設置或專門的安全軟體。例如，一些 Web 應用程式防火牆（WAF）旨在通過分析傳入流量並根據預定義的策略阻止或允許請求來為 Web 應用程式執行運行時策略。

有效實施應用程式運行時策略可以幫助降低安全風險並確保應用程式按預期運行。通過在運行時監控和執行策略，組織可以降低未授權訪問、數據洩露和其他可能導致關鍵資產損失或損壞的安全事件的發生可能性。此外，應用程式運行時策略可以幫助確保應用程式以最佳方式運行，並可以在不中斷或意外行為的情況下提供其預期功能。

定義和維護應用程式運行時策略可能是一項具有挑戰性的任務，原因如下：

1. 現代應用程式可能非常複雜，具有多個組件和依賴項，使得難以定義一套涵蓋所有可能情況的清晰策略。將微服務架構添加到這種複雜性中，甚至增加了挑戰。

2. 快速變化的應用程式和技術：技術在不斷發展，新的應用程式和平台定期開發和更新。這意味著曾經有效的策略可能很快變得過時，需要頻繁更新。

3. 應用程式策略必須在提供強大的安全措施和不阻礙用戶生產力之間取得平衡。這可能是一個微妙的平衡，因為過於嚴格的策略可能使用戶難以執行他們的工作，而過於寬鬆的策略可能使系統容易受到安全漏洞的影響。

4. 實施和執行應用程式策略可能需要大量資源，包括時間、金錢和專業知識。組織可能面臨預算和人員限制，使得難以完全實施和維護應用程式策略。

在本節中，我們將看到如何實施應用程式和網路策略，並看到一個有趣的方法：使用監控應用程式行為來生成策略。

## Kubernetes Pod 安全上下文

讓我們啟動一個新的 Minikube（預期下一部分，我們已經使用實現網路策略的 CNI 創建它）。
```bash
minikube start --cni cilium
```

讓我們以一個簡單的 Nginx Web 伺服器為例。我們希望使其比容器的預設設置更安全。Kubernetes 和容器運行時提供了一個選項來創建 `securityContext` 配置，該配置在不同方面限制容器運行時：

### 容器的用戶和組 ID

容器預設使用用戶和組 `0`（root）。主機上的 root 與容器中的 root 不同。定義這兩者之間的差異超出了我們的範圍，但簡而言之，容器化的 root 受到容器邊界的限制。儘管如此，能夠穿透以 root 身份運行的容器的攻擊比以非 root 身份運行的容器具有更大的容器逃逸攻擊面。

因此，在 `securityContext` 中定義一個不同於 root 的用戶 ID 很重要。**注意：** 您必須確保容器構建為以非 root 身份運行。以非 root 用戶身份運行 `nginx:latest` 將失敗，因為容器映像中的檔案權限是為 root 用戶構建的。

以下是在 Kubernetes 中創建不以 root 身份運行的 Nginx 實例的範例。

```bash
kubectl apply -f - << EOF
apiVersion: v1
kind: Pod
metadata:
  name: nginx-non-root
spec:
  containers:
  - name: nginx
    image: nginxinc/nginx-unprivileged
    securityContext:
        runAsUser: 1000
        runAsGroup: 1000
        allowPrivilegeEscalation: false

EOF
```

請注意 `runAsUser` 和 `runAsGroup` 欄位，我們在其中限制運行此容器的用戶 ID。這裡還設置了一個名為 `allowPrivilegeEscalation` 的附加欄位。它移除了容器內運行的進程使用[粘滯位](https://en.wikipedia.org/wiki/Sticky_bit)提升權限的能力。

### 運行時系統調用策略

預設情況下，每個進程都被允許使用內核的任何系統調用。內核可能會根據其邏輯決定不完成這些系統調用，但一般來說，有 500+ 個系統調用可供應用程式使用。

如果攻擊者穿透了容器，她/他可以嘗試使用所有這些系統調用來欺騙內核逃逸容器或造成其他損害。實際上，容器化應用程式只使用 500+ 個系統調用中的有限數量，通常它們不太容易受到漏洞的影響。因此，限制容器化應用程式可以執行的系統調用是一個很好的防禦措施。

`securityContext` 中的配置使用戶能夠使用 [seccomp](https://kubernetes.io/docs/tutorials/security/seccomp/) 在容器上實施限制。從版本 1.25 開始，Kubernetes 有一個名為 `RuntimeDefault` 的預設應用程式 seccomp 配置檔案。這是一個寬鬆的限制，但這是一個好的開始。請參閱如何創建具有此策略的 Pod：

```bash
kubectl apply -f - << EOF
apiVersion: v1
kind: Pod
metadata:
  name: nginx-seccomp-confined
spec:
  securityContext:
    seccompProfile:
        type: RuntimeDefault
  containers:
  - name: nginx
    image: nginxinc/nginx-unprivileged
    securityContext:
        runAsUser: 1000
        runAsGroup: 1000
        allowPrivilegeEscalation: false

EOF
```

請注意，這為應用程式啟用了 90% 的所有系統調用並限制了少數。這不會破壞大多數應用程式，但會在一定程度上限制攻擊者。

稍後請參閱如何為您的應用程式量身定制此配置。

## Kubernetes 原生網路策略

預設情況下，Kubernetes 集群中的 Pod 對網路通信沒有限制。任何 Pod 都可以與「Pod 網路」中的任何其他 Pod 通信。在 Pod 周圍創建微分段是限制攻擊「爆炸半徑」的重要方法：僅啟用應用程式所需的網路連接。

讓我們在集群中創建另一個 Nginx 部署和服務：
```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80
```

現在讓我們從另一個 Pod 測試連接：
```bash
kubectl run curl --rm -ti --image=curlimages/curl:latest -- sh
```
現在您可以使用 curl 從新 Pod 測試連接：
```bash
curl nginx
```

現在讓我們應用一個網路策略，僅允許標記為「nginx:client」標籤的其他 Pod 訪問 Nginx Pod

```bash
kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: nginx-client-access
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
  - from:
    - podSelector:
        matchLabels:
          nginx: "client"
EOF
```

運行與上面相同的測試將失敗：
```bash
kubectl run curl --rm -ti --image=curlimages/curl:latest -- sh
```
這應該會超時：
```bash
curl --connect-timeout 1 nginx
```

如果我們希望它工作，我們需要將標籤「nginx: client」添加到 curl Pod：
```bash
kubectl run curl --rm -ti --labels="nginx=client" --image=curlimages/curl:latest -- sh
```
現在請求將成功：
```bash
curl --connect-timeout 1 nginx
```

🆒 😄

這是 Kubernetes 原生網路策略如何工作的簡單範例。

## 從應用程式行為生成策略

正如在簡介中討論的那樣，定義這些策略存在一些相當大的複雜性。

一方面，正確定義它們需要時間，並且隨著變化，這些策略往往會破壞應用程式。這導致從業者定義寬鬆的策略。

另一方面，如果它們定義得不夠嚴格，它們在保護您的系統方面就不那麼有效。

不過還是有希望的 😉

較新的技術正在努力監控應用程式行為（網路和運行時），並自動將它們轉換為策略。

一個很好的例子是 [Inspektor Gadget](https://www.inspektor-gadget.io/)。

我們將在這裡看到如何安裝它，並看看它如何生成網路和 seccomp 配置檔案。

您可以使用 [krew](https://krew.sigs.k8s.io/) 安裝 Inspektor Gadget 的控制器。
```bash
kubectl krew install gadget
```
並使用以下命令安裝 Gadgets：
```bash
kubectl gadget deploy
```

現在您可以開始監控上述 Nginx Pod 並為其生成 seccomp 配置檔案：
```bash
kubectl gadget advise seccomp-profile start -n default -p $(kubectl get pods | grep nginx | head -n 1 | awk '{print $1}')
```

此命令開始監控並返回追蹤 ID，如果您認為已經有足夠的活動，請使用以下命令停止追蹤：
```bash
kubectl gadget advise seccomp-profile stop <traceid>
```

範例：
```bash
$ kubectl gadget advise seccomp-profile stop jd4VM2jWhnONfakF
{
  "defaultAction": "SCMP_ACT_ERRNO",
  "architectures": [
    "SCMP_ARCH_X86_64",
    "SCMP_ARCH_X86",
    "SCMP_ARCH_X32"
  ],
  "syscalls": [
    {
      "names": [
        "access",
        "arch_prctl",
        "brk",
        "chown",
        "clone",
        "close",
        "connect",
        "dup",
        "dup2",
        "execve",
        "exit_group",
        "faccessat",
        "fcntl",
        "fstat",
        "futex",
        "getcwd",
        "getdents64",
        "getegid",
        "geteuid",
        "getgid",
        "getpgrp",
        "getpid",
        "getppid",
        "getuid",
        "ioctl",
        "lseek",
        "mmap",
        "mprotect",
        "munmap",
        "openat",
        "pipe",
        "prlimit64",
        "pselect6",
        "read",
        "rt_sigaction",
        "rt_sigprocmask",
        "rt_sigreturn",
        "select",
        "set_robust_list",
        "set_tid_address",
        "setns",
        "setpgid",
        "socket",
        "stat",
        "statfs",
        "sysinfo",
        "uname",
        "wait4",
        "write"
      ],
      "action": "SCMP_ACT_ALLOW"
    }
  ]
}
```

同一個工具可以幫助您生成網路策略。讓我們使用此命令開始網路監控：
```bash
kubectl gadget advise network-policy monitor -p $(kubectl get pods | grep nginx | head -n 1 | awk '{print $1}') --output /tmp/network.log
```

當您認為監控已經看到足夠的活動時，可以使用 `ctrl-c` 停止。然後使用此命令生成策略 yaml：
```bash
kubectl gadget advise network-policy report --input /tmp/network.log 
```

這是您獲得的策略：
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  creationTimestamp: null
  name: nginx-network
  namespace: default
spec:
  ingress:
  - from:
    - podSelector: {}
    ports:
    - port: 80
      protocol: TCP
  podSelector:
    matchLabels:
      app: nginx
  policyTypes:
  - Ingress
  - Egress
status: {}
```
### 總結
這些是將行為轉換為策略的範例！好東西 😃

請參閱 [Day 34](day34.md)。
