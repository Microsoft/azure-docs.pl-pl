---
title: Informacje o wersji dla Microsoft Azure Backup Server v3
description: Ten artykuł zawiera informacje o znanych problemach i obejściach programu Microsoft Azure Backup Server (serwera usługi MAB) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254265"
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

1. [Utwórz kopię zapasową](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) bazy danych SQL i odinstaluj serwera usługi MAB v2 (Wybierz, aby zachować chronione dane podczas odinstalowywania).
2. Uaktualnij do wersji SQL 2017 (Enterprise) i Odinstaluj raportowanie w ramach uaktualnienia.
3. [Zainstaluj](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) program SQL Server Reporting Services (SSRS).
4. [Zainstaluj](/sql/ssms/download-sql-server-management-studio-ssms) program SQL Server Management Studio (SSMS).
5. Skonfiguruj raportowanie przy użyciu parametrów zgodnie z opisem w temacie [Konfiguracja usług SSRS w programie SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Zainstaluj](backup-azure-microsoft-azure-backup.md) program SERWERA USŁUGI MAB V3.
7. [Przywróć](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL za pomocą narzędzia SSMS i uruchom narzędzie DPM-Sync, zgodnie z opisem w [tym miejscu](/system-center/dpm/back-up-the-dpm-server#using-dpmsync).
8. Zaktualizuj Właściwość "bazy" w tabeli dbo.tbl_DLS_GlobalSetting przy użyciu następującego polecenia:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Uruchom usługę MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Po zainstalowaniu UR1 raporty serwera usługi MAB nie są aktualizowane przy użyciu nowych plików RDL

**Opis**: w przypadku UR1 problem z formatowaniem raportu serwera usługi MAB jest rozwiązywany przy użyciu zaktualizowanych plików RDL. Nowe pliki RDL nie są automatycznie zastępowane istniejącymi plikami.

**Obejście**: aby zastąpić pliki RDL, wykonaj następujące czynności:

1. Na maszynie serwera usługi MAB Otwórz adres URL portalu internetowego usług SQL Reporting Services.
1. W adresie URL portalu sieci Web folder katalog dpmreports jest obecny w formacie **`DPMReports_<GUID>`**

    >[!NOTE]
    >Istnieje zawsze tylko jeden folder z tą konwencją nazewnictwa. Jeśli serwera usługi MAB jest uaktualniana z poprzedniej wersji, może istnieć również inny starszy folder, ale nie będzie można go otworzyć.

    ![Folder katalog dpmreports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Wybierz i Otwórz **`DPMReports_<GUID>`** folder. Poszczególne pliki raportów zostaną wyświetlone jak pokazano poniżej.

    ![Lista poszczególnych plików raportów](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Wybierz pliki raportu, które nie kończą się **raportem**, kliknij prawym przyciskiem myszy **opcję** Włącz i wybierz pozycję **Zarządzaj**.

    ![Wybierz pozycję Zarządzaj dla plików raportów](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Na stronie nowy wybierz opcję **Zamień** , aby zastąpić pliki najnowszymi plikami raportu.

    Najnowsze pliki raportów można znaleźć w ścieżce `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Na przykład: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Zastąp pliki najnowszymi plikami raportów](./media/backup-mabs-release-notes-v3/replace-files.png)

    Po wymianie plików upewnij się, że **Nazwa** i **Opis** są nienaruszone i nie są puste.

1. Po wymianie plików uruchom ponownie usługi serwera usługi MAB i Użyj plików raportów.

## <a name="next-steps"></a>Następne kroki

[Co nowego w programie serwera usługi MAB](backup-mabs-whats-new-mabs.md)
