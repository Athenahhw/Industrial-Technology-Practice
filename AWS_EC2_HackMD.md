---
title: AWS_EC2_HackMD.md

---

# Amazon EC2 重點整理

> 課程：產業科技實務  


[TOC]

## 一、EC2 是什麼？

**Amazon EC2 = Elastic Compute Cloud（彈性運算雲）**，可以想成「向 AWS 租一台雲端電腦」。

電腦位於 AWS 資料中心，使用者透過網路操作；可依需求調整規格、數量與使用時間。

| 彈性面向 | 說明 |
| --- | --- |
| 規格彈性 | 選擇不同 CPU、RAM 與網路能力的機型 |
| 數量彈性 | 從一台擴展到多台執行個體 |
| 計費彈性 | 依使用與購買方案付費 |

### 1.1 傳統機房 vs. EC2

| 比較 | 傳統地端機房 | AWS EC2 |
| --- | --- | --- |
| 建置流程 | 採購、等待硬體、上架、拉線、安裝系統 | 選 OS／機型後啟動 |
| 前置時間 | 須等待設備與建置作業 | 可在數分鐘內建立伺服器 |
| 容量調整 | 預估失準可能造成閒置或不足 | 依需求調整規格與數量 |
| 實體硬體 | 自行維修與管理 | AWS 維護底層實體硬體 |

**核心價值：把「購買伺服器」變成「租用運算資源」，縮短建置的前置時間。**

## 二、建立 EC2：流程與核心選擇

### 2.1 五個核心決定

| 項目 | 回答的問題 | 主要影響 |
| --- | --- | --- |
| **AMI** | 我要什麼作業系統與初始環境？ | OS、預裝軟體、開機環境 |
| **Instance Type** | 我要多少 CPU／RAM？ | 效能與成本 |
| **VPC／Subnet** | 伺服器放在哪個網路？ | 網路位置與連線規劃 |
| **Security Group** | 哪些流量可以進出？ | 網路存取安全 |
| **Storage／EBS** | 資料要放在哪裡？ | 容量、效能、持久性與成本 |

### 2.2 控制台操作流程

1. 登入 AWS 控制台，進入 EC2，選擇 **Launch instance**。
2. 填寫 **Name tag**，方便辨識資源。
3. 選擇 **AMI**。
4. 選擇 **Instance type**。
5. 選擇或建立 **Key pair**，妥善保管私鑰。
6. 設定 **Network settings**，包含網路與 Security Group。
7. 設定 **Configure storage**。
8. 檢查後按 **Launch instance**。

> Launch 不只是按下啟動，而是在決定系統環境、效能、網路、安全與資料儲存方式。

## 三、AMI：伺服器的開機範本

### 3.1 AMI 包含什麼？

**AMI = Amazon Machine Image**，是可重複使用的伺服器開機範本。

- 客體作業系統，例如 Linux、Windows。
- Root Volume（根磁碟）的範本。
- 可包含預裝軟體、依賴套件與初始組態。

**AMI 決定環境；Instance Type 決定運算規格。**

### 3.2 常見 AMI 來源

| 來源 | 特色 | 適合情境 |
| --- | --- | --- |
| **Quick Start** | 標準、乾淨的起始環境 | 新手、新專案 |
| **My AMIs／Custom AMI** | 自行建立的標準化環境 | 大量部署、Auto Scaling |
| **Community AMIs** | 社群提供的映像 | 使用前須評估來源與可信度 |
| **AWS Marketplace** | 軟體與執行環境，可能包含商業授權或原廠支援 | 企業軟體、虛擬設備 |

### 3.3 AWS Marketplace

可想成雲端 App Store，把軟體採購與部署納入雲端流程。

講義舉例：

| 類別 | 軟體例子 |
| --- | --- |
| 資安 | Palo Alto、Fortinet、Check Point |
| DevOps | GitLab Enterprise、Jenkins、Jira |
| 架站 | WordPress、Drupal |
| 資料／大數據 | MongoDB Enterprise、Cloudera、Databricks |

#### 授權模式

| 模式 | 意義 | 適合情境 |
| --- | --- | --- |
| **Pay-As-You-Go** | 軟體授權費整合進 AWS 帳單 | 短期專案、PoC、突發需求 |
| **BYOL** | Bring Your Own License，自備既有授權 | 已有授權，且授權條件允許在 AWS 使用 |

> 不能只看 EC2 運算費用，也要確認軟體授權成本與使用條件。

企業價值：集中採購與帳單、減少手動安裝、搭配 Launch Template／CloudFormation 自動化，讓流程標準化。

### 3.4 自訂 AMI 與 Golden AMI

建立流程：

1. 在 EC2 完成套件安裝、設定與安全修正。
2. 使用 **Create image** 建立自訂 AMI。
3. 後續 EC2 直接由該 AMI 啟動。

**Golden AMI 的目的：讓大量伺服器具備一致的標準環境，減少重複設定與環境差異。**

## 四、Instance Type：如何選機型？

### 4.1 拆解機型名稱

以 `c7g.2xlarge` 為例：

| 部分 | 意義 |
| --- | --- |
| `c` | Compute Optimized，運算最佳化家族 |
| `7` | 第 7 代 |
| `g` | AWS Graviton，ARM 架構處理器 |
| `2xlarge` | 執行個體大小，影響 vCPU、RAM 與網路能力 |

記憶方式：**家族 + 世代 + 處理器等屬性 + 大小**。

### 4.2 四大常見家族

| 家族 | 定位 | 適合工作負載 |
| --- | --- | --- |
| **T／M** | General Purpose，一般用途 | Web、開發測試、中小型系統 |
| **C** | Compute Optimized，運算最佳化 | 高併發、批次運算、轉檔 |
| **R** | Memory Optimized，記憶體最佳化 | Redis、記憶體需求高的資料庫 |
| **G／P** | Accelerated Computing，加速運算／GPU | 機器學習、推論、3D 渲染 |

**依瓶頸選機型：缺 CPU、缺 RAM，還是需要 GPU？**

### 4.3 同一家族的 Size

講義以 T3 為例：

| 機型 | vCPU | RAM（GiB） |
| --- | --- | --- |
| `t3.nano` | 2 | 0.5 |
| `t3.micro` | 2 | 1 |
| `t3.small` | 2 | 2 |
| `t3.medium` | 2 | 4 |
| `t3.large` | 2 | 8 |
| `t3.xlarge` | 4 | 16 |

Size 增加通常代表資源與價格上升，但 **CPU 與 RAM 不一定每一級都同時增加**。

### 4.4 T 系列與 CPU Credits

T 系列適合**平常低負載、偶爾有尖峰**的工作負載。

| 狀態 | CPU Credits 的作用 |
| --- | --- |
| 負載低於基準 | 累積 Credits |
| 尖峰時高於基準 | 消耗 Credits，短時間提供爆發效能 |
| Unlimited 模式 | 可在 Credits 不足時繼續超出基準運作，但可能產生額外費用 |

> Credits 可想成累積的「衝刺額度」；持續高負載時，應重新評估機型與成本。

### 4.5 Intel、AMD、Graviton

| 處理器 | 講義列示的常見標記 | 架構 | 選擇重點 |
| --- | --- | --- | --- |
| Intel | `i` | x86 | 相容性廣，適合既有或特定軟體環境 |
| AMD | `a` | x86 | 可作為 Intel 的替代選項 |
| AWS Graviton | `g` | ARM | 須確認 OS、程式與依賴套件支援 ARM |

**先確認軟體相容性，再比較效能與價格；標記須搭配具體家族判讀。**

### 4.6 垂直擴展：Scale Up

講義流程：`Stop → Change Instance Type → Start`

例如：`t3.micro → t3.small`。

- 改成更大規格的執行個體。
- 使用 EBS 的資料可保留，不必因為換規格就重灌。
- 須留意停止期間的服務中斷與新機型相容性。

## 五、EC2 生命週期

### 5.1 四種操作比較

| 操作 | RAM | EBS | 自動指派的 Public IPv4 | 運算計費 | 能否恢復使用？ |
| --- | --- | --- | --- | --- | --- |
| **Reboot** | 清空 | 保留 | 通常保留 | 持續 | 重新啟動後繼續使用 |
| **Stop／Start** | 清空 | 保留 | Stop／Start 後可能改變 | 停止狀態不收運算費 | 可再次 Start |
| **Hibernate** | 寫入 EBS，啟動時恢復 | 保留 | 可能改變 | 停止狀態不收運算費 | 條件符合時可恢復狀態 |
| **Terminate** | 清空 | 依刪除設定決定 | 釋放 | 運算結束 | 原執行個體無法再 Start |

> 表中的運算費不代表總帳單。EBS、IP 等其他資源仍可能收費；休眠的轉換過程也不能直接視為整段免費。

### 5.2 Reboot：重新啟動作業系統

- RAM 清空，EBS 資料保留。
- Private IP 保留；一般情況下 Public IP 也保留。
- 運算資源仍在使用，計費不中斷。

**Reboot ≠ Stop：重新開機不等於停止租用運算資源。**

### 5.3 Stop／Start：暫停後再使用

- 保留 EBS 資料與 Private IP。
- RAM 狀態不保留。
- Instance Store 資料不保留。
- 自動指派的 Public IPv4 不保證保留。
- 停止狀態不收運算費，其他資源仍可能有費用。

### 5.4 Public IP 變了怎麼辦？

| 方案 | 概念 | 注意事項 |
| --- | --- | --- |
| **Elastic IP** | 可固定使用的 Public IPv4，與資源建立關聯 | IP 費用須納入成本評估，不應理解為使用中必然免費 |
| **ELB／Route 53** | 透過負載平衡器與 DNS 提供穩定的服務入口 | 讓使用者不必直接依賴單台 EC2 的動態 Public IP |

### 5.5 Hibernate：保存 RAM 狀態

1. 休眠前將 RAM 狀態寫入 EBS。
2. 停止後釋放實體運算資源。
3. 再次 Start 時，從 EBS 還原 RAM 與應用程式狀態。

優點是可縮短狀態恢復時間，但**須符合特定機型、OS 與儲存等前置條件**，不是每台 EC2 都能直接使用。

### 5.6 Terminate：永久結束執行個體

- 原 EC2 執行個體無法再次 Start。
- Root EBS 是否刪除，取決於 **DeleteOnTermination**；講義指出常見預設為 `true`。
- 其他 EBS Volume 也須依個別設定判斷是否保留。
- 可使用 **Termination Protection** 降低誤刪風險。

**Stop 是暫停；Terminate 是退租。重要資料是否保留，要檢查磁碟設定。**

## 六、EC2 儲存：EBS 與 Instance Store

### 6.1 比較表

| 比較 | Amazon EBS | Instance Store |
| --- | --- | --- |
| 形式 | 網路掛載的持久區塊儲存 | 實體主機本機儲存 |
| 資料特性 | 可在執行個體停止後保留 | 暫態，不能依賴其長期保存資料 |
| 拆裝 | 可 Detach／Attach，須符合掛載條件 | 不能像 EBS 一樣移接到另一台 EC2 |
| 備份 | 支援 EBS Snapshot | 不以 EBS Snapshot 機制直接備份 |
| 典型用途 | OS、資料庫、重要應用資料 | Cache、Scratch、可重建的暫存資料 |
| 主要考量 | 持久性、容量、IOPS、吞吐量、成本 | 本機低延遲與高 I/O 效能 |

**先問「資料能不能消失」，再比較哪個比較快。**

### 6.2 EBS 的重點

- 是持久化的區塊儲存，可作為系統磁碟或資料磁碟。
- 可拆離後再掛載到另一台合適的 EC2。
- 可透過 Snapshot 備份。
- **EBS 是 AZ 層級資源**，不能把一般掛載當成直接跨 AZ 使用；跨 AZ 須透過適當機制處理。
- 持久化不代表自動備份，也不代表 Terminate 時一定保留。

### 6.3 常見 EBS 類型

| 類型 | 儲存媒介／定位 | 適合用途 | 記憶重點 |
| --- | --- | --- | --- |
| **gp3** | General Purpose SSD | 一般用途 | IOPS 與吞吐量可獨立配置 |
| **io2** | Provisioned IOPS SSD | 高 I/O、低延遲的關鍵資料庫 | 重視 I/O 效能 |
| **st1** | Throughput Optimized HDD | 大量循序讀寫 | 重視吞吐量 |
| **sc1** | Cold HDD | 低成本冷資料 | 不作為系統開機碟 |

- **IOPS**：每秒可完成的 I/O 操作次數。
- **Throughput（吞吐量）**：每秒可傳輸的資料量。
- **Latency（延遲）**：一次操作所需的時間。

> 選型要同時衡量容量、IOPS、吞吐量、延遲與成本，不能只看磁碟大小。

### 6.4 Instance Store 的資料風險

- 直接連接實體主機，可提供低延遲與高 I/O 效能。
- Stop、Terminate 或底層硬體故障會造成資料遺失風險。
- 適合可重建的快取與暫存工作空間。
- **不能當作重要資料的唯一來源。**

### 6.5 EBS Elastic Volumes：擴大磁碟

例如從 `20 GB → 50 GB`，可在符合條件時在線調整。

必須完成兩個層次：

1. **AWS 儲存層**：增加 EBS Volume 容量。
2. **OS 層**：視需要擴展分割區與檔案系統，讓系統使用新增空間。

- **容量可放大，不能直接縮小。**
- 是否能完全不中斷，取決於 OS、檔案系統與操作方式。
- 只改控制台容量，不代表檔案系統就已經能使用新增空間。

## 七、EC2 購買方式與成本

### 7.1 四種方案比較

| 方案 | 核心概念 | 適合工作負載 | 主要取捨 |
| --- | --- | --- | --- |
| **On-Demand** | 隨開隨用，無長期承諾 | 短期測試、新產品、PoC、難預測流量 | 彈性高，但不以最低單價為目標 |
| **Reserved Instances／RI** | 長期使用承諾換折扣 | 穩定、可預估且符合方案條件的需求 | 有承諾，彈性依方案類型而定 |
| **Savings Plans** | 承諾每小時一定金額的運算消費 | 長期穩定的運算需求 | 折扣適用範圍依方案而定 |
| **Spot** | 使用可能被收回的閒置容量 | 可中斷、可重跑、有容錯的工作 | 價格優惠，但須處理中斷 |

### 7.2 Reserved Instances

講義以 **1 年／3 年承諾**比喻長期租約。

| 付款方式 | 意義 |
| --- | --- |
| **All Upfront** | 一次預付，通常可取得較高折扣 |
| **Partial Upfront** | 部分預付，其餘按期支付 |
| **No Upfront** | 不預付，依約按期支付 |

**RI 的重點是長期承諾，不等於「沒有開機就沒有承諾費用」。**

### 7.3 Savings Plans

- 承諾一段期間內，每小時一定金額的運算使用量。
- 重點是「承諾多少穩定消費」。
- 能否跨機型或運算服務使用折扣，取決於所選方案。

**RI 偏向對符合條件的執行個體使用提供折扣；Savings Plans 以運算消費承諾為核心。**

### 7.4 Spot 與中斷處理

Spot 便宜的原因是利用 AWS 閒置容量，但容量可能被收回。

| 適合 | 不適合直接使用且沒有容錯的工作 |
| --- | --- |
| 大數據批次、影片轉檔、3D 渲染 | 關鍵資料庫 |
| CI/CD 建置與測試 | 核心交易 |
| 可重跑的無狀態節點 | 不能中斷的單一服務 |

講義列出中斷行為：**Terminate、Stop、Hibernate**；可用行為與恢復方式須符合對應條件，不能假設每次都能自行立即重啟。

#### Checkpoint（檢查點）

將處理進度持續寫入 **S3／Database** 等持久儲存，中斷後從已保存的進度繼續，降低重跑成本。

**判斷是否適合 Spot：被中斷後，能否安全恢復或重跑？**

### 7.5 成本觀念

- Stop 可停止執行個體的運算使用費，但 EBS 等其他資源仍可能收費。
- Marketplace 可能另有軟體授權費。
- T 系列 Unlimited 可能產生額外 CPU 費用。
- RI／Savings Plans 須考慮長期承諾是否能被實際使用量消化。
- **「不用 EC2」不等於「所有相關費用都停止」。**

## 八、安全連線與授權

### 8.1 Security Group：虛擬防火牆

| 特性 | 說明 |
| --- | --- |
| **Default Deny** | 未被允許的流量不會因為沒有規則就自動通過 |
| **Allow only** | 設定允許規則，不提供手動 Deny 規則 |
| **Stateful** | 已允許連線的回應流量會自動被允許 |

**原則：只允許必要的來源、連接埠與流量，並檢查現有的 Inbound／Outbound 規則。**

### 8.2 SSH Key Pair

Linux EC2 常見的傳統登入方式是 **SSH + Key Pair**。

| 項目 | 保存位置／作用 |
| --- | --- |
| **Public Key（公鑰）** | 放在 EC2，用於驗證登入者 |
| **Private Key（私鑰）** | 由使用者保管，常見為 `.pem` 檔案 |

Mac／Linux 常見的本機私鑰權限設定：

```bash
chmod 400 key.pem
```

- 私鑰須妥善保存，遺失通常無法從 AWS 重新下載原私鑰。
- 不要放進 Git，不要分享給他人。
- 若從 Internet 直接 SSH，還須有可達的網路路徑及適當的 Port 22 規則。

### 8.3 Systems Manager Session Manager

| 比較 | 傳統公開 SSH | SSM Session Manager |
| --- | --- | --- |
| 接入方式 | 通常透過 Public IP 與 Port 22 | 透過 Systems Manager 建立管理工作階段 |
| 公開 Port 22 | 通常需要 | 不需要 |
| Public IP | 直接從 Internet 連入時通常需要 | 可不需要 |
| 管理方式 | 管理 SSH 金鑰與網路存取 | 可整合 IAM、MFA 與操作紀錄 |

**SSM 的核心：把登入伺服器轉為受 IAM 控制的管理操作，減少公開 SSH 暴露面。**

> 「免 Public IP」不等於完全不需要網路或設定；仍須具備 Session Manager 所需的權限、代理程式與服務連線條件。

### 8.4 IAM Role：讓 EC2 安全存取 AWS 服務

情境：EC2 上的程式需要讀取 S3。

| 作法 | 問題／優點 |
| --- | --- |
| 把 Access Key／Secret Key 寫進程式或憑證檔案 | 長期金鑰可能外洩，須自行管理與輪替 |
| **建立 IAM Role 並附加到 EC2** | SDK／CLI 可取得自動輪替的暫時憑證 |

建議流程：

1. 建立具備所需權限的 IAM Role。
2. 將 Role 附加到 EC2。
3. 程式透過 SDK／CLI 使用暫時憑證。
4. 僅授予必要操作與資源的權限。

**Key Pair 管登入；IAM Role 管 EC2 程式存取 AWS 服務的權限，兩者用途不同。**

## 九、情境判斷與快速複習

### 9.1 常見情境怎麼選？

| 情境 | 選擇與理由 |
| --- | --- |
| 想快速建立乾淨 Linux 環境 | Quick Start AMI |
| 已安裝好套件，想複製一致環境 | 自訂／Golden AMI |
| 需要商用防火牆或企業軟體 | 評估 Marketplace 與授權模式 |
| 平常低負載，偶爾流量尖峰 | 評估 T 系列與 CPU Credits |
| 需要大量 RAM 跑 Redis | 評估 R 系列 |
| 需要 GPU 進行 ML 或渲染 | 評估 G／P 系列 |
| EC2 規格不足，需要加大 | 評估 Stop → Change Instance Type → Start |
| 要暫停使用並保留 EBS 資料 | Stop；仍須注意磁碟等費用 |
| 要保存執行中的 RAM 狀態 | 符合條件時使用 Hibernate |
| 要永久刪除執行個體 | Terminate；先確認 EBS 刪除設定 |
| 儲存不能遺失的應用資料 | EBS，並規劃備份 |
| 處理可重建的高速暫存資料 | 評估 Instance Store |
| EBS 已加大，但 OS 空間沒變 | 檢查分割區與檔案系統是否完成擴展 |
| 短期測試、需求難預測 | On-Demand |
| 長期穩定的運算需求 | 比較 RI／Savings Plans |
| 可中斷的批次或渲染任務 | Spot，搭配 Checkpoint／重跑設計 |
| 不想公開 SSH Port 22 | 評估 Session Manager |
| EC2 程式需要讀 S3 | IAM Role，避免內嵌長期金鑰 |

### 9.2 易混淆概念

| 概念 | 正確理解 |
| --- | --- |
| AMI／Instance Type | AMI 決定開機環境；Type 決定運算規格 |
| Scale Up／增加台數 | Scale Up 是把單台換成更大規格 |
| Reboot／Stop | Reboot 持續使用運算資源；Stop 暫停運算資源 |
| Stop／Terminate | Stop 可以再啟動；Terminate 後原執行個體不能再啟動 |
| Stop／Hibernate | Stop 不保留 RAM；Hibernate 將 RAM 寫入 EBS |
| EBS 持久／永不刪除 | EBS 可保留，但仍受 DeleteOnTermination 與刪除操作影響 |
| EBS／Instance Store | EBS 是持久區塊儲存；Instance Store 是本機暫態儲存 |
| EBS 擴容／檔案系統擴容 | 雲端磁碟與 OS 層須分別處理 |
| On-Demand／RI／Savings Plans | 隨用隨付／長期使用承諾／每小時消費承諾 |
| Spot 便宜／隨時可用 | 低價不代表容量保證或不中斷 |
| Key Pair／IAM Role | Key Pair 用於登入驗證；Role 提供 AWS 服務存取權限 |
| Security Group | Allow only、Stateful；只開必要流量 |

### 9.3 課後應能回答的五個問題

1. **我要什麼樣的伺服器？** → AMI／Instance Type。
2. **資料放哪裡？** → EBS／Instance Store。
3. **下課要不要關機？** → Reboot／Stop／Hibernate／Terminate。
4. **怎麼省錢？** → On-Demand／RI／Savings Plans／Spot。
5. **怎麼安全管理？** → Security Group／SSH Key Pair／SSM／IAM Role。

> **先選環境與規格，再確認資料如何保存、生命週期如何影響資源，最後依工作負載選購買方案與安全管理方式。**

## 十、講義提供的學習資源

- [AWS Educate](https://aws.amazon.com/tw/education/awseducate/)：講義提供的申請與學習入口。
