---
title: 將 SQL Server 資料庫備份至 Azure | Microsoft Docs
description: 本教學課程說明如何將 SQL Server 備份至 Azure。 本文也將說明 SQL Server 復原。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847306"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>關於 Azure VM 中的 SQL Server 備份

SQL Server 資料庫是需要低復原點目標 (RPO) 和長期保留的重要工作負載。 您可以使用 [Azure 備份](backup-overview.md)來備份在 Azure VM 上執行的 SQL Server 資料庫。

## <a name="backup-process"></a>備份程序

此解決方案會利用 SQL 原生 API 來備份您的 SQL 資料庫。

* 在您指定想要保護的 SQL Server VM 並查詢此 VM 中的資料庫之後，「Azure 備份」服務會在此 VM 上安裝名為 `AzureBackupWindowsWorkload` 的工作負載備份延伸模組。
* 此延伸模組是由一個協調器和一個 SQL 外掛程式所組成。 協調器負責觸發各種作業 (例如設定備份、備份和還原) 的工作流程，而外掛程式則負責實際的資料流程。
* 為了能夠在此 VM 上探索資料庫，「Azure 備份」會建立 `NT SERVICE\AzureWLBackupPluginSvc` 帳戶。 此帳戶會用於備份和還原，且必須具備 SQL 系統管理員 (sysadmin) 權限。 「Azure 備份」會利用 `NT AUTHORITY\SYSTEM` 帳戶來進行資料庫探索/查詢，因此這個帳戶必須是 SQL 上的公開登入帳戶。 如果您未從 Azure Marketplace 建立 SQL Server VM，您可能會收到  **UserErrorSQLNoSysadminMembership** 錯誤。 若發生此狀況，請 [依照這些指示進行操作](backup-azure-sql-database.md)。
* 在您於選取的資料庫上觸發設定保護之後，備份服務便會為協調器設定備份排程及其他原則詳細資料，延伸模組會將這些都快取在 VM 本機 
* 在排定的時間，協調器會與外掛程式進行通訊，然後使用 VDI 開始從 SQL Server 串流處理備份資料。  
* 外掛程式會將資料直接傳送給復原服務保存庫，因此不需要暫存位置。 「Azure 備份」服務會將資料加密並儲存在儲存體帳戶中。
* 當資料傳輸完成時，協調器會向備份服務確認認可。

  ![SQL 備份架構](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>開始之前

開始之前，請確認下列事項：

1. 請確定您具有在 Azure 中執行的 SQL Server 執行個體。 您可以在 Marketplace 中[快速建立 SQL Server 執行個體](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。
2. 檢閱[功能考量](#feature-consideration-and-limitations)和[案例支援](#scenario-support)。
3. [檢閱關於此案例的常見問題](faq-backup-sql-server.md)。

## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的部署** | 支援 SQL Marketplace Azure VM 和非 Marketplace (手動安裝 SQL Server) VM。
**支援的地區** | 澳大利亞東南部 (ASE)、澳大利亞東部 (AE) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大東部 (CE)<br> 東南亞 (SEA)、東亞 (EA) <br> 美國東部 (EUS)、美國東部 2 (EUS2)、美國中西部 (WCUS)、美國西部 (WUS)、美國西部 2 (WUS2)、美國中北部 (NCUS)、美國中部 (CUS)、美國中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS) <br> 日本東部 (JPE)、日本西部 (JPW) <br> 南韓中部 (KRC)、南韓南部 (KRS) <br> 北歐 (NE)、西歐 <br> 英國南部 (UKS)、英國西部 (UKW)
**受支援的作業系統** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> 目前不支援 Linux。
**支援的 SQL Server 版本** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。
**支援的 .NET 版本** | 安裝在 VM 上的 .NET Framework 4.5.2 和更新版本

## <a name="feature-consideration-and-limitations"></a>功能考量和限制

- 您可以透過 Azure 入口網站或 **PowerShell** 來設定 SQL Server 備份。 我們不支援 CLI。
- 執行 SQL Server 的 VM 需要有網際網路連線能力，才能存取 Azure 公用 IP 位址。
- 不支援 SQL Server **容錯移轉叢集執行個體 (FCI)** 和 SQL Server Always On 容錯移轉叢集執行個體。
- 不支援鏡像資料庫和資料庫快照集的備份和還原作業。
- 使用多個備份解決方案來備份獨立 SQL Server 執行個體或 SQL Always On 可用性群組可能會導致備份失敗；請避免這麼做。
- 使用相同或不同的解決方案來備份可用性群組的兩個節點，也可能導致備份失敗。 「Azure 備份」可以偵測並保護與保存庫位於相同區域中的所有節點。 如果您的 SQL Always On 可用性群組跨多個 Azure 區域，請從具有主要節點的區域設定備份。 「Azure 備份」可以依據您的備份喜好設定，偵測並保護可用性群組中的所有資料庫。  
- 「Azure 備份」針對**唯讀**資料庫僅支援「完整」和「只複製完整」備份
- 無法保護含有大量檔案的資料庫。 支援的檔案數目上限為 **1000** 個。  
- 您最多可在保存庫中備份 **2000** 個 SQL Server 資料庫。 如果您有更多資料庫，則可以建立多個保存庫。
- 您最多可以一次設定 **50** 個資料庫的備份；此限制有助於將備份負載最佳化。
- 我們可支援的資料庫大小上限為 **2TB**；如果大小超出此上限，則可能發生效能問題。
- 若要瞭解每一伺服器可保護多少個資料庫，我們需要考慮頻寬、VM 大小、備份頻率、資料庫大小等因素。我們正努力推出一個可協助您自行計算這些數字的規劃工具。 我們很快就會發佈此工具。
- 如果是可用性群組，則會根據幾個因素，從不同節點進行備份。 可用性群組的備份行為摘述於下方。

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Alaways On 可用性群組的備份行為

視備份喜好設定和備份類型 (完整/差異/記錄/只複製完整) 而定，會從特定節點 (主要/次要) 進行備份。

- **備份喜好設定：主要**

**備份類型** | **Node**
    --- | ---
    完整 | 主要
    差異 | 主要
    記錄檔 |  主要
    只複製完整 |  主要

- **備份喜好設定：僅次要**

**備份類型** | **Node**
--- | ---
完整 | 主要
差異 | 主要
記錄檔 |  次要
只複製完整 |  次要

- **備份喜好設定：次要**

**備份類型** | **Node**
--- | ---
完整 | 主要
差異 | 主要
記錄檔 |  次要
只複製完整 |  次要

- **沒有備份喜好設定**

**備份類型** | **Node**
--- | ---
完整 | 主要
差異 | 主要
記錄檔 |  次要
只複製完整 |  次要

## <a name="fix-sql-sysadmin-permissions"></a>修正 SQL 系統管理員權限

  若因 **UserErrorSQLNoSysadminMembership** 錯誤而需要修正權限，請執行下列動作：

  1. 使用具有 SQL Server 系統管理員權限的帳戶登入 SQL Server Management Studio (SSMS)。 除非您需要特殊權限，否則 Windows 驗證應該能運作。
  2. 在 SQL Server 上，開啟 [安全性]/[登入] 資料夾。

      ![開啟 [安全性]/[登入] 資料夾來查看帳戶](./media/backup-azure-sql-database/security-login-list.png)

  3. 以滑鼠右鍵按一下 [登入] 資料夾，然後選取 [新增登入]。 在 [登入 - 新增] 中，選取 [搜尋]。

      ![在 [登入 - 新增] 對話方塊中，選取 [搜尋]](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 虛擬服務帳戶 **NT SERVICE\AzureWLBackupPluginSvc** 已於虛擬機器註冊期間和 SQL 探索階段建立。 請輸入 [輸入要選取的物件名稱] 中顯示的帳戶名稱。 選取 [檢查名稱] 以解析名稱。 按一下 [確定]。

      ![選取 [檢查名稱] 以解析未知的服務名稱](./media/backup-azure-sql-database/check-name.png)

  5. 在 [伺服器角色] 中，確定已選取**系統管理員**角色。 按一下 [確定]。 現在應該存在必要權限。

      ![確定已選取系統管理員伺服器角色](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 現在，請建立資料庫與復原服務保存庫的關聯。 在 Azure 入口網站的 [受保護的伺服器] 清單中，以滑鼠右鍵按一下處於錯誤狀態的伺服器，然後選取 [重新探索資料庫]。

      ![確認伺服器具有適當的權限](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. 在 [通知] 區域中查看進度。 找到選取的資料庫之後，即會出現成功訊息。

      ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>後續步驟

- [了解](backup-sql-server-database-azure-vms.md)如何備份 SQL Server 資料庫。
- [了解](restore-sql-database-azure-vm.md)如何還原已備份的 SQL Server 資料庫。
- [了解](manage-monitor-sql-database-backup.md)如何管理已備份的 SQL Server 資料庫。
