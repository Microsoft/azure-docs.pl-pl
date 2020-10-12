---
title: Ponowne wdrażanie pakietów usług SSIS w pojedynczej bazie danych SQL
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować i ponownie wdrażać SQL Server Integration Services pakiety i projekty, aby Azure SQL Database pojedynczą bazę danych przy użyciu Azure Database Migration Service i Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e5f9ba7ea4afd81d62cba7b970693f603b53ef9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316090"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Wdróż ponownie pakiety usług SSIS w Azure SQL Database z Azure Database Migration Service

Jeśli używasz SQL Server Integration Services (SSIS) i chcesz migrować projekty SSIS/pakiety z SSISDB źródłowego hostowanego przez SQL Server do SSISDB docelowego hostowanego przez Azure SQL Database, można je wdrożyć ponownie przy użyciu Kreatora wdrażania usług Integration Services. Kreatora można uruchomić z poziomu programu SQL Server Management Studio (SSMS).

Jeśli używana wersja usług SSIS jest wcześniejsza niż 2012, przed ponownym wdrożeniem projektów i pakietów SSIS w modelu wdrażania projektu, najpierw należy je przekonwertować przy użyciu Kreatora konwersji projektu usług Integration Services, który można także uruchomić z programu SSMS. Aby uzyskać więcej informacji, zobacz artykuł [konwertowanie projektów na model wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) obecnie nie obsługuje migracji źródłowej SSISDB do Azure SQL Database, ale można ponownie wdrożyć projekty i pakiety SSIS w ramach następującego procesu.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródłowe projekty SSIS/pakiety.
> * Migruj projekty SSIS/pakiety na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* SSMS w wersji 17,2 lub nowszej.
* Wystąpienie docelowego serwera bazy danych do hostowania SSISDB. Jeśli jeszcze tego nie masz, Utwórz [logiczny serwer SQL](../azure-sql/database/logical-servers.md) (bez bazy danych) przy użyciu Azure Portal, przechodząc do [formularza](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (tylko serwer logiczny).
* Program SSIS musi być zainicjowany w Azure Data Factory (ADF) zawierający Azure-SSIS Integration Runtime (IR) z miejscem docelowym SSISDB hostowanym przez SQL Database (zgodnie z opisem w artykule [obsługa Azure-SSIS Integration Runtime w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Oceń źródłowe projekty SSIS/pakiety

Ocena źródłowej SSISDB nie jest jeszcze zintegrowana z bazą danych Asystent migracji (DMA) ani Azure Database Migration Service (DMS), projekty i pakiety usług SSIS zostaną ocenione/zweryfikowane w miarę ich ponownego wdrożenia w miejscu docelowym SSISDB hostowanym przez Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>Migrowanie projektów/pakietów SSIS

Aby przeprowadzić migrację projektów i pakietów SSIS do Azure SQL Database, wykonaj następujące czynności.

1. Otwórz program SSMS, a następnie wybierz **Opcje** , aby wyświetlić okno dialogowe **łączenie z serwerem** .

2. Na karcie **Logowanie** Określ informacje niezbędne do nawiązania połączenia z serwerem, który będzie obsługiwał SSISDB docelowy.

    ![Karta logowania SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na karcie **Właściwości połączenia** w polu tekstowym **Połącz z bazą danych** wybierz lub wpisz **SSISDB**, a następnie wybierz pozycję **Połącz**.

    ![Karta właściwości połączenia SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. W Eksplorator obiektów SSMS rozwiń węzeł **katalogi usług integracji** , rozwiń węzeł **SSISDB**i jeśli nie ma żadnych istniejących folderów, kliknij prawym przyciskiem myszy pozycję **SSISDB** i Utwórz nowy folder.

5. W obszarze **SSISDB**Rozwiń wszystkie foldery, kliknij prawym przyciskiem myszy pozycję **projekty**, a następnie wybierz polecenie **Wdróż projekt**.

    ![Rozwinięto węzeł SSIS SSISDB](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. W Kreatorze wdrażania usług Integration Services na stronie **wprowadzenie** Przejrzyj informacje, a następnie wybierz przycisk **dalej**.

    ![Strona wprowadzenia kreatora wdrażania](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na stronie **Wybierz źródło** określ istniejący projekt SSIS, który ma zostać wdrożony.

    Jeśli program SSMS jest również połączony z SQL Server hostem źródła, wybierz pozycję **Katalog usług Integration Services**, a następnie wprowadź nazwę serwera i ścieżkę projektu w katalogu, aby wdrożyć projekt bezpośrednio.

    Alternatywnie wybierz pozycję **plik wdrożenia projektu**, a następnie określ ścieżkę do istniejącego pliku wdrożenia projektu (. ispac), aby wdrożyć projekt.

    ![Wybieranie strony źródłowej przez Kreatora wdrażania](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Wybierz opcję **Dalej**.
9. Na stronie **Wybierz miejsce docelowe** określ miejsce docelowe dla projektu.

    a. W polu tekstowym Nazwa serwera wprowadź w pełni kwalifikowaną nazwę serwera (<server_name>. database.windows.net).

    b. Podaj informacje o uwierzytelnianiu, a następnie wybierz pozycję **Połącz**.

    ![Kreator wdrażania — Wybieranie strony docelowej](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Wybierz pozycję **Przeglądaj** , aby określić folder docelowy w SSISDB, a następnie wybierz przycisk **dalej**.

    > [!NOTE]
    > Przycisk **dalej** jest włączony dopiero po wybraniu pozycji **Połącz**.

10. Na stronie **Walidacja Sprawdź** wszystkie błędy/ostrzeżenia, a następnie w razie potrzeby zmodyfikuj odpowiednie pakiety.

    ![Strona weryfikacji kreatora wdrażania](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Wybierz opcję **Dalej**.

12. Na stronie **Przegląd** przejrzyj ustawienia wdrożenia.

    > [!NOTE]
    > Ustawienia można zmienić, wybierając pozycję **poprzednie** lub wybierając dowolne z kroków w okienku po lewej stronie.

13. Wybierz pozycję **Wdróż** , aby rozpocząć proces wdrażania.

14. Po zakończeniu procesu wdrażania można wyświetlić stronę wyniki, która wyświetla powodzenie lub niepowodzenie każdej akcji wdrożenia.
    a. Jeśli jakakolwiek akcja zakończyła się niepowodzeniem, w kolumnie **wynik** wybierz pozycję **nie można** wyświetlić wyjaśnienia błędu.
    b. Opcjonalnie wybierz pozycję **Zapisz raport** , aby zapisać wyniki w pliku XML.

15. Wybierz pozycję **Zamknij** , aby zakończyć działanie Kreatora wdrażania usług Integration Services.

Jeśli wdrożenie projektu zakończy się niepowodzeniem, możesz wybrać wszystkie pakiety, które zawiera, aby uruchomić je na Azure-SSIS IR.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.
