---
title: Przenoszenie zasobów usługi Azure SQL między regionami przy użyciu usługi Azure Resource przeprowadzki
description: Informacje na temat przenoszenia zasobów usługi Azure SQL do innego regionu za pomocą usługi Azure Resource przenoszącej
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9fe43125c83436f89bf93cbe975317efec2beb46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542817"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Samouczek: przenoszenie zasobów Azure SQL Database do innego regionu

W tym samouczku dowiesz się, jak przenieść bazy danych SQL Azure i pule elastyczne do innego regionu platformy Azure przy użyciu funkcji [przenoszenia zasobów platformy Azure](overview.md).

> [!NOTE]
> Usługa przenoszenia zasobów platformy Azure jest obecnie dostępna w wersji zapoznawczej.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne i wymagania.
> * Wybierz zasoby, które chcesz przenieść.
> * Rozwiąż zależności zasobów.
> * Przygotuj i Przenieś SQL Server do regionu docelowego.
> * Przygotowywanie i przenoszenie baz danych i pul elastycznych.
> * Zdecyduj, czy chcesz odrzucić lub zatwierdzić przeniesienie. 
> * Opcjonalnie można usunąć zasoby w regionie źródłowym po przeniesieniu. 

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

-  Sprawdź, czy masz dostęp *właściciela* do subskrypcji zawierającej zasoby, które chcesz przenieść.
    - Przy pierwszym dodawaniu zasobu dla określonej pary źródłowej i docelowej w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję.
    - Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor lub administratora dostępu użytkownika w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
- Subskrypcja wymaga wystarczającego limitu przydziału, aby utworzyć zasoby, które są przenoszone w regionie docelowym. Jeśli nie ma limitu przydziału, [Zażądaj dodatkowych limitów](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Sprawdź ceny i opłaty związane z regionem docelowym, do którego są przenoszone zasoby. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.
    

## <a name="check-sql-requirements"></a>Sprawdź wymagania SQL

1. [Sprawdź](support-matrix-move-region-sql.md) , które funkcje bazy danych/puli elastycznej są obsługiwane na potrzeby przechodzenia do innego regionu.
2. W regionie docelowym Utwórz serwer docelowy dla każdego serwera źródłowego. [Dowiedz się więcej](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Jeśli bazy danych są szyfrowane przy użyciu transparentu szyfrowanie danych (TDE) i używasz własnego klucza szyfrowania w Azure Key Vault, [Dowiedz się, jak](../key-vault/general/move-region.md) przenieść magazyny kluczy do innego regionu.
5. Jeśli synchronizacja danych SQL jest włączona, przeniesienie baz danych elementów członkowskich jest obsługiwane. Po przeniesieniu należy skonfigurować synchronizację danych SQL do nowej docelowej bazy danych.
6. Usuń zaawansowane ustawienia zabezpieczeń danych przed przeniesieniem. Po przeniesieniu [Skonfiguruj ustawienia](../azure-sql/database/azure-defender-for-sql.md) na poziomie SQL Server w regionie docelowym.
7. Jeśli inspekcja jest włączona, zasady są resetowane do domyślnych po przeniesieniu. Ponownie [Skonfiguruj inspekcję](../azure-sql/database/auditing-overview.md) po przeniesieniu.
7. Zasady przechowywania kopii zapasowej dla źródłowej bazy danych są przenoszone do docelowej bazy danych. [Dowiedz się więcej](../azure-sql/database/long-term-backup-retention-configure.md) o modyfikowaniu ustawień po przeniesieniu.
8. Przed przeniesieniem Usuń reguły zapory na poziomie serwera. Reguły zapory na poziomie bazy danych są kopiowane z serwera źródłowego na serwer docelowy podczas przenoszenia. Po przeniesieniu [Skonfiguruj reguły zapory](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) dla SQL Server w regionie docelowym.
9. Przed przeniesieniem Usuń ustawienia autodostrajania. Po przeniesieniu ponownie [Skonfiguruj ustawienia automatycznego dostrajania](../azure-sql/database/automatic-tuning-enable.md) .
10. Przed przeniesieniem Usuń ustawienia alertów bazy danych. [Zresetuj](../azure-sql/database/alerts-insights-configure-portal.md) po przeniesieniu.
    
## <a name="select-resources"></a>Wybieranie zasobów

Wybierz zasoby, które chcesz przenieść.

- Możesz wybrać dowolny obsługiwany typ zasobu w dowolnej grupie zasobów w wybranym regionie źródłowym.
- Przenoszenie zasobów do regionu docelowego w tej samej subskrypcji co region źródłowy. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po przeniesieniu zasobów.

1. W Azure Portal Wyszukaj pozycję *przeniesienie zasobów*. Następnie w obszarze **usługi** wybierz pozycję **Azure Resource** przenosząca.

     ![Wyniki wyszukiwania dla przenoszenia zasobów w Azure Portal](./media/tutorial-move-region-sql/search.png)

2. W obszarze **Przegląd** kliknij pozycję **Rozpocznij**.

    ![Przycisk dodawania zasobów do przenoszenia do innego regionu](./media/tutorial-move-region-sql/get-started.png)

3. W obszarze **Przenieś zasoby**  >  **Źródło + miejsce docelowe** wybierz źródłową subskrypcję i region.
4. W obszarze **Lokalizacja docelowa** wybierz region, do którego chcesz przenieść zasoby. Następnie kliknij przycisk **Dalej**.

    ![Strona umożliwiająca wybranie regionu źródłowego i docelowego](./media/tutorial-move-region-sql/source-target.png)

6. W obszarze **zasoby do przeniesienia** kliknij pozycję **Wybierz zasoby**.
7. W obszarze **Wybierz zasoby** Wybierz zasoby. Można dodawać tylko zasoby obsługiwane do przenoszenia. Następnie kliknij przycisk **gotowe**.

    ![Strona umożliwiająca wybranie zasobów SQL do przeniesienia](./media/tutorial-move-region-sql/select-resources.png)

8. W obszarze **zasoby do przeniesienia** kliknij przycisk **dalej**.

9. W obszarze **Recenzja + Dodawanie** Sprawdź ustawienia źródłowe i docelowe. Upewnij się, że rozumiesz, że metadane dotyczące przeniesienia będą przechowywane w grupie zasobów utworzonej w tym celu w regionie metadanych.


    ![Strona umożliwiająca przejrzenie ustawień i przejście do przenoszenia](./media/tutorial-move-region-sql/review.png)

10. Kliknij przycisk " **Zastosuj**", aby rozpocząć dodawanie zasobów.
11. Po pomyślnym zakończeniu dodawania kliknij pozycję **Dodaj zasoby do przeniesienia** w ikonie powiadomienia.
12. Po kliknięciu powiadomienia Przejrzyj zasoby na stronie **między regionami** .


> [!NOTE]
> 
> - SQL Server jest teraz w stanie *oczekiwania na ręczne przypisanie* .
> - Inne dodane zasoby są w stanie *oczekiwania na przygotowanie* .
> - Jeśli chcesz usunąć zasób z kolekcji Move, metoda dla operacji, która zależy od tego, gdzie jesteś w procesie przenoszenia. [Dowiedz się więcej](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Rozwiązywanie zależności


1. W **różnych regionach**, jeśli w kolumnie **problemy** zostanie wyświetlony komunikat *Sprawdź poprawność zależności* , kliknij przycisk **Weryfikuj zależności** . Rozpocznie się proces walidacji.
2. Jeśli znajdują się zależności, kliknij przycisk **Dodaj zależności**.

    ![Przycisk dodawania zależności](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. W obszarze **Dodaj zależności** Wybierz zasoby zależne > **Dodaj zależności**. Monitoruj postęp w powiadomieniach.

4. W razie konieczności Dodaj dodatkowe zależności i ponownie sprawdź zależności. 

5. Na stronie **między regionami** Sprawdź, czy zasoby są teraz w stanie *gotowości do przygotowania* , bez problemów.

    ![Strona wyświetlająca zasoby w stanie oczekiwanie na przygotowanie](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Przenieś SQL Server

Przypisz SQL Server docelowy w regionie docelowym i zatwierdź przeniesienie.

### <a name="assign-a-target-sql-server"></a>Przypisywanie SQL Server docelowej

1. W **różnych regionach** dla zasobu SQL Server w kolumnie **Konfiguracja docelowa** kliknij pozycję **zasób nie przypisano**.
2. Wybierz istniejący zasób SQL Server w regionie docelowym. 
    
    ![Wpis pokazujący, że stan SQL Server jest ustawiony na zatwierdzanie przenoszenia oczekujących](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> Stan SQL Server źródła zmieni się na *oczekujące przeniesienie*. 

### <a name="commit-the-sql-server-move"></a>Zatwierdź SQL Server Przenieś

1. W obszarze **między regionami** wybierz SQL Server, a następnie kliknij pozycję **Zatwierdź przeniesienie**.
2. W obszarze **Zatwierdź zasoby** kliknij pozycję **Zatwierdź**.

    ![Strona, aby zatwierdzić SQL Server Przenieś](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Śledź postęp przenoszenia na pasku powiadomień.

> [!NOTE]
> Po zatwierdzeniu SQL Server jest teraz w stanie oczekiwania na *usunięcie źródła* .


## <a name="prepare-resources-to-move"></a>Przygotowanie zasobów do przeniesienia

Po przeniesieniu SQL Server źródłowej można przygotować się do przeniesienia innych zasobów.

## <a name="prepare-an-elastic-pool"></a>Przygotowywanie puli elastycznej

1. W **wielu regionach** wybierz źródłową pulę elastyczną (Demonstracja-TEST1-elasticpool w naszym przewodniku), a następnie kliknij pozycję **Przygotuj**.

    ![Przycisk służący do przygotowywania zasobów](./media/tutorial-move-region-sql/prepare-elastic.png)

2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.
3. Gdy powiadomienia pokazują, że proces przygotowania zakończył się pomyślnie, kliknij przycisk **Odśwież**.

> [!NOTE]
> Pula elastyczna jest teraz w stanie *inicjowania przenoszenia* .

## <a name="prepare-a-single-database"></a>Przygotowanie pojedynczej bazy danych

1. W **różnych regionach** wybierz pojedynczą bazę danych (nie w puli elastycznej), a następnie kliknij pozycję **Przygotuj**.

    ![Przycisk przygotowujący wybrane zasoby](./media/tutorial-move-region-sql/prepare-db.png)

2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.
3. Gdy powiadomienia pokazują, że proces przygotowania zakończył się pomyślnie, kliknij przycisk **Odśwież**.

> [!NOTE]
> Baza danych znajduje się teraz w stanie *inicjowania przenoszenia* i została utworzona w regionie docelowym.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Przenoszenie puli i przygotowanie baz danych puli

Aby przygotować bazy danych w puli elastycznej, Pula elastyczna musi znajdować się w stanie *oczekiwania na przeniesienie zatwierdzenia* . Aby przejść do tego stanu, zainicjuj przenoszenie dla puli.

#### <a name="initiate-move---elastic-pool"></a>Inicjowanie puli przenoszenia i elastyczności

1. W **wielu regionach** wybierz źródłową pulę elastyczną (Demonstracja-TEST1-elasticpool w naszym przewodniku), a następnie kliknij pozycję **zainicjuj przenoszenie**.
2. W obszarze **Przenieś zasoby** kliknij pozycję **Inicjuj przenoszenie**.

    
    ![Przycisk inicjowania przenoszenia puli elastycznej](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Śledź postęp przenoszenia na pasku powiadomień.
1. Gdy powiadomienia pokazują, że przenoszenie zakończyło się pomyślnie, kliknij przycisk **Odśwież**.

> [!NOTE]
> Pula elastyczna jest teraz w stanie *oczekiwania na przeniesienie zatwierdzenia* .

#### <a name="prepare-database"></a>Przygotowywanie bazy danych

1. W **różnych regionach** wybierz bazę danych (Demonstracja-TEST2-SQLDB w naszym przewodniku), a następnie kliknij pozycję **Przygotuj**.
2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.

    ![Przycisk służący do przygotowywania bazy danych w puli elastycznej](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Podczas przygotowywania docelowa baza danych jest tworzona w regionie docelowym, a replikacja danych jest uruchamiana. Po przygotowaniu baza danych znajduje się w stanie *oczekiwanie na przeniesienie* . 

![Przycisk przygotowujący wybraną bazę danych w puli elastycznej](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Przenoszenie baz danych

Rozpocznij przenoszenie baz danych.
1. W **różnych regionach** wybierz pozycję zasoby z stanem **Inicjuj przenoszenie oczekujące**. Następnie kliknij pozycję **zainicjuj przenoszenie**.
2. W obszarze **Przenieś zasoby** kliknij pozycję **Inicjuj przenoszenie**.

    ![Strona umożliwiająca zainicjowanie przenoszenia](./media/tutorial-move-region-sql/initiate-move.png)

3. Śledź postęp przenoszenia na pasku powiadomień.

> [!NOTE]
> Bazy danych znajdują się teraz w stanie *oczekiwania na przeniesienie zatwierdzenia* .


## <a name="discard-or-commit"></a>Odrzucić lub zatwierdzić?

Po początkowym przeniesieniu możesz zdecydować, czy chcesz zatwierdzić przeniesienie, czy go odrzucić. 

- **Odrzuć**: możesz chcieć odrzucić przeniesienie, jeśli testujesz, i nie chcesz faktycznie przenosić zasobu źródłowego. Odrzucanie przesunięcia spowoduje zwrócenie zasobu do stanu **inicjacja oczekującego przeniesienia**.
- **Zatwierdzenie**: zatwierdzenie powoduje zakończenie przejścia do regionu docelowego. Po zatwierdzeniu zasób źródłowy będzie w stanie **oczekiwania na usunięcie źródła** i można zdecydować, czy ma zostać usunięty.


## <a name="discard-the-move"></a>Odrzuć przeniesienie 

Możesz odrzucić przeniesienie w następujący sposób:

1. W obszarze **między regionami** wybierz pozycję zasoby z **oczekującym przeniesieniem** stanu, a następnie kliknij pozycję **odrzuć przeniesienie**.
2. W polu **Odrzuć przenoszenie** kliknij pozycję **Odrzuć**.
3. Śledź postęp przenoszenia na pasku powiadomień.


> [!NOTE]
> - Po odrzuceniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".
> - Jeśli istnieje tylko Pula elastyczna, Odrzuć postępy, a pula elastyczna utworzona w regionie docelowym zostanie usunięta.
> - Jeśli istnieje Pula elastyczna ze skojarzonymi bazami danych w stanie *oczekiwania przeniesienia zatwierdzenia* , nie można odrzucić puli elastycznej.
> - Jeśli odrzucisz bazę danych SQL, zasoby regionu docelowego nie zostaną usunięte. 

Jeśli chcesz rozpocząć przechodzenie ponownie po odrzuceniu, wybierz bazę danych SQL lub pulę elastyczną i ponownie zainicjuj operację przenoszenia.


## <a name="commit-the-move"></a>Zatwierdź przeniesienie

Kończąc przeniesienie baz danych i pul elastycznych w następujący sposób:


1. Sprawdź, czy SQL Server jest stanem *oczekiwania na usunięcie źródła* .
2. Zaktualizuj parametry połączenia bazy danych do regionu docelowego przed zatwierdzeniem.
3. W obszarze **między regionami** Wybierz zasoby SQL, a następnie kliknij pozycję **Zatwierdź przeniesienie**.
4. W obszarze **Zatwierdź zasoby** kliknij pozycję **Zatwierdź**.

    ![Zatwierdź przeniesienie](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Śledź postęp zatwierdzania na pasku powiadomień.


> [!NOTE]
> W przypadku baz danych SQL podczas procesu zatwierdzania następuje jakiś Przestój.
> Zatwierdzone bazy danych i pule elastyczne znajdują się teraz w stanie *oczekiwania na usunięcie źródła* .
> Po zatwierdzeniu zaktualizuj ustawienia dotyczące bazy danych, w tym reguły zapory, zasady i alerty, w docelowej bazie danych.


## <a name="delete-source-resources-after-commit"></a>Usuń zasoby źródłowe po zatwierdzeniu

Po przeniesieniu można opcjonalnie usunąć zasoby w regionie źródłowym. 

1. W **różnych regionach** kliknij nazwę każdego zasobu źródłowego, który chcesz usunąć.
2. Na stronie właściwości dla każdego zasobu wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Przenoszenie baz danych Azure SQL Database do innego regionu platformy Azure.
> * Przenoszenie pul elastycznych usługi Azure SQL do innego regionu.

Teraz trwa próba przeniesienia maszyn wirtualnych platformy Azure do innego regionu.

> [!div class="nextstepaction"]
> [Przenoszenie maszyn wirtualnych platformy Azure](./tutorial-move-region-virtual-machines.md)