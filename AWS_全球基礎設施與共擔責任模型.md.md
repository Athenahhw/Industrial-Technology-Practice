---
title: AWS_全球基礎設施與共擔責任模型.md

---

# AWS 全球基礎設施與共擔責任模型

> 課程：產業科技實務  

[TOC]

## 一、為什麼企業使用公有雲？

核心轉變：**從「購買設備」轉向「按需取得能力」**。

| 地端機房的挑戰 | 公有雲提供的能力 |
| --- | --- |
| 容量預估容易失準 | 依需求彈性擴展 |
| 硬體折舊、汰換 | 按用量付費（Pay-as-you-go） |
| 自行負擔電力、空調與機房維護 | 使用雲端供應商的基礎設施 |
| 跨國部署成本高、建置時間長 | 快速建立跨區架構 |

- **CAPEX（資本支出）**：購買設備等前期投資。
- **OPEX（營運支出）**：持續依使用需求支付費用。
- 上雲代表成本結構與管理方式改變，仍須評估 **TCO（總持有成本）**。

## 二、AWS 全球基礎設施

### 2.1 基礎設施的層級

| 名稱 | 定義與用途 | 記憶重點 |
| --- | --- | --- |
| Region（區域） | 獨立地理位置，由多個彼此隔離的 AZ 組成 | 地理位置與隔離邊界 |
| Availability Zone／AZ（可用區） | 一個或多個獨立資料中心，具備冗餘電力、網路與連線 | 故障隔離與高可用 |
| Data Center／DC（資料中心） | 實際放置伺服器、儲存與網路設備的設施 | 實體機房 |
| Edge Location／PoP（邊緣節點） | 靠近使用者，提供快取、連線終止與加速 | 讓服務更靠近使用者 |

**Region ≠ 一棟資料中心；AZ ≠ 一間機房。**

講義列示規模：**39 個 Region、123 個 AZ、700+ 個 CloudFront／PoP 節點**，並以全球私有骨幹網路連接。

### 2.2 Region 的選擇因素

| 因素 | 必須考慮的問題 |
| --- | --- |
| 資料位置與合規 | 資料可存放在哪些國家或地區？ |
| 延遲 | 使用者在哪裡？服務是否需要即時回應？ |
| 服務支援度 | 該 Region 是否支援需要的服務、執行個體類型與功能？ |
| 可用性與復原 | 是否需要 Multi-AZ 或跨 Region 災難復原？ |
| 成本 | 運算、儲存、請求、資料傳輸等整體成本是多少？ |

#### 延遲：距離仍然重要

- 講義舉例：台北到東京約數十毫秒，台北到美西約百餘毫秒。
- **RTT（Round-Trip Time）**：封包往返所需的時間。
- 金融交易、即時 API、遊戲對戰等工作負載對延遲較敏感。
- 雲端與網路加速可改善路徑，無法消除物理距離。

#### 服務支援度：各 Region 不一定相同

- 新服務可能先在少數 Region 推出，再逐步擴展。
- 選型前確認 Region Table、服務可用性、配額（Quota）、執行個體家族及跨 Region 功能限制。
- **有這個 Region，不代表有全部 AWS 服務。**

#### 價格：應比較整體 TCO

- EC2：執行個體、作業系統、租用方式（Tenancy）。
- S3：儲存、請求與資料傳輸。
- 土地、能源、稅務、供應鏈與服務成熟度可能影響區域價格。

### 2.3 AZ 與 Multi-AZ

- 同一 Region 內的 AZ 彼此隔離，降低單一故障的影響。
- AZ 之間透過**高頻寬、低延遲、具備冗餘的專屬光纖網路**連接。
- 支援跨 AZ 負載平衡、資料複寫與故障切換（Failover）。

| 部署方式 | 故障時的影響 |
| --- | --- |
| Single-AZ | 該 AZ 故障可能導致服務中斷 |
| Multi-AZ | 配合冗餘部署與 Failover，可持續提供服務 |

**Design for Failure：假設任何單一元件都可能故障，再設計冗餘與復原機制。**

> Multi-AZ 必須搭配正確的負載平衡、資料複寫及切換設計，才能達成高可用。

### 2.4 AZ Name 與 AZ ID

| 項目 | 意義 |
| --- | --- |
| AZ Name | 例如 `ap-northeast-1a`，不同帳號下可能對應不同實體 AZ |
| AZ ID | 用於識別實體 AZ，跨帳號對齊時應以此為準 |

**不同帳號的「1a」不一定是同一個實體 AZ。**

### 2.5 特殊區域與在地部署選項

| 類型 | 特色 | 適用需求 |
| --- | --- | --- |
| AWS GovCloud (US) | 面向美國政府與受監管工作負載的獨立 Regions | 合規、隔離與受限人員／資料處理要求 |
| European Sovereign Cloud | 講義介紹的歐洲主權雲環境 | 資料主權與營運控制 |
| Local Zones | 將部分運算與儲存能力延伸到特定都會區 | 更靠近終端使用者、降低延遲 |
| AWS Outposts | 將 AWS 基礎設施與 API 部署在客戶機房 | 低延遲、資料留置、混合雲 |



## 三、邊緣服務與全球網路

### 3.1 Amazon CloudFront

CloudFront 是 **CDN（內容傳遞網路）**，透過邊緣節點讓內容更接近使用者。

典型路徑：`使用者 → CloudFront → Origin（來源站）`

| 功能 | 重點 |
| --- | --- |
| 靜態內容快取 | 快取圖片、CSS、JavaScript、影片等，減少 Origin 請求 |
| 動態內容加速 | 對 API／動態內容進行連線與路由最佳化 |
| 安全整合 | 搭配 WAF、Shield 在 Edge 提供防護 |

### 3.2 AWS Global Accelerator

- 使用 **Anycast IP**，讓使用者就近進入 AWS Edge。
- 再透過 AWS 全球私有骨幹網路傳送流量。
- 目的：改善網路路徑與穩定性，降低公網路由變動及壅塞的影響。

| 比較 | CloudFront | Global Accelerator |
| --- | --- | --- |
| 講義核心定位 | CDN：內容快取與傳遞 | 網路路徑加速與穩定性 |
| 主要機制 | 邊緣快取、連線與路由最佳化 | Anycast IP、就近接入 AWS 骨幹 |

## 四、共擔責任模型（Shared Responsibility Model）

### 4.1 最重要的兩句話

| 責任類型 | 負責者 | 主要範圍 |
| --- | --- | --- |
| **Security OF the Cloud** | **AWS** | 雲端本身：實體、硬體、網路、虛擬化與底層基礎設施安全 |
| **Security IN the Cloud** | **客戶** | 雲端中的資料、身分、設定、應用程式，以及依服務型態而定的 OS／網路管理 |

**Shared ≠ Shared equally：共同負責不代表責任各半，邊界會隨服務改變。**

### 4.2 AWS 負責什麼？

- 資料中心的門禁、監控與實體安全。
- 伺服器、儲存與網路設備。
- Hypervisor／Nitro 等虛擬化基礎設施。
- 硬體維護與汰換。

### 4.3 客戶負責什麼？

- **資料**：分類、保護、加密與備份策略。
- **身分與權限**：IAM、MFA、角色與最小權限。
- **OS／應用程式**：依服務型態處理修補、漏洞與程式碼安全。
- **網路設定**：Security Group、NACL、路由與存取範圍。

### 4.4 不同服務的責任邊界

託管程度越高，AWS 接管越多底層維運；客戶仍須管理自身資料與使用設定。

| 服務 | AWS 主要負責 | 客戶主要負責 |
| --- | --- | --- |
| **EC2（IaaS）** | 實體主機、電源、底層網路、虛擬化 | OS 選型與修補、防毒／EDR、連接埠／SG、應用安全、資料與權限 |
| **RDS（託管資料庫）** | 底層主機、OS 維運、DB Engine 安裝／維護、自動備份等託管能力 | DB 帳號與權限、Schema／SQL、資料分類、加密選擇、網路存取控制 |
| **Lambda** | 底層伺服器、虛擬化及託管環境維運 | 函式程式碼、IAM 權限、資料與相關安全設定 |
| **S3** | 底層儲存基礎設施與服務維運 | Bucket Policy、資料、存取權限與加密設定 |


**Serverless ≠ Securityless：不用管理伺服器，仍要管理安全。**

## 五、客戶必須掌握的安全設定

### 5.1 資料加密與金鑰

| AWS 提供的工具／能力 | 客戶必須決定與管理 |
| --- | --- |
| KMS、CloudHSM | 金鑰使用權限與管理策略 |
| 靜態加密（At Rest） | 資料保護與加密選擇 |
| 傳輸加密（In Transit） | 傳輸保護設定 |
| 加密相關功能 | 金鑰輪換、敏感欄位遮蔽 |

### 5.2 IAM 與最小權限

- **IAM**：身分與存取管理。
- **MFA**：多因素驗證。
- **Role**：角色；**Policy**：定義權限的政策。
- **Least Privilege（最小權限）**：只給完成工作所需的權限。
- 權限應盡量精確到必要的 **Action（操作）與 Resource（資源）**。
- 濫用 `AdministratorAccess` 會擴大 **Blast Radius（事故影響範圍）**。

### 5.3 VPC 與網路控制

- AWS 提供 VPC、子網路與路由等虛擬網路能力。
- 客戶配置 Subnet、Route Table、NAT Gateway、Security Group 與 NACL。
- **Security Group 是 Stateful Firewall（有狀態防火牆）。**

### 5.4 EC2 的 OS 修補

- EC2 上的 Linux／Windows 修補仍屬客戶責任。
- 客戶須決定修補時間，並驗證修補後服務能正常運作。
- AWS Systems Manager／Patch Manager 可協助排程、自動化與合規檢查。

### 5.5 Root User

- Root 具備完整帳號控制權，不能用一般 IAM Policy 限制其本身。
- 啟用 MFA。
- 避免建立／刪除不必要的 Root Access Key。
- 日常維運不用 Root，將其視為 **Break Glass Account（緊急用途帳號）**。

## 六、備份、高可用與災難復原

### 6.1 RTO 與 RPO

| 指標 | 全名 | 要回答的問題 | 理解範例 |
| --- | --- | --- | --- |
| **RTO** | Recovery Time Objective | 最多能中斷多久？ | RTO 1 小時：目標在 1 小時內恢復服務 |
| **RPO** | Recovery Point Objective | 最多可遺失多長時間的資料？ | RPO 15 分鐘：目標最多遺失最近 15 分鐘的資料 |

> 範例為理解用補充；RPO 的資料損失容忍度通常用時間衡量。

### 6.2 HA 與 DR

| 比較 | HA（High Availability，高可用） | DR（Disaster Recovery，災難復原） |
| --- | --- | --- |
| 目標 | 元件故障時持續服務 | 重大災難後恢復服務 |
| 常見做法 | Multi-AZ | Cross-Region |
| 講義中的時間尺度 | 秒至分鐘 | 分鐘至小時以上 |
| 核心問題 | 部分元件壞了，能否繼續運作？ | 整個區域失效，要如何恢復？ |

**高可用不等於備份：即使基礎設施正常，資料仍可能被誤刪。**

客戶須規劃備份／還原、Failover、DR Runbook（災難復原操作手冊），並實際演練與驗證。

### 6.3 跨 Region 災難復原四大策略

| 策略 | 作法 | 成本與復原特性 |
| --- | --- | --- |
| **Backup & Restore** | 依備份重建與還原服務 | 成本最低；RTO／RPO 通常較長 |
| **Pilot Light** | 異地保留核心元件待命 | 復原時仍須啟動或擴展其他元件 |
| **Warm Standby** | 異地持續運行縮小版完整環境 | 比 Pilot Light 更快恢復，成本也較高 |
| **Active-Active** | 多地同時提供服務 | 成本最高；目標為近乎即時持續服務 |

整體趨勢：**投入越多，通常可換取更快復原與更高可用性；實際 RTO／RPO 仍取決於設計與驗證。**

## 七、合規、稽核與安全工具

### 7.1 合規認證與 AWS Artifact

| 名稱 | 重點 |
| --- | --- |
| ISO 27001 | 資訊安全管理 |
| SOC 1／2／3 | 控制與稽核報告 |
| PCI DSS | 支付卡產業安全標準 |
| **AWS Artifact** | 取得 AWS 合規報告、SOC／ISO 相關文件及特定協議的入口 |

**AWS 通過合規認證，不代表客戶的應用與流程自動合規。**

### 7.2 控制措施的三種類型

| 類型 | 意義與例子 |
| --- | --- |
| Inherited（繼承控制） | 可採用 AWS 的基礎設施控制證據，例如實體安全與機房控制 |
| Shared（共同控制） | AWS 與客戶在各自範圍執行，例如修補與時間同步 |
| Customer-specific（客戶特定控制） | 客戶自行落實，例如應用邏輯、員工權限與資料治理 |

### 7.3 AWS 原生安全工具

| 工具 | 核心用途 | 記憶關鍵字 |
| --- | --- | --- |
| **GuardDuty** | 偵測可疑活動與威脅 | 威脅偵測 |
| **Inspector** | 評估漏洞／弱點 | 弱點掃描 |
| **Security Hub** | 集中安全發現與告警 | 集中管理 |
| **CloudTrail** | 記錄 API 活動，追查誰做了什麼 | 行為稽核 |
| **AWS Config** | 記錄資源設定、歷史與關聯，評估是否符合規則 | 設定與合規 |

#### CloudTrail vs. Config

- **CloudTrail**：誰、何時、從哪裡、呼叫了什麼 API？
- **Config**：資源設定是什麼？如何變更？是否符合規則？
- Config 規則例子：S3 是否加密、EC2 是否具備必要 Tag、SG 是否過度開放。

### 7.4 WAF vs. Shield

| 比較 | AWS WAF | AWS Shield |
| --- | --- | --- |
| 講義重點層級 | Layer 7（應用層） | Layer 3／4（網路／傳輸層） |
| 核心功能 | 過濾 HTTP 請求 | DDoS 防護 |
| 例子 | SQL Injection、XSS | 大量流量攻擊 |
| 記憶方式 | 檢查「請求內容」 | 防禦「流量攻擊」 |

Shield 有 Standard／Advanced；此表呈現講義的核心對照。

### 7.5 雲端滲透測試

- 測試範圍須符合 AWS 對允許服務的政策。
- 禁止破壞底層基礎設施或影響其他客戶。
- 測試前確認 Scope、Target、Rate Limit、帳號隔離與事件應變方式。
- **實作時以最新 AWS Penetration Testing Policy 為準。**

## 八、常見資安事故與責任判斷

### 8.1 S3 Bucket 被設成公開讀取

`錯誤設定 Public Read → 資料外洩`

- 屬於 **Security IN the Cloud：客戶責任**。
- AWS 提供 Block Public Access 等保護機制，客戶仍須正確配置。

### 8.2 Access Key 外洩後遭濫用

`金鑰上傳 GitHub → 自動掃描發現 → 呼叫 AWS API → 大量啟動 EC2／GPU → 挖礦與高額帳單`

講義列示的防禦措施：

- IAM Role：減少在程式中放置長期金鑰的需求。
- Secrets Manager：管理機密資訊。
- Git-secrets：檢查提交內容是否含機密。
- MFA：強化身分驗證。
- Cost Anomaly Detection：偵測異常費用。

> 各措施處理不同環節；不能只靠其中一項取代權限控管與機密管理。

## 九、多帳號與組織治理

### 9.1 為什麼使用 Multi-Account？

| 目的 | 效益 |
| --- | --- |
| 風險隔離 | 降低單一帳號事故的影響範圍 |
| 成本管理 | 按團隊、環境或系統分帳 |
| 集中治理 | 更容易管理組織政策與權限邊界 |

### 9.2 Organizations、OU、Control Tower、SCP

| 名稱 | 功能 |
| --- | --- |
| **AWS Organizations** | 管理多個 AWS 帳號與組織政策 |
| **OU（Organizational Unit）** | 群組化管理帳號，例如 Security、Prod、Dev |
| **AWS Control Tower** | 建立標準化 Landing Zone 與治理環境 |
| **SCP（Service Control Policy）** | 設定組織中帳號可使用權限的上限，作為治理防護欄 |

**SCP 是「權限上限」，不會直接授予權限，也不會取代 IAM Policy。**

SCP 範例：

1. 限制可使用的 Regions，避免資料落到未核准地區。
2. 禁止關閉或破壞 CloudTrail，保留稽核能力。

### 9.3 治理的四個面向

| 面向 | 常見措施 |
| --- | --- |
| 身分 | MFA、Least Privilege |
| 網路 | 標準化 VPC、Security Group |
| 紀錄 | CloudTrail、Config |
| 成本 | Budget、費用異常偵測 |

**將最佳實務設成預設值，用 Guardrails 降低人工檢查的負擔。**

## 十、架構選型與快速複習

### 10.1 選型前先問五個問題

1. **資料能放哪裡？** → 資料位置與合規。
2. **使用者在哪裡？** → 距離與延遲。
3. **需要哪些 AWS 服務？** → Region 的服務支援度。
4. **故障時要撐多久？** → HA、DR、RTO、RPO。
5. **預算與 TCO 是多少？** → 整體成本與取捨。

最終須在**成本、可用性、延遲、合規**之間取得適合需求的平衡。

### 10.2 易混淆觀念速查

| 觀念 | 正確理解 |
| --- | --- |
| Region／AZ／資料中心 | Region 包含多個 AZ；每個 AZ 可包含一個或多個資料中心 |
| AZ Name／AZ ID | 跨帳號對齊實體 AZ 要看 AZ ID |
| Edge／Region | Edge 提供就近快取與加速；Region 提供核心運算與資料服務 |
| OF／IN the Cloud | AWS 負責底層雲端；客戶負責自身使用、資料與設定 |
| EC2／RDS | EC2 的客體 OS 修補由客戶負責；RDS 的底層 OS 由 AWS 維運 |
| Serverless／安全責任 | 不管理伺服器，仍須管理程式碼、IAM、資料與設定 |
| HA／備份 | 高可用無法取代防誤刪、備份與還原 |
| HA／DR | HA 著重故障時持續服務；DR 著重災後恢復 |
| RTO／RPO | RTO 看恢復時間；RPO 看可容忍資料損失的時間範圍 |
| CloudTrail／Config | CloudTrail 查操作行為；Config 查設定狀態與合規 |
| WAF／Shield | WAF 過濾應用請求；Shield 提供 DDoS 防護 |
| IAM／SCP | IAM 授予權限；SCP 限制權限上限 |
| AWS 合規／客戶合規 | AWS 的認證可作為部分證據；客戶仍須落實自己的控制措施 |

### 10.3 一句話記憶

> **選對 Region，用 Multi-AZ 提升可用性，用跨 Region 規劃 DR；AWS 保護雲端底層，客戶保護自己的資料、身分、程式與設定。**
