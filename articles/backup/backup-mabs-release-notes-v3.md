---
title: Informacje o wersji dla Microsoft Azure Backup Server v3
description: Ten artykuł zawiera informacje o znanych problemach i obejściach programu Microsoft Azure Backup Server (serwera usługi MAB) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a5c99bcb95fde39bddc9e9db9ab000881c89081a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185629"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Informacje o wersji programu Microsoft Azure Backup Server

W tym artykule przedstawiono znane problemy i rozwiązania dla programu Microsoft Azure Backup Server (serwera usługi MAB) v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Tworzenie kopii zapasowych i odzyskiwanie nie powiedzie się w przypadku obciążeń klastrowanych

**Opis:** Tworzenie kopii zapasowej/przywracanie nie powiedzie się w przypadku klastrowanych źródeł danych, takich jak klaster funkcji Hyper-V lub klaster SQL (zawsze włączone) lub program Exchange w grupie dostępności bazy danych (DAG) po uaktualnieniu serwera usługi MAB v2 do wersji serwera usługi MAB v3.

Obejście **:** Aby temu zapobiec, Otwórz SQL Server Management Studio (SSMS)) i uruchom następujący skrypt SQL w bazie danych programu DPM:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Uaktualnianie do programu serwera usługi MAB V3 nie powiodło się w ustawieniach regionalnych rosyjskich

**Opis:** Uaktualnianie z wersji serwera usługi MAB v2 do serwera usługi MAB V3 w rosyjskich ustawieniach regionalnych kończy się niepowodzeniem z kodem błędu **4387**.

Obejście **:** Wykonaj następujące kroki, aby uaktualnić program do wersji serwera usługi MAB v3 przy użyciu pakietu instalacji Rosyjskiej:

1. [Utwórz kopię zapasową](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) bazy danych SQL i odinstaluj serwera usługi MAB v2 (Wybierz, aby zachować chronione dane podczas odinstalowywania).
2. Uaktualnij do wersji SQL 2017 (Enterprise) i Odinstaluj raportowanie w ramach uaktualnienia.
3. [Zainstaluj](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) program SQL Server Reporting Services (SSRS).
4. [Zainstaluj](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) program SQL Server Management Studio (SSMS).
5. Skonfiguruj raportowanie przy użyciu parametrów zgodnie z opisem w temacie [Konfiguracja usług SSRS w programie SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Zainstaluj](backup-azure-microsoft-azure-backup.md) program SERWERA USŁUGI MAB V3.
7. [Przywróć](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL za pomocą programu SSMS i uruchom narzędzie synchronizacji programu DPM zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Zaktualizuj Właściwość "bazy" w tabeli dbo. tbl_DLS_GlobalSetting przy użyciu następującego polecenia:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Uruchom usługę MSDPM.

## <a name="next-steps"></a>Następne kroki

[Co nowego w programie serwera usługi MAB v3](backup-mabs-whats-new-mabs.md)
