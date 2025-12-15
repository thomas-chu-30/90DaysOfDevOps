# 理解 Kubernetes Secrets

## 簡介

Kubernetes 已成為容器編排的事實標準，使開發人員能夠輕鬆部署、管理和擴展應用程式。當您在 Kubernetes 上管理應用程式時，您經常需要處理敏感資訊，如密碼、token 和密鑰。這就是 Kubernetes secrets 發揮作用的地方。在這篇文章中，我們將探討什麼是 Kubernetes secrets，為什麼它們很重要，以及它們如何工作。

## 什麼是 Kubernetes Secrets？

Kubernetes secrets 是設計用於安全存儲敏感資訊的對象。與以純文字存儲配置數據的 ConfigMaps 不同，secrets 旨在保存敏感數據，例如：

- 密碼
- API token
- SSH 密鑰
- TLS 證書

Secrets 以 Base64 格式編碼，可以以各種方式使用，以確保在 Kubernetes 集群內安全地處理敏感資訊。

## Secrets 類型：

- Opaque：用於任意用戶定義數據的預設 secret 類型。
- TLS：專門用於存儲 TLS 證書和密鑰。
- Docker Config：用於存儲 Docker 註冊表憑證。
- Basic Auth：存儲用戶名和密碼對。
- SSH Auth：存儲 SSH 密鑰。

## 為什麼 Kubernetes Secrets 很重要？

Kubernetes secrets 至關重要，原因如下：

1. 安全性和機密性：Secrets 有助於將敏感數據排除在應用程式代碼和配置檔案之外。
2. 避免硬編碼：它們防止在代碼庫中硬編碼敏感資訊，降低洩露風險。
3. 簡化管理：Secrets 簡化了更新敏感數據的過程，無需應用程式重啟或重新部署。

## Kubernetes Secrets 如何工作

創建和存儲：Secrets 可以使用 kubectl、Kubernetes API 或 Helm charts 手動或程式化地創建。它們存儲在 etcd 數據庫中，應該配置為對靜態數據進行加密。

訪問 Secrets：Secrets 可以作為卷掛載或作為環境變數在 Pod 內公開。這允許應用程式訪問敏感資訊，而不會在容器映像中暴露它。

靜態加密：Kubernetes 支援對存儲在 etcd 中的 secrets 進行靜態加密。這通過確保在寫入磁碟時對 secret 數據進行加密來增加額外的安全層。

## 結論

Kubernetes secrets 是在集群中安全管理敏感資訊的重要組件。它們提供了一種安全、靈活且可管理的方式來處理不應該暴露或硬編碼的數據。在下一篇文章中，我們將深入探討動手場景，以幫助您開始創建和使用 Kubernetes secrets。

## 資源

# Kubernetes Secrets 管理的免費 YouTube 資源

1. **Kubernetes Secrets 解釋**
   - [Kubernetes Secrets 解釋 | Kubernetes 教程 16 | 學習 Kubernetes](https://www.youtube.com/watch?v=au6gC2iE2JM) by TechWorld with Nana
   - 此視頻解釋了 Kubernetes secrets 的基礎知識，如何創建它們，以及如何在 Pod 中使用它們。

2. **在 Kubernetes 中管理 Secrets**
   - [在 Kubernetes 中管理 Secrets](https://www.youtube.com/watch?v=ON5pQByUkkE) by A Cloud Guru
   - 此視頻涵蓋了不同類型的 secrets，如何管理它們，以及最佳實踐。

3. **Kubernetes Secrets：使用 Kubernetes 存儲、使用和加密 Secrets**
   - [Kubernetes Secrets：使用 Kubernetes 存儲、使用和加密 Secrets](https://www.youtube.com/watch?v=fFOvlPjuw9I) by DevOps Toolkit
   - 該視頻深入探討了如何創建 secrets，從 Pod 訪問它們，以及啟用靜態加密。

4. **使用 Kubernetes Secrets**
   - [使用 Kubernetes Secrets](https://www.youtube.com/watch?v=gZX9Vxjpo5Y) by IBM Technology
   - 此教程解釋了如何在 Kubernetes 集群中創建、管理和使用 secrets。

5. **Kubernetes 教程：如何在集群中使用 Kubernetes Secrets**
   - [Kubernetes 教程：如何在集群中使用 Kubernetes Secrets](https://www.youtube.com/watch?v=5fCJlAqC1B0) by Just me and Opensource
   - 該視頻提供了在 Kubernetes 中創建和使用 secrets 的動手指南。

6. **Kubernetes Secrets 管理最佳實踐**
   - [Kubernetes Secrets 管理最佳實踐](https://www.youtube.com/watch?v=Nwd8tUP43WU) by Kubernetes Community Days
   - 此演講專注於在 Kubernetes 環境中管理 secrets 的最佳實踐。

7. **Kubernetes Secrets 和 ConfigMaps**
   - [Kubernetes Secrets 和 ConfigMaps](https://www.youtube.com/watch?v=7UXJ-nxW1EI) by FreeCodeCamp.org
   - 此視頻涵蓋了 ConfigMaps 和 Secrets 之間的差異，以及如何有效地使用兩者。

8. **Kubernetes Secrets | Kubernetes 中的安全和配置管理**
   - [Kubernetes Secrets | Kubernetes 中的安全和配置管理](https://www.youtube.com/watch?v=twFRhEcvC2E) by Tech Primers
   - 使用 Kubernetes secrets 進行安全和配置管理的綜合指南。

9. **使用 HashiCorp Vault 進行高級 Kubernetes Secrets 管理**
   - [使用 HashiCorp Vault 進行高級 Kubernetes Secrets 管理](https://www.youtube.com/watch?v=byCCrbt0bBo) by HashiCorp
   - 此視頻演示了如何將 HashiCorp Vault 與 Kubernetes 整合以進行高級 secrets 管理。

10. **使用 Sealed Secrets 在 Kubernetes 中進行 Secrets 管理**
    - [使用 Sealed Secrets 在 Kubernetes 中進行 Secrets 管理](https://www.youtube.com/watch?v=UrhZiFEYcs4) by KubeCon + CloudNativeCon
    - 關於如何使用 Sealed Secrets 管理 secrets 的演示，這是一個允許在 Git 儲存庫中安全存儲和管理 secrets 的工具。

這結束了 Day 40，明天我們將動手實踐 Kubernetes secrets [Day 41](day41.md)
