---
title: Zadania dla lidera zespołu w zespole procesów nauki o danych zespołowych
description: Szczegółowy przewodnik dotyczący zadań dla zespołu, który prowadzi do zespołu procesów nauki o danych zespołowych
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321208"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Zadania dla zespołu liderzy zespołu ds. procesów naukowych

W tym artykule opisano zadania, które *lider zespołu* ukończy dla zespołu ds. analizy danych. Celem lidera zespołu jest ustanowienie środowiska zespołu współpracy, które jest standaryzacją w [zespołowym procesie nauki o danych](overview.md) (przetwarzania TDSP). PRZETWARZANIA TDSP zaprojektowano tak, aby pomóc w ulepszaniu współpracy i uczenia zespołu. 

PRZETWARZANIA TDSP to metoda Agile, iteracyjnej analizy danych, która umożliwia wydajne dostarczanie rozwiązań do analizy predykcyjnej i inteligentnych aplikacji. Proces odwołuje najlepsze rozwiązania i struktury od firmy Microsoft i branży.  Celem jest pomyślne wdrożenie inicjatyw naukowych dotyczących danych i w pełni realizowanie korzyści wynikających z ich programów analitycznych. Aby zapoznać się z zarysem ról pracowników i skojarzonych zadań dla zespołu ds. analizy danych w przetwarzania TDSP, zobacz temat [role i zadania zespołowego procesu nauki danych](roles-tasks.md).

Lider zespołu zarządza zespołem składającym się z kilku analityków danych w jednostce analizy danych przedsiębiorstwa. W zależności od rozmiaru i struktury jednostki analizy danych [Menedżer grupy](group-manager-tasks.md) i lider zespołu mogą należeć do tej samej osoby lub mogą delegować swoje zadania do surogatów. Ale same zadania nie zmieniają się. 

Na poniższym diagramie przedstawiono przepływ pracy dla zadań ukończonych przez lidera zespołu w celu skonfigurowania środowiska zespołu:

![Przepływ pracy zadania lidera zespołu](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Utwórz **projekt zespołowy** w organizacji grupy w usłudze Azure DevOps. 
  
1. Zmień nazwę domyślnego repozytorium zespołu na **TeamUtilities**.
  
1. Utwórz nowe repozytorium **TeamTemplate** w projekcie zespołowym. 
  
1. Zaimportuj zawartość repozytoriów **GroupUtilities** i **GroupProjectTemplate** z grupy do repozytoriów **TeamUtilities** i **TeamTemplate** . 
  
1. Skonfiguruj **kontrolę zabezpieczeń** , dodając członków zespołu i konfigurując ich uprawnienia.
  
1. W razie potrzeby utwórz zasoby danych zespołu i analizy:
   - Dodawanie narzędzi specyficznych dla zespołu do repozytorium **TeamUtilities** . 
   - Tworzenie **usługi Azure File Storage** do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. 
   - Zainstaluj magazyn plików platformy Azure w **Data Science Virtual Machineu** lidera zespołu (DSVM) i Dodaj do niego zasoby danych.

Poniższy samouczek zawiera szczegółowe instrukcje.

> [!NOTE] 
> W tym artykule są używane usługi Azure DevOps i DSVM w celu skonfigurowania środowiska zespołu przetwarzania TDSP, ponieważ jest to sposób implementacji przetwarzania TDSP w firmie Microsoft. Jeśli Twój zespół korzysta z innych platform hostingowych lub deweloperskich, zadania lidera zespołu są takie same, ale sposób ich ukończenia może być różny.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że następujące zasoby i uprawnienia zostały skonfigurowane przez [Menedżera grupy](group-manager-tasks.md):

- **Organizacja** usługi Azure DevOps dla Twojej jednostki danych
- Repozytoria **GroupProjectTemplate** i **GroupUtilities** , uzupełnione o zawartość repozytoriów **ProjectTemplate** i **narzędzi** zespołu firmy Microsoft przetwarzania TDSP
- Uprawnienia na koncie organizacji umożliwiające tworzenie projektów i repozytoriów dla zespołu

Aby można było klonować repozytoria i modyfikować ich zawartość na komputerze lokalnym lub DSVM lub skonfigurować usługę Azure File Storage i zainstalować ją w DSVM, potrzebne są następujące elementy:

- Subskrypcja platformy Azure.
- Na maszynie zainstalowano narzędzie git. Jeśli używasz DSVM, program git jest wstępnie zainstalowany. W przeciwnym razie zapoznaj się z [dodatkiem platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, system Windows lub Linux DSVM utworzony i skonfigurowany na platformie Azure. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z [dokumentacją Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- W przypadku systemu Windows DSVM na komputerze zainstalowano program [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . W pliku *README.MD* przewiń w dół do sekcji **pobieranie i instalacja** , a następnie wybierz **najnowszy Instalator**. Pobierz instalatora *exe* ze strony Instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny skonfigurowany na DSVM i dodany do usługi Azure DevOps. Aby uzyskać więcej informacji i instrukcje, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku platformy i narzędzia](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Tworzenie projektu zespołowego i repozytoriów

W tej sekcji utworzysz następujące zasoby w organizacji usługi Azure DevOps dla Twojej grupy:

- Projekt **Webteam** w usłudze Azure DevOps
- Repozytorium **TeamTemplate**
- Repozytorium **TeamUtilities**

Nazwy określone dla repozytoriów i katalogów w tym samouczku założono, że chcesz ustanowić oddzielny projekt dla własnego zespołu w ramach większej organizacji analizy danych. Jednak cała grupa może zdecydować się na działanie jednego projektu utworzonego przez Menedżera grupy lub administratora organizacji. Następnie wszystkie zespoły analizy danych tworzą repozytoria w ramach tego pojedynczego projektu. Ten scenariusz może być prawidłowy dla:
- Niewielka grupa nauki o danych, która nie ma wielu zespołów do nauki o danych. 
- Większa grupa nauki o danych z wieloma zespołami nauki danych, które jednak chcą zoptymalizować współpracę między zespołami z działaniami, takimi jak planowanie przebiegu na poziomie grupy. 

Jeśli zespoły zdecydują się na posiadanie repozytoriów specyficznych dla zespołu w ramach pojedynczego projektu grupy, liderzy zespołu powinni utworzyć repozytoria z nazwami, takimi jak *\<TeamName> szablon* i *\<TeamName> Narzędzia*. Na przykład: *TeamATemplate* i *TeamAUtilities*. 

W każdym przypadku liderzy zespołu muszą pozwolić, aby członkowie zespołu wiedzieli, które szablony i narzędzia są repozytoriami do konfigurowania i klonowania. Potencjalni klienci projektu powinni postępować zgodnie z [zadaniami lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md) w celu tworzenia repozytoriów projektów, niezależnie od tego, czy są w różnych projektach czy pojedynczym projekcie. 

### <a name="create-the-myteam-project"></a>Utwórz projekt Webteam

Aby utworzyć oddzielny projekt dla zespołu:

1. W przeglądarce internetowej przejdź do strony głównej organizacji usługi Azure DevOps w grupie pod adresem URL *https: \/ / \<server name> / \<organization name>* , a następnie wybierz pozycję **Nowy projekt**. 
   
   ![Wybierz nowy projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. W oknie dialogowym **Tworzenie projektu** wprowadź nazwę zespołu, na przykład *Webteam* , w obszarze **Nazwa projektu** , a następnie wybierz pozycję **Zaawansowane**. 
   
1. W obszarze **Kontrola wersji** wybierz pozycję **git** i w obszarze **proces elementu pracy** wybierz pozycję **Agile**. Następnie wybierz przycisk **Utwórz**. 
   
   ![Tworzenie projektu](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Zostanie otwarta strona **Podsumowanie** projektu zespołowego z adresem URL strony *https \/ / \<server name> / \<organization name> / \<team name> :*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Zmień nazwę repozytorium domyślnego Webteam na TeamUtilities

1. Na stronie **Podsumowanie** projektu **Webteam** w obszarze **jakiej usługi chcesz zacząć od?** wybierz pozycję **repozytoria**. 
   
   ![Wybierz repozytoria](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na stronie repozytorium elementu **Webteam** wybierz repozytorium **Webteam** w górnej części strony, a następnie wybierz pozycję **Zarządzaj repozytoriami** z listy rozwijanej. 
   
   ![Wybierz pozycję Zarządzaj repozytoriami](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na stronie **Ustawienia projektu** wybierz pozycję **...** obok repozytorium **Webteam** , a następnie wybierz pozycję **Zmień nazwę repozytorium**. 
   
   ![Wybierz pozycję Zmień nazwę repozytorium](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. W oknie podręcznym **Zmień nazwę repozytorium elementu Webteam** wprowadź wartość *TeamUtilities* , a następnie wybierz pozycję **Zmień nazwę**. 

### <a name="create-the-teamtemplate-repository"></a>Tworzenie repozytorium TeamTemplate

1. Na stronie **Ustawienia projektu** wybierz pozycję **nowe repozytorium.** 
   
   ![Wybierz nowe repozytorium](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Lub wybierz pozycję **repozytoria** z lewego obszaru nawigacji na stronie **Podsumowanie** projektu **Webteam** , zaznacz repozytorium w górnej części strony, a następnie wybierz pozycję **nowe repozytorium** z listy rozwijanej.
   
1. W oknie dialogowym **Tworzenie nowego repozytorium** upewnij się, że wybrano opcję **git** w obszarze **Typ**. Wprowadź *TeamTemplate* w polu **Nazwa repozytorium** , a następnie wybierz pozycję **Utwórz**.
   
   ![Utwórz repozytorium](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Upewnij się, że na stronie ustawień projektu są widoczne dwa repozytoria **TeamUtilities** i **TeamTemplate** . 
   
   ![Dwa repozytoria zespołu](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Zaimportuj zawartość grup wspólnych repozytoriów

Aby wypełnić repozytoria zespołu zawartością grup wspólnych repozytoriów, które zostały skonfigurowane przez Menedżera grupy:

1. Na stronie głównej projektu **Webteam** wybierz pozycję **repozytoria** w lewym okienku nawigacji. Jeśli zostanie wyświetlony komunikat informujący o tym, że nie znaleziono szablonu **Webteam** , wybierz link w **przeciwnym razie przejdź do domyślnego repozytorium TeamTemplate.** 
   
   Zostanie otwarte domyślne repozytorium **TeamTemplate** . 
   
1. Na stronie **TeamTemplate jest pusta** wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/team-lead-tasks/import-repo.png)
   
1. W oknie dialogowym **Importowanie repozytorium git** wybierz pozycję **git** jako **Typ źródła** , a następnie wprowadź adres URL repozytorium wspólnych szablonów grupy w obszarze **Klonowanie adresu URL**. Adres URL to *https: \/ / \<server name> / \<organization name> /_git/ \<repository name>*. Na przykład: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Wybierz pozycję **Importuj**. Zawartość repozytorium szablonów grup jest zaimportowana do repozytorium szablonów zespołu. 
   
   ![Importuj repozytorium wspólnych szablonów grupy](./media/team-lead-tasks/import-repo-2.png)
   
1. W górnej części strony **repozytoria** projektu rozwiń listę rozwijaną i wybierz repozytorium **TeamUtilities** .
   
1. Powtórz proces importowania, aby zaimportować zawartość repozytorium wspólnych narzędzi grupy, na przykład *GroupUtilities* , do repozytorium **TeamUtilities** . 
   
Każdy z dwóch repozytoriów zespołu zawiera teraz pliki z odpowiedniego repozytorium wspólnych grup. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Dostosuj zawartość repozytoriów zespołu

Jeśli chcesz dostosować zawartość repozytoriów zespołu w celu spełnienia określonych wymagań zespołu, możesz to zrobić teraz. Można zmodyfikować pliki, zmienić strukturę katalogów lub dodać pliki i foldery.

Aby modyfikować, ładować lub tworzyć pliki lub foldery bezpośrednio w usłudze Azure DevOps:

1. Na stronie **Podsumowanie** projektu **Webteam** wybierz pozycję **repozytoria**. 
   
1. W górnej części strony wybierz repozytorium, które chcesz dostosować.

1. W strukturze katalogów repozytorium przejdź do folderu lub pliku, który chcesz zmienić. 
   
   - Aby utworzyć nowe foldery lub pliki, wybierz strzałkę obok pozycji **Nowy**. 
     
     ![Utwórz nowy plik](./media/team-lead-tasks/new-file.png)
     
   - Aby przekazać pliki, wybierz pozycję **Przekaż** pliki. 
     
     ![Przekazywanie plików](./media/team-lead-tasks/upload-files.png)
     
   - Aby edytować istniejące pliki, przejdź do pliku, a następnie wybierz pozycję **Edytuj**. 
     
     ![Edytuj plik](./media/team-lead-tasks/edit-file.png)
     
1. Po dodaniu lub edytowaniu plików wybierz pozycję **Zatwierdź**.
   
   ![Zatwierdź zmiany](./media/team-lead-tasks/commit.png)

Aby można było korzystać z repozytoriów na komputerze lokalnym lub DSVM, należy najpierw skopiować lub *sklonować* repozytoria na maszynę lokalną, a następnie zatwierdzić i wypchnąć zmiany do udostępnionych repozytoriów zespołu, 

Aby sklonować repozytoria:

1. Na stronie **Podsumowanie** projektu elementu **Webteam** wybierz pozycję **repozytoria** i w górnej części strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **klonowanie repozytorium** w obszarze **wiersz polecenia** wybierz opcję **https** dla połączenia HTTP lub protokołu **SSH** dla połączenia SSH, a następnie skopiuj adres URL klonowania do Schowka.
   
   ![Kopiuj sklonowany adres URL](./media/team-lead-tasks/clone.png)
   
1. Na komputerze lokalnym Utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos\MyTeam**
   - W przypadku systemu Linux **$Home/gitrepos/myTeam** 
   
1. Przejdź do utworzonego katalogu.
   
1. W narzędziu git bash Uruchom polecenie `git clone <clone URL>` , gdzie \<clone URL> jest adresem URL skopiowanym z okna dialogowego **klonowania** .
   
   Na przykład użyj jednego z poniższych poleceń, aby sklonować repozytorium **TeamUtilities** do katalogu *Webteam* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Po wprowadzeniu wszelkich zmian w lokalnym klonie repozytorium Zatwierdź i wypchnij zmiany do udostępnionych repozytoriów zespołu. 

Uruchom następujące polecenia narzędzia Git bash z lokalnego katalogu **GitRepos\MyTeam\TeamTemplate** lub **GitRepos\MyTeam\TeamUtilities** .

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Jeśli po raz pierwszy zatwierdzisz repozytorium git, może być konieczne skonfigurowanie parametrów globalnych *User.Name* i *User. email* przed uruchomieniem `git commit` polecenia. Uruchom następujące dwa polecenia:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Jeśli przekazujesz do kilku repozytoriów Git, Użyj tej samej nazwy i adresu e-mail dla wszystkich z nich. Korzystanie z tej samej nazwy i adresu e-mail jest wygodne podczas kompilowania Power BI pulpitów nawigacyjnych w celu śledzenia działań Git w wielu repozytoriach.

## <a name="add-team-members-and-configure-permissions"></a>Dodaj członków zespołu i Skonfiguruj uprawnienia

Aby dodać członków do zespołu:

1. W usłudze Azure DevOps na stronie głównej projektu **Webteam** wybierz pozycję **Ustawienia projektu** na lewym pasku nawigacyjnym. 
   
1. W lewym panelu nawigacyjnym **Ustawienia projektu** wybierz pozycję **zespoły** , a następnie na stronie **zespoły** wybierz **zespół Webteam**. 
   
   ![Konfigurowanie zespołów](./media/team-lead-tasks/teams.png)
   
1. Na stronie **profil zespołu** wybierz pozycję **Dodaj**.
   
   ![Dodaj do zespołu Webteam](./media/team-lead-tasks/add-to-team.png)
   
1. W oknie dialogowym **Dodawanie użytkowników i grup** Wyszukaj i wybierz członków do dodania do grupy, a następnie wybierz pozycję **Zapisz zmiany**. 
   
   ![Dodawanie użytkowników i grup](./media/team-lead-tasks/add-users.png)
   

Aby skonfigurować uprawnienia dla członków zespołu:

1. W lewym panelu nawigacyjnym **Ustawienia projektu** wybierz pozycję **uprawnienia**. 
   
1. Na stronie **uprawnienia** wybierz grupę, do której chcesz dodać członków. 
   
1. Na stronie tej grupy wybierz pozycję **elementy członkowskie** , a następnie wybierz pozycję **Dodaj**. 
   
1. W oknie podręcznym **zapraszanie członków** Wyszukaj i wybierz członków do dodania do grupy, a następnie wybierz pozycję **Zapisz**. 
   
   ![Przyznawanie uprawnień członkom](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Tworzenie danych zespołu i zasobów analitycznych

Ten krok jest opcjonalny, ale udostępnianie danych i zasobów analitycznych dla całego zespołu ma zalety wydajności i kosztów. Członkowie zespołu mogą wykonywać swoje projekty w zasobach udostępnionych, zapisywać w budżetach i wydajniej pracować. Możesz utworzyć magazyn plików platformy Azure i zainstalować go na DSVM, aby udostępnić członkom zespołu. 

Aby uzyskać informacje na temat udostępniania innym zasobom zespołu, na przykład klastrów Azure HDInsight Spark, zobacz [platformy i narzędzia](platforms-and-tools.md). Ten temat zawiera wskazówki dotyczące naukowej perspektywy dotyczącej wyboru zasobów, które są odpowiednie dla Twoich potrzeb, oraz linki do stron produktu i innych odpowiednich i przydatnych samouczków.

>[!NOTE]
> Aby uniknąć przesyłania danych między centrami danych, które mogą być powolne i kosztowne, upewnij się, że grupa zasobów platformy Azure, konto magazynu i DSVM są hostowane w tym samym regionie świadczenia usługi Azure. 

### <a name="create-azure-file-storage"></a>Tworzenie usługi Azure File Storage

1. Uruchom następujący skrypt, aby utworzyć magazyn plików Azure dla zasobów danych, które są przydatne dla całego zespołu. Skrypt poprosi o podanie informacji o subskrypcji platformy Azure, więc jest to gotowe do wprowadzenia. 

   - Na komputerze z systemem Windows uruchom skrypt w wierszu polecenia programu PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Na komputerze z systemem Linux Uruchom skrypt z poziomu powłoki systemu Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Po wyświetleniu monitu zaloguj się do konta Microsoft Azure i wybierz subskrypcję, której chcesz użyć.
   
1. Wybierz konto magazynu, które ma być używane, lub Utwórz nowe w ramach wybranej subskrypcji. W nazwie magazynu plików platformy Azure można używać małych liter, cyfr i łączników.
   
1. Aby ułatwić instalowanie i udostępnianie magazynu, naciśnij klawisz ENTER lub wprowadź *Y* , aby zapisać informacje o usłudze Azure File Storage do pliku tekstowego w bieżącym katalogu. Możesz zaewidencjonować ten plik tekstowy do repozytorium **TeamTemplate** , najlepiej w obszarze **Docs\DataDictionaries** , tak aby wszystkie projekty w zespole mogły uzyskać do niego dostęp. Potrzebne są również informacje o pliku do zainstalowania usługi Azure File Storage na platformie Azure DSVM w następnej sekcji. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Instalowanie usługi Azure File Storage na maszynie lokalnej lub DSVM

1. Aby zainstalować usługę Azure File Storage na komputerze lokalnym lub DSVM, użyj następującego skryptu.
   
   - Na komputerze z systemem Windows uruchom skrypt w wierszu polecenia programu PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Na komputerze z systemem Linux Uruchom skrypt z poziomu powłoki systemu Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Naciśnij *klawisz ENTER lub ENTER, aby kontynuować* , jeśli plik informacji o usłudze Azure File Storage został zapisany w poprzednim kroku. Wprowadź pełną ścieżkę i nazwę utworzonego pliku. 
   
   Jeśli nie masz pliku z informacjami o usłudze Azure File Storage, wprowadź *n* i postępuj zgodnie z instrukcjami, aby wprowadzić swoją subskrypcję, konto usługi Azure Storage i informacje o usłudze Azure File Storage.
   
1. Wprowadź nazwę dysku lokalnego lub przetwarzania TDSP, na którym ma zostać zainstalowany udział plików. Na ekranie zostanie wyświetlona lista istniejących nazw dysków. Podaj nazwę dysku, która jeszcze nie istnieje.
   
1. Upewnij się, że nowy dysk i magazyn zostały pomyślnie zainstalowane na komputerze.

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauka danych zespołu:

- [Zadania Menedżera grupy dla zespołu ds. analizy danych](group-manager-tasks.md)
- [Zadania lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md)
- [Projekt poszczególnych zadań współautora dla zespołu ds. analizy danych](project-ic-tasks.md)