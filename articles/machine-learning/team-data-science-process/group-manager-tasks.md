---
title: Zadania kierownika ds. przetwarzania danych zespołowych
description: Postępuj zgodnie z szczegółowym opisem zadań wykonywanych przez Menedżera grupy w projekcie zespołu analizy danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740230"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Zadania kierownika ds. przetwarzania danych zespołowych

W tym artykule opisano zadania, które *Menedżer grupy* ukończy dla organizacji analizy danych. Menedżer grupy zarządza całą jednostką analizy danych w przedsiębiorstwie. Jednostka do nauki o danych może mieć kilka zespołów, z których każdy pracuje w wielu projektach naukowych w różnych firmach. Celem Menedżera grupy jest ustanowienie środowiska grupy współpracy, które jest standaryzacją w [zespołowym procesie nauki o danych](overview.md) (przetwarzania TDSP). Aby zapoznać się z zarysem wszystkich ról pracowników i skojarzonych zadań obsłużonych przez zespół ds. analizy danych na przetwarzania TDSP, zobacz temat [role i zadania zespołowego procesu nauka danych zespołu](roles-tasks.md).

Na poniższym diagramie przedstawiono sześć głównych zadań instalacyjnych programu Group Manager. Menedżerowie grup mogą delegować swoje zadania do surogatów, ale zadania skojarzone z rolą nie zmieniają się.

![Zadania Menedżera grupy](./media/group-manager-tasks/tdsp-group-manager.png)

1. Skonfiguruj **organizację usługi Azure DevOps** dla grupy.
2. Utwórz domyślny **projekt GroupCommon** w organizacji usługi Azure DevOps.
3. Utwórz repozytorium **GroupProjectTemplate** w Azure Repos.
4. Utwórz repozytorium **GroupUtilities** w Azure Repos.
5. Zaimportuj zawartość repozytoriów przetwarzania TDSP i **narzędzi** **zespołu firmy Microsoft** do wspólnych repozytoriów grup.
6. Skonfiguruj **członkostwo** i **uprawnienia** dla członków zespołu, aby uzyskać dostęp do grupy.

Poniższy samouczek zawiera szczegółowe instrukcje. 

> [!NOTE] 
> W tym artykule za pomocą usługi Azure DevOps można skonfigurować środowisko grupy przetwarzania TDSP, ponieważ jest to sposób implementacji przetwarzania TDSP w firmie Microsoft. Jeśli Twoja Grupa korzysta z innych platform hostingowych lub deweloperskich, zadania Menedżera grupy są takie same, ale sposób ich wykonania może być różny.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Tworzenie organizacji i projektu na platformie Azure DevOps

1. Przejdź do [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), wybierz pozycję **Zaloguj się** w prawym górnym rogu i zaloguj się do konto Microsoft. 
   
   ![Zaloguj się do konto Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Jeśli nie masz konto Microsoft, wybierz pozycję **zarejestruj się teraz**, Utwórz konto Microsoft i zaloguj się przy użyciu tego konta. Jeśli Twoja organizacja ma subskrypcję programu Visual Studio, zaloguj się przy użyciu poświadczeń dla tej subskrypcji.
   
1. Po zalogowaniu się w prawym górnym rogu strony Azure DevOps wybierz pozycję **Utwórz nową organizację**.
   
   ![Utwórz nową organizację](./media/group-manager-tasks/create-organization.png)
   
1. Jeśli zostanie wyświetlony monit o zgodę na warunki użytkowania usługi, zasady zachowania poufności informacji i Kodeks postępowania, wybierz pozycję **Kontynuuj**.
   
1. W oknie dialogowym rejestracji nazwij swoją organizację Azure DevOps, zaakceptuj przypisanie regionu hosta lub listę rozwijaną i wybierz inny region. Następnie wybierz pozycję **Kontynuuj**. 

1. W obszarze **Utwórz projekt, aby** rozpocząć, wprowadź *GroupCommon*, a następnie wybierz pozycję **Utwórz projekt**. 
   
   ![Tworzenie projektu](./media/group-manager-tasks/create-project.png)

Zostanie otwarta strona **Podsumowanie** projektu **GroupCommon** . Adres URL strony to *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Strona podsumowania projektu](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Konfigurowanie wspólnych repozytoriów grupy

Azure Repos hostuje następujące typy repozytoriów dla grupy:

- **Najpopularniejsze** repozytoria grup: repozytoria ogólnego przeznaczenia, które wiele zespołów w jednostce analizy danych może przyjąć dla wielu projektów analizy danych. 
- **Repozytoria zespołu**: repozytoria dla określonych zespołów w jednostce analizy danych. Te repozytoria są specyficzne dla potrzeb zespołu i mogą być używane dla wielu projektów w ramach tego zespołu, ale nie są wystarczająco ogólne, aby można było ich używać w wielu zespołach w ramach jednostki analizy danych.
- **Repozytoria projektu**: repozytoria dla określonych projektów. Takie repozytoria mogą nie być generalnie wystarczające dla wielu projektów w zespole lub dla innych zespołów w jednostce analizy danych.

W celu skonfigurowania wspólnych repozytoriów grup w projekcie: 
- Zmień nazwę domyślnego repozytorium **GroupCommon** na **GroupProjectTemplate**
- Utwórz nowe repozytorium **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Zmień nazwę domyślnego repozytorium projektu na GroupProjectTemplate

Aby zmienić nazwę domyślnego repozytorium projektu **GroupCommon** na **GroupProjectTemplate**:

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **repozytoria**. Ta akcja spowoduje przejście do domyślnego repozytorium **GroupCommon** projektu GroupCommon, który jest obecnie pusty.
   
1. W górnej części strony rozwiń strzałkę obok pozycji **GroupCommon** i wybierz pozycję **Zarządzaj repozytoriami**.
   
   ![Zarządzanie repozytoriami](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na stronie **Ustawienia projektu** wybierz pozycję **...** obok pozycji **GroupCommon**, a następnie wybierz pozycję **Zmień nazwę repozytorium**. 
   
   ![Wybierz... a następnie wybierz pozycję Zmień nazwę repozytorium](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. W oknie podręcznym **Zmień nazwę repozytorium GroupCommon** wprowadź *GroupProjectTemplate*, a następnie wybierz pozycję **Zmień nazwę**. 
   
   ![Zmień nazwę repozytorium](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Tworzenie repozytorium GroupUtilities

Aby utworzyć repozytorium **GroupUtilities** :

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **repozytoria**. 
   
1. W górnej części strony rozwiń strzałkę obok pozycji **GroupProjectTemplate** i wybierz pozycję **nowe repozytorium**.
   
   ![Wybierz nowe repozytorium](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. W oknie dialogowym **Tworzenie nowego repozytorium** wybierz pozycję **git** jako **Typ**, wprowadź *GroupUtilities* jako **nazwę repozytorium**, a następnie wybierz pozycję **Utwórz**.
   
   ![Utwórz repozytorium GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na stronie **Ustawienia projektu** wybierz pozycję **repozytoria** w obszarze **repozytoria** w okienku nawigacji po lewej stronie, aby wyświetlić dwie repozytoria grup: **GroupProjectTemplate** i **GroupUtilities**.
   
   ![Dwie repozytoria grup](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importuj repozytoria zespołu programu Microsoft przetwarzania TDSPe

W tej części samouczka zaimportowano zawartość repozytoriów **ProjectTemplate** i **narzędzi** zarządzanych przez zespół przetwarzania TDSP firmy Microsoft do swoich repozytoriów **GroupProjectTemplate** i **GroupUtilities** . 

Aby zaimportować repozytoria zespołu przetwarzania TDSP:

1. Na stronie głównej projektu **GroupCommon** wybierz pozycję **repozytoria** w lewym okienku nawigacji. Zostanie otwarte domyślne repozytorium **GroupProjectTemplate** . 
   
1. Na stronie **GroupProjectTemplate jest pusta** wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/group-manager-tasks/import-repo.png)
   
1. W oknie dialogowym **Importowanie repozytorium git** wybierz pozycję **git** jako **Typ źródła**, a następnie wprowadź wartość *https: \/ /GitHub.com/Azure/Azure-TDSP-ProjectTemplate.git* dla **adresu URL klonowania**. Następnie wybierz pozycję **Importuj**. Zawartość repozytorium Microsoft przetwarzania TDSP Team ProjectTemplate jest zaimportowana do repozytorium GroupProjectTemplate. 
   
   ![Importuj repozytorium zespołu przetwarzania TDSP firmy Microsoft](./media/group-manager-tasks/import-repo-2.png)
   
1. W górnej części strony **repozytoria** wybierz pozycję repozytorium **GroupUtilities** .
   
Każdy z dwóch repozytoriów grup zawiera teraz wszystkie pliki, z wyjątkiem tych znajdujących się w katalogu *. git* , z odpowiedniego repozytorium programu Microsoft przetwarzania tdspe. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Dostosowywanie zawartości repozytoriów grup

Jeśli chcesz dostosować zawartość repozytoriów grup, aby spełniały określone potrzeby danej grupy, możesz to zrobić teraz. Można zmodyfikować pliki, zmienić strukturę katalogów lub dodać pliki, które zostały opracowane przez grupę lub które są przydatne dla grupy.

### <a name="make-changes-in-azure-repos"></a>Wprowadzanie zmian w Azure Repos

Aby dostosować zawartość repozytorium:

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **repozytoria**. 
   
1. W górnej części strony wybierz repozytorium, które chcesz dostosować.

1. W strukturze katalogów repozytorium przejdź do folderu lub pliku, który chcesz zmienić. 
   
   - Aby utworzyć nowe foldery lub pliki, wybierz strzałkę obok pozycji **Nowy**. 
     
     ![Utwórz nowy plik](./media/group-manager-tasks/new-file.png)
     
   - Aby przekazać pliki, wybierz pozycję **Przekaż** pliki. 
     
     ![Przekazywanie plików](./media/group-manager-tasks/upload-files.png)
     
   - Aby edytować istniejące pliki, przejdź do pliku, a następnie wybierz pozycję **Edytuj**. 
     
     ![Edytuj plik](./media/group-manager-tasks/edit-file.png)
     
1. Po dodaniu lub edytowaniu plików wybierz pozycję **Zatwierdź**.
   
   ![Zatwierdź zmiany](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Wprowadzanie zmian przy użyciu komputera lokalnego lub DSVM

Jeśli chcesz wprowadzić zmiany przy użyciu komputera lokalnego lub DSVM i wypchnąć zmiany do repozytoriów grup, upewnij się, że spełniono wymagania wstępne dotyczące pracy z usługami git i DSVMs:

- Subskrypcja platformy Azure, jeśli chcesz utworzyć DSVM.
- Na maszynie zainstalowano narzędzie git. Jeśli używasz DSVM, program git jest wstępnie zainstalowany. W przeciwnym razie zapoznaj się z [dodatkiem platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, system Windows lub Linux DSVM utworzony i skonfigurowany na platformie Azure. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z [dokumentacją Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- W przypadku systemu Windows DSVM na komputerze zainstalowano program [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . W pliku *README.MD* przewiń w dół do sekcji **pobieranie i instalacja** , a następnie wybierz **najnowszy Instalator**. Pobierz instalatora *exe* ze strony Instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny skonfigurowany na DSVM i dodany do usługi Azure DevOps. Aby uzyskać więcej informacji i instrukcje, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku platformy i narzędzia](platforms-and-tools.md#appendix). 

Najpierw skopiuj lub *Sklonuj* repozytorium na komputerze lokalnym. 
   
1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **repozytoria** i w górnej części strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **klonowanie repozytorium** wybierz opcję **https** dla połączenia HTTP lub **SSH** dla połączenia SSH, a następnie skopiuj adres URL klonowania w **wierszu polecenia** do Schowka.
   
   ![Klonowanie repozytorium](./media/group-manager-tasks/clone.png)
   
1. Na komputerze lokalnym Utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos\GroupCommon**
   - W przypadku systemu Linux, **$/GitRepos/GroupCommon** w katalogu macierzystym 
   
1. Przejdź do utworzonego katalogu.
   
1. W narzędziu git bash Uruchom polecenie `git clone <clone URL>.`
   
   Na przykład jedno z następujących poleceń klonuje repozytorium **GroupUtilities** do katalogu *GroupCommon* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Po wprowadzeniu wszelkich zmian w lokalnym klonie repozytorium można wypchnąć zmiany do wspólnych repozytoriów grupy udostępnionej. 

Uruchom następujące polecenia narzędzia Git bash z lokalnego katalogu **GroupProjectTemplate** lub **GroupUtilities** .

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Jeśli po raz pierwszy zatwierdzisz repozytorium git, przed uruchomieniem polecenia może być konieczne skonfigurowanie parametrów globalnych *User.Name* i *User.email* `git commit` . Uruchom następujące dwa polecenia:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Jeśli przekazujesz do kilku repozytoriów Git, Użyj tej samej nazwy i adresu e-mail dla wszystkich z nich. Korzystanie z tej samej nazwy i adresu e-mail jest wygodne podczas kompilowania Power BI pulpitów nawigacyjnych w celu śledzenia działań Git w wielu repozytoriach.

## <a name="add-group-members-and-configure-permissions"></a>Dodaj członków grupy i Skonfiguruj uprawnienia

Aby dodać członków do grupy:

1. W usłudze Azure DevOps na stronie głównej projektu **GroupCommon** wybierz pozycję **Ustawienia projektu** na lewym pasku nawigacyjnym. 
   
1. W lewym panelu nawigacyjnym **Ustawienia projektu** wybierz pozycję **zespoły**, a następnie na stronie **zespoły** wybierz **zespół GroupCommon**. 
   
   ![Konfigurowanie zespołów](./media/group-manager-tasks/teams.png)
   
1. Na stronie **profil zespołu** wybierz pozycję **Dodaj**.
   
   ![Dodaj do zespołu GroupCommon](./media/group-manager-tasks/add-to-team.png)
   
1. W oknie dialogowym **Dodawanie użytkowników i grup** Wyszukaj i wybierz członków do dodania do grupy, a następnie wybierz pozycję **Zapisz zmiany**. 
   
   ![Dodawanie użytkowników i grup](./media/group-manager-tasks/add-users.png)
   

Aby skonfigurować uprawnienia dla członków:

1. W lewym panelu nawigacyjnym **Ustawienia projektu** wybierz pozycję **uprawnienia**. 
   
1. Na stronie **uprawnienia** wybierz grupę, do której chcesz dodać członków. 
   
1. Na stronie tej grupy wybierz pozycję **elementy członkowskie**, a następnie wybierz pozycję **Dodaj**. 
   
1. W oknie podręcznym **zapraszanie członków** Wyszukaj i wybierz członków do dodania do grupy, a następnie wybierz pozycję **Zapisz**. 
   
   ![Przyznawanie uprawnień członkom](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do szczegółowych opisów innych ról i zadań w procesie nauki danych zespołu:

- [Zadania lidera zespołu dla zespołu ds. analizy danych](team-lead-tasks.md)
- [Zadania lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md)
- [Projekt poszczególnych zadań współautora dla zespołu ds. analizy danych](project-ic-tasks.md)
