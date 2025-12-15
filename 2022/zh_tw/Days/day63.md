---
title: '#90DaysOfDevOps - 概述：配置管理 - 第 63 天'
published: false
description: 90DaysOfDevOps - 概述配置管理
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048711
---

## 概述：配置管理

緊接著涵蓋基礎設施即代碼的部分，當我們談論配置管理或應用程式配置管理時，可能會有一些交叉。

配置管理是在所需狀態下維護應用程式、系統和伺服器的過程。與基礎設施即代碼的重疊是 IaC 將確保你的基礎設施處於所需狀態，但之後，特別是 terraform 不會照顧你的 OS 設置或應用程式的所需狀態，這就是配置管理工具的用武之地。確保系統和應用程式在隨著時間推移發生變化時按預期方式執行。

配置管理可以防止你進行未記錄的小或大更改。

### 場景：為什麼你想使用配置管理

場景或為什麼你想使用配置管理，認識 Dean，他是我們的系統管理員，Dean 是一個快樂的露營者，在他的環境中的所有系統上工作。

如果他們的系統失敗，如果有火災，伺服器出現故障怎麼辦？Dean 確切知道該怎麼做，他可以輕鬆修復火災，但如果多個伺服器開始失敗，特別是當你有大型和擴展環境時，問題對 Dean 來說變得困難，這就是為什麼 Dean 需要配置管理工具。配置管理工具可以幫助 Dean 看起來像搖滾明星，他所要做的就是配置正確的代碼，允許他快速有效地大規模推送如何設置每個伺服器的指令。

### 配置管理工具

有各種可用的配置管理工具，每種都有特定功能，使其在某些情況下比其他工具更好。

![](Images/Day63_config1.png)

在這個階段，我們將快速查看上圖中的選項，然後再決定我們將使用哪一個以及為什麼。

- **Chef**

  - Chef 確保配置在每個環境中一致應用，在任何規模下都使用基礎設施自動化。
  - Chef 是由 OpsCode 開發的開源工具，用 Ruby 和 Erlang 編寫。
  - Chef 最適合擁有異構基礎設施並正在尋找成熟解決方案的組織。
  - Recipes 和 Cookbooks 確定系統的配置代碼。
  - 優點 - 有大量可用的 recipes
  - 優點 - 與 Git 整合良好，提供強大的版本控制
  - 缺點 - 學習曲線陡峭，需要相當長的時間。
  - 缺點 - 主伺服器沒有太多控制。
  - 架構 - 伺服器 / 客戶端
  - 設置難易度 - 中等
  - 語言 - 程序式 - 指定如何執行任務

- **Puppet**
  - Puppet 是一個支持自動部署的配置管理工具。
  - Puppet 用 Ruby 構建，使用 DSL 編寫 manifests。
  - Puppet 在專注於可擴展性的異構基礎設施上也工作良好。
  - 優點 - 大型社群支持。
  - 優點 - 完善的報告機制。
  - 缺點 - 高級任務需要 Ruby 語言知識。
  - 缺點 - 主伺服器沒有太多控制。
  - 架構 - 伺服器 / 客戶端
  - 設置難易度 - 中等
  - 語言 - 聲明式 - 僅指定要做什麼
- **Ansible**

  - Ansible 是一個 IT 自動化工具，可自動化配置管理、雲配置、部署和編排。
  - Ansible playbooks 的核心用 YAML 編寫。（應該做一個關於 YAML 的部分，因為我們已經見過幾次了）
  - Ansible 在專注於快速啟動和運行的環境中工作良好。
  - 在 playbooks 上工作，為伺服器提供指令。
  - 優點 - 遠程節點上不需要代理。
  - 優點 - YAML 易於學習。
  - 缺點 - 性能速度通常低於其他工具（比 Dean 自己手動執行更快）
  - 缺點 - YAML 不如 Ruby 強大，但學習曲線較小。
  - 架構 - 僅客戶端
  - 設置難易度 - 非常容易
  - 語言 - 程序式 - 指定如何執行任務

- **SaltStack**
  - SaltStack 是一個基於 CLI 的工具，可自動化配置管理和遠程執行。
  - SaltStack 基於 Python，而指令用 YAML 或其 DSL 編寫。
  - 非常適合以可擴展性和彈性為優先的環境。
  - 優點 - 啟動和運行時易於使用
  - 優點 - 良好的報告機制
  - 缺點 - 設置階段很困難
  - 缺點 - 新的 Web UI，比其他工具開發程度低得多。
  - 架構 - 伺服器 / 客戶端
  - 設置難易度 - 中等
  - 語言 - 聲明式 - 僅指定要做什麼

### Ansible vs Terraform

我們將在本節中使用的工具將是 Ansible。（易於使用，需要更簡單的語言基礎。）

我認為在我們進一步研究工具之前，觸及 Ansible 和 Terraform 之間的一些差異很重要。

|                | Ansible                                                      | Terraform                                                        |
| -------------- | ------------------------------------------------------------ | ---------------------------------------------------------------- |
| Type           | Ansible 是配置管理工具                                       | Terraform 是編排工具                                             |
| Infrastructure | Ansible 提供對可變基礎設施的支持                             | Terraform 提供對不可變基礎設施的支持                               |
| Language       | Ansible 遵循程序式語言                                       | Terraform 遵循聲明式語言                                          |
| Provisioning   | Ansible 提供部分配置（VM、網路、儲存）                       | Terraform 提供廣泛的配置（VM、網路、儲存）                        |
| Packaging      | Ansible 提供對打包和模板的完整支持                           | Terraform 提供對打包和模板的部分支持                              |
| Lifecycle Mgmt | Ansible 沒有生命週期管理                                     | Terraform 嚴重依賴生命週期和狀態管理                              |

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)

我們[第 64 天](day64.md)見