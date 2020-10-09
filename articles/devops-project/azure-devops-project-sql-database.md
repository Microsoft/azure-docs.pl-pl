---
title: 'Samouczek: wdrażanie aplikacji ASP.NET i kodu Azure SQL Database za pomocą usługi Azure DevOps Starter'
description: DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Dzięki DevOps Starter możesz wdrożyć aplikację ASP.NET i kod Azure SQL Database w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 6a1af644bbd88af5c513ed9a43ce154f285c06df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856011"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Samouczek: wdrażanie aplikacji ASP.NET i kodu Azure SQL Database za pomocą usługi Azure DevOps Starter

Usługa Azure DevOps Starter przedstawia uproszczone środowisko, w którym można przenieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację w celu utworzenia potoku ciągłej integracji i ciągłego dostarczania na platformę Azure. 

DevOps Starter również:
* Program automatycznie tworzy zasoby platformy Azure, takie jak baza danych w Azure SQL Database.
* Tworzy i konfiguruje potok wydania w usłudze Azure Pipelines, który obejmuje potok kompilacji na potrzeby ciągłej integracji.
* Konfiguruje potok wydania na potrzeby ciągłego wdrażania. 
* Tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Użyj platformy Azure DevOps Starter, aby wdrożyć aplikację ASP.NET i kod Azure SQL Database
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Łączenie z bazą danych Azure SQL Database 
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>Utwórz projekt w DevOps Projects dla aplikacji ASP.NET i Azure SQL Database

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure, takie jak Azure SQL Database, w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz pozycję **ASP.NET**.

1. Wybierz pozycję **Dodaj bazę danych**, a następnie wybierz pozycję **Dalej**. Wybrana w poprzednim kroku struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 
    
1. Wybierz opcję **Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

1. Wprowadź nazwę projektu usługi Azure DevOps. 

1. Wybierz usługi subskrypcji platformy Azure. Możesz również wybrać link **Zmień**, aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure, a następnie określić nazwę użytkownika w sekcji **Szczegóły logowania na serwerze bazy danych**. Zachowaj nazwę użytkownika, aby użyć jej w dalszych krokach tego samouczka. Jeśli możesz wykonać ten krok opcjonalny, zamknij obszar konfiguracji platformy Azure przed wybraniem pozycji **Gotowe**.
 
1. Wybierz pozycję **Gotowe**. Po kilku minutach proces zostanie ukończony i zostanie otwarty pulpit nawigacyjny DevOps Starter w Azure Portal. Możesz również przejść do pulpitu nawigacyjnego bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal. Po prawej stronie wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
    
## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

DevOps Starter automatycznie konfiguruje pełny potok ciągłej integracji/ciągłego wdrażania w Azure Repos. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z potokami kompilacji usługi Azure DevOps, wykonaj następujące czynności:

1. W górnej części pulpitu nawigacyjnego DevOps początkowy wybierz pozycję **potoki kompilacji**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **stan** , a następnie wybierz przycisk wielokropka (...). Menu wyświetla kilka opcji, takich jak kolejkowanie nowej kompilacji, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Kliknij pozycję **Edytuj**.

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz CI i każde zatwierdzenie do repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

DevOps Starter automatycznie tworzy i konfiguruje kroki niezbędne do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. Automatyzacja tworzy również potok ciągłego wdrażania, a on zapewnia ciągłe wdrażanie na maszynie wirtualnej platformy Azure. Aby dowiedzieć się więcej o potoku ciągłego wdrażania usługi Azure DevOps, wykonaj następujące czynności:

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**. DevOps Starter tworzy potok wydania do zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**. Potok wydania zawiera *potok*, który definiuje proces tworzenia wydania.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

    DevOps Starter konfiguruje losowe hasło SQL i używa go dla potoku wydania.
    
1. Po lewej stronie wybierz pozycję **Zmienne**. 

   > [!NOTE]
   > Ten krok należy wykonać tylko, jeśli hasło programu SQL Server zostało zmienione. Istnieje jedna zmienna hasła.
  
1. Obok pola **Wartość** wybierz ikonę kłódki, wprowadź nowe hasło, a następnie wybierz pozycję **Zapisz**.

1. Po lewej stronie wybierz pozycję **Zadania**, a następnie wybierz środowisko. Zadania to czynności wykonywane w procesie wdrażania, pogrupowane w fazy. Ten potok wydania ma jedną fazę, która zawiera zadania *wdrożenia usługi Azure App Service* i *wdrożenia usługi Azure SQL Database*.

1. Wybierz zadanie *wykonania usługi Azure SQL* i sprawdź różne właściwości użyte podczas wdrożenia bazy danych SQL. W obszarze **Pakiet wdrożeniowy** w zadaniu używany jest plik *SQL DACPAC*.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**. Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure 

 > [!NOTE]
 > Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu. Aby sprawdzić proces wdrażania usługi SQL, możesz również wprowadzić zmianę w schemacie programu SQL Server w tabeli.

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Zmiany kodu wywołują rozpoczęcie procesu ciągłej integracji/ciągłego wdrażania i automatyczne wdrożenie zmian na platformie Azure.

1. W lewym okienku wybierz pozycję **Kod**, a następnie przejdź do repozytorium.

1. Przejdź do katalogu *SampleWebApplication\Views\Home*, a następnie wybierz symbol wielokropka (...) obok pliku *Index.cshtml* i wybierz polecenie **Edytuj**. 

1. Wprowadź zmianę w pliku, na przykład dodaj tekst w jednym z tagów div. 

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę. Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym DevOps Starter lub w przeglądarce za pomocą swojej organizacji usługi Azure DevOps.

1. Po zakończeniu tworzenia wydania odśwież aplikację, aby zweryfikować zmiany.

## <a name="connect-to-azure-sql-database"></a>Łączenie z bazą danych Azure SQL Database

Musisz mieć odpowiednie uprawnienia, aby nawiązać połączenie z Azure SQL Database.

1. Na pulpicie nawigacyjnym DevOps Starter wybierz pozycję **SQL Database** , aby przejść do strony zarządzania dla SQL Database.
   
1. Wybierz polecenie **Ustaw zaporę serwera**, a następnie wybierz pozycję **Dodaj adres IP klienta**. 

1. Wybierz pozycję **Zapisz**. Adres IP klienta ma teraz dostęp do zasobów usługi Azure SQL Server.

1. Wróć do okienka **Baza danych SQL**. 

1. Po prawej stronie wybierz nazwę serwera, aby przejść do strony konfiguracji usługi **SQL Server**.

1. Wybierz pozycję **zresetuj hasło**, wprowadź hasło dla logowania administratora SQL Server, a następnie wybierz pozycję **Zapisz**. Zachowaj to hasło do użycia w dalszej części tego samouczka.

    Opcjonalnie możesz użyć narzędzi klienckich, takich jak SQL Server Management Studio lub Visual Studio, aby połączyć się z SQL Server i Azure SQL Database. Użyj właściwości **Nazwa serwera**, aby nawiązać połączenie.

    Jeśli nie zmieniono nazwy użytkownika bazy danych podczas początkowego konfigurowania projektu w usłudze DevOps Projects, nazwa użytkownika to lokalna część adresu e-mail. Na przykład jeśli adres e-mail to *jankowalski \@ Microsoft.com*, nazwa użytkownika to *jankowalski*.

   > [!NOTE]
   > Jeśli zmienisz hasło dla logowania SQL, musisz zmienić hasło w zmiennej potoku wydania, zgodnie z opisem w sekcji [Sprawdzanie potoku dysku CD](#examine-the-cd-pipeline) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeprowadzasz testowanie, możesz uniknąć naliczania opłat, oczyszczając zasoby. Gdy nie są już potrzebne, możesz usunąć Azure SQL Database i powiązane zasoby, które zostały utworzone w tym samouczku. Aby to zrobić, użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

> [!IMPORTANT]
> Poniższa procedura powoduje trwałe usunięcie zasobów. Funkcja *usuwania* niszczy dane, które są tworzone przez projekt w DevOps Starter w systemie Azure i Azure DevOps, i nie będzie można go pobrać. Użyj tej procedury dopiero po uważnym przeczytaniu monitów.

1. W Azure Portal przejdź do pulpitu nawigacyjnego DevOps Starter.
2. W prawym górnym rogu wybierz pozycję **Usuń**. 
3. Po wyświetleniu monitu wybierz pozycję **Tak**, aby *trwale usunąć* zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj platformy Azure DevOps Starter, aby wdrożyć aplikację ASP.NET i kod Azure SQL Database
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Łączenie z bazą danych Azure SQL Database 
> * Czyszczenie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Filmy wideo

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]