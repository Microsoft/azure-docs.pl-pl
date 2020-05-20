---
title: Inspekcja wystąpienia zarządzanego
description: Dowiedz się, jak rozpocząć pracę z Azure SQL Database inspekcji wystąpienia zarządzanego przy użyciu języka T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 7656944af16db650ec1fea36f0bdefc81b99922e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654622"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Rozpoczynanie pracy z inspekcją wystąpienia zarządzanego usługi Azure SQL Database

Inspekcja [wystąpienia zarządzanego](sql-database-managed-instance.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , gdzie można znaleźć najbardziej aktualną listę certyfikatów zgodności SQL Database.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Skonfiguruj inspekcję serwera w usłudze Azure Storage

W poniższej sekcji opisano konfigurację inspekcji wystąpienia zarządzanego.

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Utwórz **kontener** usługi Azure Storage, w którym są przechowywane dzienniki inspekcji.

   1. Przejdź do usługi Azure Storage, w której chcesz przechowywać dzienniki inspekcji.

      > [!IMPORTANT]
      > Użyj konta magazynu w tym samym regionie, w którym znajduje się wystąpienie zarządzane, aby uniknąć operacji odczytu/zapisu między regionami. Jeśli konto magazynu znajduje się za Virtual Network lub zaporą, zobacz [udzielanie dostępu z sieci wirtualnej](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).

   1. Na koncie magazynu przejdź do **omówienia** , a następnie kliknij pozycję **obiekty blob**.

      ![Element widget obiektów blob platformy Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. W górnym menu kliknij pozycję **+ kontener** , aby utworzyć nowy kontener.

      ![Ikona tworzenia kontenera obiektów BLOB](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Podaj **nazwę**kontenera, ustaw poziom dostępu publicznego na **prywatny**, a następnie kliknij przycisk **OK**.

      ![Utwórz konfigurację kontenera obiektów BLOB](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Klient chcący skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych powinien postępować zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Upewnij się, że wybrano opcję **Zezwalaj na dodatkowe dołączenia** podczas konfigurowania niezmiennego magazynu obiektów BLOB).
  
3. Po utworzeniu kontenera dla dzienników inspekcji istnieją dwa sposoby skonfigurowania go jako element docelowy dla dzienników inspekcji: [przy użyciu języka T-SQL](#blobtsql) lub [interfejsu użytkownika SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Konfigurowanie magazynu blogów dla dzienników inspekcji przy użyciu języka T-SQL:

     1. Na liście kontenery kliknij nowo utworzony kontener, a następnie kliknij pozycję **właściwości kontenera**.

        ![Przycisk właściwości kontenera obiektów BLOB](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Skopiuj adres URL kontenera, klikając ikonę kopiowania i zapisując adres URL (na przykład w Notatniku) do użytku w przyszłości. Format adresu URL kontenera powinien być`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adres URL kopiowania kontenera obiektów BLOB](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Wygeneruj **token SAS** usługi Azure Storage, aby udzielić uprawnień dostępu do inspekcji wystąpienia zarządzanego na koncie magazynu:

        - Przejdź do konta usługi Azure Storage, na którym został utworzony kontener w poprzednim kroku.

        - Kliknij pozycję **sygnatura dostępu współdzielonego** w menu ustawienia magazynu.

          ![Ikona sygnatury dostępu współdzielonego w menu ustawienia magazynu](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Skonfiguruj sygnaturę dostępu współdzielonego w następujący sposób:

          - **Dozwolone usługi**: obiekt BLOB

          - **Data rozpoczęcia**: aby uniknąć problemów dotyczących strefy czasowej, zaleca się użycie wczoraj daty

          - **Data zakończenia**: Wybierz datę wygaśnięcia tego tokenu sygnatury dostępu współdzielonego

            > [!NOTE]
            > Odnów token po wygaśnięciu, aby uniknąć błędów inspekcji.

          - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.
            
            ![Konfiguracja SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknięciu przycisku Generuj sygnaturę dostępu współdzielonego w dolnej części pojawi się token SAS. Skopiuj token, klikając ikonę kopiowania i zapisując ją (na przykład w Notatniku) do użycia w przyszłości.

          ![Kopiuj token SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Usuń znak zapytania ("?") od początku tokenu.

     1. Połącz się z wystąpieniem zarządzanym za pośrednictwem SQL Server Management Studio (SSMS) lub dowolnego innego obsługiwanego narzędzia.

     1. Wykonaj następującą instrukcję T-SQL, aby **utworzyć nowe poświadczenie** przy użyciu adresu URL kontenera i tokenu sygnatury dostępu współdzielonego, który został utworzony w poprzednich krokach:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Wykonaj następującą instrukcję T-SQL, aby utworzyć nową inspekcję serwera (wybierz własną nazwę inspekcji, użyj adresu URL kontenera utworzonego w poprzednich krokach). Jeśli nie zostanie określony, `RETENTION_DAYS` wartością domyślną jest 0 (nieograniczony czas przechowywania):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Kontynuuj przez [utworzenie specyfikacji inspekcji serwera lub specyfikacji inspekcji bazy danych](#createspec)

   - <a id="blobssms"></a>Konfigurowanie usługi BLOB Storage na potrzeby dzienników inspekcji przy użyciu SQL Server Management Studio (SSMS) 18 (wersja zapoznawcza):

     1. Połącz się z wystąpieniem zarządzanym przy użyciu interfejsu użytkownika SQL Server Management Studio (SSMS).

     1. Rozwiń główną uwagę Eksplorator obiektów.

     1. Rozwiń węzeł **zabezpieczenia** , kliknij prawym przyciskiem myszy węzeł **inspekcje** , a następnie kliknij pozycję "Nowa inspekcja":

        ![Rozwiń węzeł Zabezpieczenia i inspekcja](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Upewnij się, że wybrano "URL" w **miejscu docelowym inspekcji** , a następnie kliknij przycisk **Przeglądaj**:

        ![Przeglądaj usługę Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. Obowiązkowe Zaloguj się do konta platformy Azure:

        ![Logowanie do platformy Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Wybierz subskrypcję, konto magazynu i kontener obiektów blob z listy rozwijanej lub Utwórz własny kontener, klikając pozycję **Utwórz**. Po zakończeniu kliknij przycisk **OK**:

        ![Wybierz subskrypcję platformy Azure, konto magazynu i kontener obiektów BLOB](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Kliknij przycisk **OK** w oknie dialogowym "Tworzenie inspekcji".

4. <a id="createspec"></a>Po skonfigurowaniu kontenera obiektów BLOB jako celu dla dzienników inspekcji należy utworzyć i włączyć specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych w taki sposób, jak w przypadku SQL Server:

   - [Utwórz Podręcznik T-SQL dla specyfikacji inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik tworzenia instrukcji języka T-SQL dla specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Włącz inspekcję serwera utworzoną w kroku 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Dodatkowe informacje:

- [Inspekcja różnic między pojedynczymi bazami danych, pulami elastycznymi i wystąpieniami zarządzanymi w Azure SQL Database i bazach danych w SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [UTWÓRZ INSPEKCJĘ SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Skonfiguruj inspekcję serwera do centrum zdarzeń lub dzienników Azure Monitor

Dzienniki inspekcji z wystąpienia zarządzanego mogą być wysyłane do nawet centrów lub dzienników Azure Monitor. W tej sekcji opisano sposób konfigurowania tego programu:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/) do wystąpienia zarządzanego.

2. Kliknij pozycję **Ustawienia diagnostyczne**.

3. Kliknij pozycję **Włącz diagnostykę**. Jeśli Diagnostyka jest już włączona, zamiast tego zostanie wyświetlone *ustawienie + Dodaj diagnostykę* .

4. Wybierz pozycję **SQLSecurityAuditEvents** na liście dzienników.

5. Wybierz lokalizację docelową dla zdarzeń inspekcji — centrum zdarzeń, dzienniki Azure Monitor lub oba te elementy. Skonfiguruj dla każdego obiektu docelowego wymagane parametry (np. Log Analytics obszar roboczy).

6. Kliknij przycisk **Zapisz**.

    ![Konfigurowanie ustawień diagnostycznych](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Połącz się z wystąpieniem zarządzanym przy użyciu programu **SQL Server Management Studio (SSMS)** lub dowolnego innego obsługiwanego klienta.

8. Wykonaj następującą instrukcję T-SQL, aby utworzyć inspekcję serwera:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Utwórz i Włącz specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych w taki sposób, jak w przypadku SQL Server:

   - [Utwórz Podręcznik T-SQL dla specyfikacji inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik tworzenia instrukcji języka T-SQL dla specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Włącz inspekcję serwera utworzoną w kroku 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Korzystanie z dzienników inspekcji

### <a name="consume-logs-stored-in-azure-storage"></a>Korzystanie z dzienników przechowywanych w usłudze Azure Storage

Istnieje kilka metod, których można użyć do wyświetlania dzienników inspekcji obiektów BLOB.

- Użyj funkcji system `sys.fn_get_audit_file` (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [dokumentację sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów BLOB w kontenerze zdefiniowanym do przechowywania dzienników inspekcji. Aby uzyskać więcej szczegółowych informacji na temat hierarchii folderu magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [dokumentacja formatu dziennika inspekcji obiektów BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

- Aby zapoznać się z pełną listą metod korzystania z dzienników inspekcji, zapoznaj się z tematem Rozpoczynanie [pracy z inspekcją bazy danych SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Korzystanie z dzienników przechowywanych w centrum zdarzeń

Aby korzystać z danych inspekcji dzienników z centrum zdarzeń, należy skonfigurować strumień, który będzie korzystał z zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, zobacz dokumentację usługi Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Wykorzystywanie i analizowanie dzienników przechowywanych w dziennikach Azure Monitor

Jeśli dzienniki inspekcji są zapisywane w dziennikach Azure Monitor, są one dostępne w obszarze roboczym Log Analytics, gdzie można uruchamiać zaawansowane wyszukiwania na danych inspekcji. Jako punkt początkowy przejdź do obszaru roboczego Log Analytics i w obszarze *Ogólne* kliknij pozycję *dzienniki* , a następnie wprowadź proste zapytanie, takie jak: `search "SQLSecurityAuditEvents"` Aby wyświetlić dzienniki inspekcji.  

Dzienniki Azure Monitor udostępniają usługi operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, które umożliwiają łatwe analizowanie milionów rekordów na wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe informacje dotyczące Azure Monitor języka i poleceń wyszukiwania dzienników, zobacz artykuł [Azure monitor Logs Search Reference](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Inspekcja różnic między bazami danych w Azure SQL Database i bazami danych w SQL Server

Kluczowe różnice między inspekcją w bazach danych w Azure SQL Database i bazach danych w SQL Server są następujące:

- Przy użyciu opcji wdrożenia wystąpienia zarządzanego w Azure SQL Database Inspekcja działa na poziomie serwera i przechowuje `.xel` pliki dziennika w usłudze Azure Blob Storage.
- W SQL Server maszynach lokalnych/wirtualnych Inspekcja działa na poziomie serwera, ale przechowuje zdarzenia dotyczące systemu plików system/dzienniki zdarzeń systemu Windows.

Inspekcja systemu XEvent w wystąpieniu zarządzanym obsługuje cele usługi Azure Blob Storage. Dzienniki plików i systemu Windows **nie są obsługiwane**.

Kluczowe różnice w `CREATE AUDIT` składni inspekcji usługi Azure Blob Storage są następujące:

- `TO URL`Dostępna jest nowa składnia i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure, w którym `.xel` umieszczane są pliki.
- Podano nową składnię `TO EXTERNAL MONITOR` umożliwiającą włączenie elementów docelowych dzienników koncentratora i Azure monitor.
- Składnia `TO FILE` nie jest **obsługiwana** , ponieważ SQL Database nie może uzyskać dostępu do udziałów plików systemu Windows.
- Opcja zamykania **nie jest obsługiwana**.
- `queue_delay`wartość 0 **nie jest obsługiwana**.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z pełną listą metod korzystania z dzienników inspekcji, zapoznaj się z tematem Rozpoczynanie [pracy z inspekcją bazy danych SQL](sql-database-auditing.md).
- Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , gdzie można znaleźć najbardziej aktualną listę certyfikatów zgodności SQL Database.

<!--Image references-->









