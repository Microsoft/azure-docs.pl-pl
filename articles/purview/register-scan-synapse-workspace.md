---
title: Jak skanować obszary robocze usługi Azure Synapse
description: Dowiedz się, jak skanować obszar roboczy Synapse w usłudze Azure kontrolą Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031446"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Rejestrowanie i Skanowanie obszarów roboczych usługi Azure Synapse

W tym artykule opisano sposób rejestrowania obszaru roboczego usługi Azure Synapse w programie kontrolą i konfigurowania na nim skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Skany obszaru roboczego usługi Azure Synapse obsługują przechwytywanie metadanych i schematów dla baz danych SQL dedykowanych i bezserwerowych. Automatycznie klasyfikuje dane w oparciu o system i niestandardowe reguły klasyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą
- Konfigurowanie uwierzytelniania zgodnie z opisem w poniższych sekcjach

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Konfigurowanie uwierzytelniania do wyliczania dedykowanych zasobów usługi SQL Database w obszarze roboczym Synapse

1. Przejdź do **grupy zasobów** lub **subskrypcji** , w której znajduje się obszar roboczy Synapse, w Azure Portal.  
1. Wybierz pozycję **Access Control (IAM)**   z menu nawigacji po lewej stronie 
1. Aby dodać rolę do grupy zasobów lub subskrypcji, musisz być administratorem dostępu właściciela lub użytkownika. Wybierz przycisk *+ Dodaj* . 
1. Ustaw rolę **czytelnika** i wprowadź nazwę konta usługi Azure kontrolą (która reprezentuje jego plik msi) w obszarze Wybieranie pola wejściowego. Kliknij przycisk *Zapisz* , aby zakończyć przypisanie roli.
1. Wykonaj kroki od 2 do 4 powyżej, aby dodać również rolę **czytnika danych obiektów blob magazynu** dla usługi Azure kontrolą MSI w grupie zasobów lub subskrypcji, w której znajduje się obszar roboczy Synapse.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Konfigurowanie uwierzytelniania do wyliczania zasobów usługi SQL Database bezserwerowych w obszarze roboczym Synapse

> [!NOTE]
> Aby uruchamiać te polecenia, musisz być **administratorem Synapse** w obszarze roboczym. Więcej informacji o uprawnieniach Synapse [znajdziesz tutaj](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Przejdź do obszaru roboczego Synapse
1. Przejdź do sekcji **dane** i do jednej z baz danych SQL bezserwerowych
1. Kliknij ikonę wielokropka i Rozpocznij nowy skrypt SQL
1. Dodaj plik MSI konta usługi Azure kontrolą (reprezentowany przez nazwę konta) jako **sysadmin** w bazach danych SQL bez serwera, uruchamiając poniższe polecenie w skrypcie SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Konfigurowanie uwierzytelniania do skanowania zasobów w obszarze roboczym Synapse

Istnieją trzy sposoby konfigurowania uwierzytelniania dla źródła usługi Azure Synapse:

- Tożsamość zarządzana
- Jednostka usługi
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Korzystanie z tożsamości zarządzanej dla dedykowanych baz danych SQL

1. Przejdź do **obszaru roboczego Synapse**
1. Przejdź do sekcji **dane** i do jednej z baz danych SQL bezserwerowych
1. Kliknij ikonę wielokropka i Rozpocznij nowy skrypt SQL
1. Dodaj plik MSI konta usługi Azure kontrolą (reprezentowany przez nazwę konta) jako **db_owner** w dedykowanej bazie danych SQL, uruchamiając poniższe polecenie w skrypcie SQL:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Używanie tożsamości zarządzanej dla baz danych SQL bezserwerowych

1. Przejdź do **obszaru roboczego Synapse**
1. Przejdź do sekcji **dane** i do jednej z baz danych SQL bezserwerowych
1. Kliknij ikonę wielokropka i Rozpocznij nowy skrypt SQL
1. Dodaj plik MSI konta usługi Azure kontrolą (reprezentowany przez nazwę konta) jako **sysadmin** w bazach danych SQL bez serwera, uruchamiając poniższe polecenie w skrypcie SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Używanie jednostki usługi dla dedykowanych baz danych SQL

> [!NOTE]
> Najpierw należy skonfigurować nowe **poświadczenie** typu jednostki usługi, wykonując [poniższe instrukcje.](manage-credentials.md)

1. Przejdź do **obszaru roboczego Synapse**
1. Przejdź do sekcji **dane** i do jednej z baz danych SQL bezserwerowych
1. Kliknij ikonę wielokropka i Rozpocznij nowy skrypt SQL
1. Dodaj **Identyfikator jednostki usługi** jako **db_owner** w dedykowanej bazie danych SQL, uruchamiając poniższe polecenie w skrypcie SQL:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Używanie nazwy głównej usługi dla baz danych SQL bezserwerowych

1. Przejdź do **obszaru roboczego Synapse**
1. Przejdź do sekcji **dane** i do jednej z baz danych SQL bezserwerowych
1. Kliknij ikonę wielokropka i Rozpocznij nowy skrypt SQL
1. Dodaj plik MSI konta usługi Azure kontrolą (reprezentowany przez nazwę konta) jako **sysadmin** w bazach danych SQL bez serwera, uruchamiając poniższe polecenie w skrypcie SQL:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Należy skonfigurować uwierzytelnianie dla każdej dedykowanej bazy danych SQL w obszarze roboczym Synapse, która ma zostać zarejestrowana i przeskanowana. Uprawnienia wymienione powyżej dla bezserwerowego programu SQL Database mają zastosowanie do wszystkich z nich w obszarze roboczym, np. należy uruchomić je tylko raz.
    
## <a name="register-an-azure-synapse-source"></a>Rejestrowanie źródła usługi Azure Synapse

Aby zarejestrować nowe źródło usługi Azure Synapse w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Synapse Analytics (wiele)**
1. Wybierz przycisk **Kontynuuj**

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Konfigurowanie źródła usługi Azure Synapse":::

Na ekranie **Rejestr sources (Azure Synapse Analytics)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Opcjonalnie wybierz **subskrypcję** do odfiltrowania.
1. **Wybierz nazwę obszaru roboczego Synapse** z listy rozwijanej. Punkty końcowe SQL są automatycznie wypełniane na podstawie wybranego obszaru roboczego. 
1. Wybierz **kolekcję** lub Utwórz nową (opcjonalnie)
1. **Zakończ** , aby zarejestrować źródło danych

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Wypełnij szczegóły dla źródła Synapse platformy Azure":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1. Przejdź do sekcji **sources (źródła** ).

1. Wybierz zarejestrowane źródło danych.

1. Kliknij pozycję Wyświetl szczegóły i wybierz pozycję **+ Nowe skanowanie** lub użyj ikony Skanuj szybką akcję na kafelku źródłowym

1. Wypełnij pola *Nazwa* i wybierz wszystkie typy zasobów, które mają zostać przeskanowane w ramach tego źródła. **SQL Database** jest jedynym obsługiwanym typem w obszarze roboczym Synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Skanowanie źródła usługi Azure Synapse":::

1. Wybierz **poświadczenie** , aby połączyć się z zasobami w ramach źródła danych. 
  
1. W każdym typie można wybrać opcję skanowania wszystkich zasobów lub ich podzbioru według nazwy.
1.  Kliknij przycisk **Kontynuuj** Aby kontynuować. 

1.  Wybierz **zestaw reguł skanowania** typu Azure Synapse SQL. Możesz również utworzyć wbudowane zestawy reguł skanowania.

1. Wybierz wyzwalacz skanowania. Można zaplanować uruchamianie go **co tydzień/miesięcznie** lub **raz**

1. Przejrzyj skanowanie i wybierz pozycję Zapisz, aby ukończyć konfigurację   

## <a name="viewing-your-scans-and-scan-runs"></a>Wyświetlanie skanów i uruchomień skanowania

1. Wyświetl szczegóły źródła, klikając pozycję **Wyświetl szczegóły** na kafelku w sekcji źródła. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Szczegóły źródła usługi Azure Synapse"::: 

1. Wyświetl szczegóły uruchomienia skanowania, przechodząc do strony **szczegółów skanowania** .
    1. *Pasek stanu* to krótkie podsumowanie stanu działania zasobów podrzędnych. Zostanie on wyświetlony na stronie skanowania na poziomie obszaru roboczego.
    1. Zielony oznacza powodzenie, a czerwony oznacza, że nie powiodło się. Szare oznacza, że skanowanie jest nadal w toku
    1. Możesz kliknąć poszczególne skanowania, aby wyświetlić bardziej szczegółowe szczegóły

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Szczegóły skanowania usługi Azure Synapse" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Wyświetl podsumowanie ostatnich nieudanych uruchomień skanowania w dolnej części strony szczegółów źródła. Możesz również kliknąć pozycję aby wyświetlić bardziej szczegółowe szczegóły dotyczące tych przebiegów.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Zarządzanie skanowaniem — Edytuj, Usuń lub Anuluj
Aby zarządzać skanowaniem lub usunąć je, wykonaj następujące czynności:

- Przejdź do centrum zarządzania. Wybierz pozycję źródła danych w sekcji źródła i skanowanie, a następnie wybierz odpowiednie źródło danych.

- Wybierz skanowanie, którymi chcesz zarządzać. Skanowanie można edytować, wybierając pozycję Edytuj.

- Możesz usunąć skanowanie, wybierając pozycję Usuń.
- Jeśli skanowanie jest uruchomione, można je również anulować.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)   