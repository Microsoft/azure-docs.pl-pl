---
title: Inspekcja wystąpienia zarządzanego SQL
description: Dowiedz się, jak rozpocząć pracę z inspekcją wystąpienia zarządzanego usługi Azure SQL przy użyciu języka T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575475"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Wprowadzenie do inspekcji wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Inspekcja [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), w którym można znaleźć najbardziej aktualną listę certyfikatów zgodności.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Skonfiguruj inspekcję serwera w usłudze Azure Storage

W poniższej sekcji opisano konfigurację inspekcji wystąpienia zarządzanego.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Utwórz **kontener** usługi Azure Storage, w którym są przechowywane dzienniki inspekcji.

   1. Przejdź do konta usługi Azure Storage, na którym chcesz przechowywać dzienniki inspekcji.

      > [!IMPORTANT]
      > - Użyj konta magazynu w tym samym regionie, w którym znajduje się wystąpienie zarządzane, aby uniknąć operacji odczytu/zapisu między regionami. 
      > - Jeśli konto magazynu znajduje się za Virtual Network lub zaporą, zobacz [udzielanie dostępu z sieci wirtualnej](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Jeśli zmienisz okres przechowywania z 0 (nieograniczony czas przechowywania) na inną wartość, należy pamiętać, że przechowywanie będzie dotyczyło tylko dzienników utworzonych po zmianie wartości przechowywania (dzienniki zapisane w okresie, gdy czas przechowywania był ustawiony na nieograniczony, nawet po włączeniu przechowywania).

   1. Na koncie magazynu przejdź do **omówienia** , a następnie kliknij pozycję **obiekty blob**.

      ![Widżet obiektów blob platformy Azure](./media/auditing-configure/1_blobs_widget.png)

   1. W górnym menu kliknij pozycję **+ kontener** , aby utworzyć nowy kontener.

      ![Ikona tworzenia kontenera obiektów BLOB](./media/auditing-configure/2_create_container_button.png)

   1. Podaj **nazwę** kontenera, ustaw **poziom dostępu publicznego** na **prywatny**, a następnie kliknij przycisk **OK**.

      ![Utwórz konfigurację kontenera obiektów BLOB](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Klienci chcący skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych powinni postępować zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). (Upewnij się, że wybrano opcję **Zezwalaj na dodatkowe dołączenia** podczas konfigurowania niezmiennego magazynu obiektów BLOB).
  
3. Po utworzeniu kontenera dla dzienników inspekcji istnieją dwa sposoby skonfigurowania go jako element docelowy dla dzienników inspekcji: [przy użyciu języka T-SQL](#blobtsql) lub [interfejsu użytkownika SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>**Konfigurowanie usługi BLOB Storage na potrzeby dzienników inspekcji przy użyciu języka T-SQL:**

     1. Na liście kontenery kliknij nowo utworzony kontener, a następnie kliknij pozycję **właściwości kontenera**.

        ![Przycisk właściwości kontenera obiektów BLOB](./media/auditing-configure/4_container_properties_button.png)

     1. Skopiuj adres URL kontenera, klikając ikonę kopiowania i zapisując adres URL (na przykład w Notatniku) do użytku w przyszłości. Format adresu URL kontenera powinien być `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adres URL kopiowania kontenera obiektów BLOB](./media/auditing-configure/5_container_copy_name.png)

     1. Wygeneruj **token SAS** usługi Azure Storage, aby udzielić uprawnień dostępu do inspekcji wystąpienia zarządzanego na koncie magazynu:

        - Przejdź do konta usługi Azure Storage, na którym został utworzony kontener w poprzednim kroku.

        - Kliknij pozycję **sygnatura dostępu współdzielonego** w menu **ustawienia magazynu** .

          ![Ikona sygnatury dostępu współdzielonego w menu ustawienia magazynu](./media/auditing-configure/6_storage_settings_menu.png)

        - Skonfiguruj sygnaturę dostępu współdzielonego w następujący sposób:

          - **Dozwolone usługi**: obiekt BLOB

          - **Data rozpoczęcia**: aby uniknąć problemów związanych ze strefą czasową, użyj daty wczoraj

          - **Data zakończenia**: Wybierz datę wygaśnięcia tego tokenu sygnatury dostępu współdzielonego

            > [!NOTE]
            > Odnów token po wygaśnięciu, aby uniknąć błędów inspekcji.

          - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.

            ![Konfiguracja SAS](./media/auditing-configure/7_sas_configure.png)

        - Token sygnatury dostępu współdzielonego pojawia się u dołu. Skopiuj token, klikając ikonę kopiowania i zapisując ją (na przykład w Notatniku) do użycia w przyszłości.

          ![Kopiuj token SAS](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Usuń znak zapytania ("?") od początku tokenu.

     1. Połącz się z wystąpieniem zarządzanym za pośrednictwem SQL Server Management Studio lub dowolnego innego obsługiwanego narzędzia.

     1. Wykonaj następującą instrukcję T-SQL, aby **utworzyć nowe poświadczenie** przy użyciu adresu URL kontenera i tokenu sygnatury dostępu współdzielonego, który został utworzony w poprzednich krokach:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Wykonaj następującą instrukcję T-SQL, aby utworzyć nową inspekcję serwera (wybierz własną nazwę inspekcji i użyj adresu URL kontenera utworzonego w poprzednich krokach). Jeśli nie zostanie określony, `RETENTION_DAYS` wartością domyślną jest 0 (nieograniczony czas przechowywania):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Kontynuuj przez [utworzenie specyfikacji inspekcji serwera lub specyfikacji inspekcji bazy danych](#createspec).

   - <a id="blobssms"></a>**Skonfiguruj magazyn obiektów BLOB dla dzienników inspekcji przy użyciu SQL Server Management Studio 18:**

     1. Połącz się z wystąpieniem zarządzanym przy użyciu interfejsu użytkownika SQL Server Management Studio.

     1. Rozwiń główną uwagę Eksplorator obiektów.

     1. Rozwiń węzeł **zabezpieczenia** , kliknij prawym przyciskiem myszy węzeł **inspekcje** , a następnie kliknij pozycję **Nowa Inspekcja**:

        ![Rozwiń węzeł Zabezpieczenia i inspekcja](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Upewnij się, że **adres URL** jest wybrany w **miejscu docelowym inspekcji** , a następnie kliknij przycisk **Przeglądaj**:

        ![Przeglądaj usługę Azure Storage](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Obowiązkowe Zaloguj się do konta platformy Azure:

        ![Logowanie do platformy Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Wybierz subskrypcję, konto magazynu i kontener obiektów blob z listy rozwijanej lub Utwórz własny kontener, klikając pozycję **Utwórz**. Po zakończeniu kliknij przycisk **OK**:

        ![Wybierz subskrypcję platformy Azure, konto magazynu i kontener obiektów BLOB](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Kliknij przycisk **OK** w oknie dialogowym **Tworzenie inspekcji** .
     
     1. <a id="createspec"></a>Po skonfigurowaniu kontenera obiektów BLOB jako celu dla dzienników inspekcji należy utworzyć i włączyć specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych w taki sposób, jak w przypadku SQL Server:

   - [Utwórz Podręcznik T-SQL dla specyfikacji inspekcji serwera](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik tworzenia instrukcji języka T-SQL dla specyfikacji inspekcji bazy danych](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Włącz inspekcję serwera utworzoną w kroku 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Dodatkowe informacje:

- [Inspekcja różnic między wystąpieniem zarządzanym usługi Azure SQL i bazą danych w SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [UTWÓRZ INSPEKCJĘ SERWERA](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Skonfiguruj inspekcję serwera do Event Hubs lub dzienników Azure Monitor

Dzienniki inspekcji z wystąpienia zarządzanego mogą być wysyłane do usługi Azure Event Hubs lub dzienników Azure Monitor. W tej sekcji opisano sposób konfigurowania tego programu:

1. Przejdź [Azure Portal](https://portal.azure.com/) do wystąpienia zarządzanego.

2. Kliknij pozycję **Ustawienia diagnostyczne**.

3. Kliknij pozycję **Włącz diagnostykę**. Jeśli Diagnostyka jest już włączona, a zamiast tego zostanie wyświetlone **ustawienie Dodaj diagnostykę** .

4. Wybierz pozycję **SQLSecurityAuditEvents** na liście dzienników.

5. Wybierz lokalizację docelową dla zdarzeń inspekcji: Event Hubs, dzienniki Azure Monitor lub oba te elementy. Skonfiguruj dla każdego obiektu docelowego wymagane parametry (np. Log Analytics obszar roboczy).

6. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie ustawień diagnostycznych](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Połącz się z wystąpieniem zarządzanym przy użyciu programu **SQL Server Management Studio (SSMS)** lub dowolnego innego obsługiwanego klienta.

8. Wykonaj następującą instrukcję T-SQL, aby utworzyć inspekcję serwera:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Utwórz i Włącz specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych w taki sposób, jak w przypadku SQL Server:

   - [Utwórz Podręcznik T-SQL dla specyfikacji inspekcji serwera](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik tworzenia instrukcji języka T-SQL dla specyfikacji inspekcji bazy danych](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Włącz inspekcję serwera utworzoną w kroku 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Korzystanie z dzienników inspekcji

### <a name="consume-logs-stored-in-azure-storage"></a>Korzystanie z dzienników przechowywanych w usłudze Azure Storage

Istnieje kilka metod, których można użyć do wyświetlania dzienników inspekcji obiektów BLOB.

- Użyj funkcji system `sys.fn_get_audit_file` (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [dokumentację sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów BLOB w kontenerze zdefiniowanym do przechowywania dzienników inspekcji. Aby uzyskać więcej szczegółowych informacji na temat hierarchii folderu magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [dokumentacja formatu dziennika inspekcji obiektów BLOB](../database/audit-log-format.md).

- Aby zapoznać się z pełną listą metod użycia dzienników inspekcji, zobacz [wprowadzenie do inspekcji Azure SQL Database](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Korzystanie z dzienników przechowywanych w Event Hubs

Aby można było korzystać z danych dzienników inspekcji z Event Hubs, należy skonfigurować strumień, który będzie używać zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, zobacz dokumentację usługi Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Wykorzystywanie i analizowanie dzienników przechowywanych w dziennikach Azure Monitor

Jeśli dzienniki inspekcji są zapisywane w dziennikach Azure Monitor, są one dostępne w obszarze roboczym Log Analytics, gdzie można uruchamiać zaawansowane wyszukiwania na danych inspekcji. Jako punkt początkowy przejdź do obszaru roboczego Log Analytics. W sekcji **Ogólne** kliknij pozycję **dzienniki** i wprowadź proste zapytanie, takie jak: `search "SQLSecurityAuditEvents"` Aby wyświetlić dzienniki inspekcji.  

Dzienniki Azure Monitor udostępniają usługi operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, które umożliwiają łatwe analizowanie milionów rekordów na wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe informacje dotyczące Azure Monitor języka i poleceń wyszukiwania dzienników, zobacz artykuł [Azure monitor Logs Search Reference](../../azure-monitor/logs/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Inspekcja różnic między bazami danych w wystąpieniu zarządzanym usługi Azure SQL i bazami danych w SQL Server

Kluczowe różnice między inspekcją w bazach danych w wystąpieniach zarządzanych usługi Azure SQL i bazach danych w SQL Server są następujące:

- W przypadku wystąpienia zarządzanego usługi Azure SQL Inspekcja działa na poziomie serwera i przechowuje `.xel` pliki dziennika w usłudze Azure Blob Storage.
- W SQL Server Inspekcja działa na poziomie serwera, ale przechowuje zdarzenia dotyczące systemu plików/dzienników zdarzeń systemu Windows.

Inspekcja systemu XEvent w wystąpieniach zarządzanych obsługuje cele usługi Azure Blob Storage. Dzienniki plików i systemu Windows **nie są obsługiwane**.

Kluczowe różnice w `CREATE AUDIT` składni inspekcji usługi Azure Blob Storage są następujące:

- `TO URL`Dostępna jest nowa składnia i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure, w którym `.xel` umieszczane są pliki.
- Podano nową składnię `TO EXTERNAL MONITOR` umożliwiającą włączenie Event Hubs i Azure monitor celów dzienników.
- Składnia `TO FILE` nie jest **obsługiwana** , ponieważ wystąpienie zarządzane SQL platformy Azure nie może uzyskać dostępu do udziałów plików systemu Windows.
- Opcja zamykania **nie jest obsługiwana**.
- `queue_delay` wartość 0 **nie jest obsługiwana**.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z pełną listą metod użycia dzienników inspekcji, zobacz [wprowadzenie do inspekcji Azure SQL Database](../../azure-sql/database/auditing-overview.md).
- Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), w którym można znaleźć najbardziej aktualną listę certyfikatów zgodności.

<!--Image references-->
