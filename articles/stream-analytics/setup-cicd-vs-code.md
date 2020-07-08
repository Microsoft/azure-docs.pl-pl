---
title: Wdróż zadanie Azure Stream Analytics przy użyciu pakietu CI/CD npm
description: W tym artykule opisano, jak Azure Stream Analytics za pomocą pakietu npm/CD pozostała do konfigurowania ciągłej integracji i wdrażania.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7a7fe3f7e1c39837106471d118a8b1bb770a524e
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045828"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Wdróż zadanie Azure Stream Analytics przy użyciu pakietu CI/CD npm 

Aby skonfigurować ciągłą integrację i proces wdrażania dla zadań Stream Analytics Azure Stream Analytics, można użyć pakietu npm/CD. W tym artykule opisano, jak używać pakietu npm ogólnie z dowolnym systemem ciągłej integracji/ciągłego wdrażania, a także konkretnych instrukcji dotyczących wdrożenia z Azure Pipelines.

Aby uzyskać więcej informacji na temat wdrażania przy użyciu programu PowerShell, zobacz [Deploy with a Menedżer zasobów plik szablonu i Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Możesz również dowiedzieć się więcej na temat [używania obiektu jako parametru w szablonie Menedżer zasobów](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Kompiluj projekt VS Code

Możesz włączyć ciągłą integrację i wdrażanie dla Azure Stream Analytics zadań przy użyciu pakietu **ASA-streamanalytics-cicd** npm. Pakiet npm udostępnia narzędzia do generowania szablonów Azure Resource Manager [Stream Analytics projektów Visual Studio Code](quick-create-vs-code.md). Można go używać w systemach Windows, macOS i Linux bez instalowania Visual Studio Code.

Możesz [pobrać pakiet](https://www.npmjs.com/package/azure-streamanalytics-cicd) bezpośrednio lub zainstalować go [globalnie](https://docs.npmjs.com/downloading-and-installing-packages-globally) za pomocą `npm install -g azure-streamanalytics-cicd` polecenia. Jest to zalecane podejście, które może być również używane w ramach zadania skryptu interfejsu wiersza polecenia platformy Azure w ramach potoku kompilacji w **Azure Pipelines**.

Po zainstalowaniu pakietu Użyj następującego polecenia, aby uzyskać dane wyjściowe szablonów Azure Resource Manager. Argument **scriptPath** jest ścieżką bezwzględną do pliku **asaql** w projekcie. Upewnij się, że asaproj.jsi JobConfig.jsdla plików znajdują się w tym samym folderze, w którym znajduje się plik skryptu. Jeśli **outputPath** nie zostanie określony, szablony zostaną umieszczone w folderze **Deploy** w folderze **bin** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Przykład (na macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

W przypadku pomyślnego skompilowania Stream Analytics Visual Studio Code projektu generowane są następujące dwa pliki szablonów Azure Resource Manager w folderze **bin/[Debug/retails]/Deploy** : 

* Plik szablonu Menedżer zasobów

   ```
   [ProjectName].JobTemplate.json 
   ```

* Plik parametrów Menedżer zasobów

   ```
   [ProjectName].JobTemplate.parameters.json
   ```   

Parametry domyślne w parameters.jsna pliku pochodzą z ustawień w projekcie Visual Studio Code. Jeśli chcesz wdrożyć program w innym środowisku, Zastąp odpowiednio parametry.

> [!NOTE]
> Dla wszystkich poświadczeń wartości domyślne są ustawiane na wartość null. Musisz ustawić **required** wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Wdrażanie za pomocą usługi Azure Pipelines

W tej sekcji szczegółowo opisano, jak utworzyć Azure Pipelines [kompilacje](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) i potoki [wydań](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) przy użyciu npm.

Otwórz przeglądarkę internetową i przejdź do projektu Visual Studio Code Azure Stream Analytics.

1. W obszarze **potoki** w menu nawigacji po lewej stronie wybierz pozycję **kompilacje**. Następnie wybierz pozycję **Nowy potok**

   ![Tworzenie nowego potoku platformy Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Wybierz pozycję **Użyj klasycznego edytora** , aby utworzyć potok bez YAML.

3. Wybierz typ źródła, projekt zespołowy i repozytorium. Następnie wybierz pozycję **Kontynuuj**.

   ![Wybierz projekt Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na stronie **Wybierz szablon** wybierz pozycję **puste zadanie**.

### <a name="add-npm-task"></a>Dodawanie zadania npm

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wprowadź wartość "npm" w polu wyszukiwania zadania i wybierz pozycję **npm**.

   ![Wybieranie zadania npm](./media/setup-cicd-vs-code/search-npm.png)

2. Nadaj zadanie **nazwę wyświetlaną**. Zmień opcję **polecenia** na *niestandardową* i wprowadź następujące polecenie w **poleceniu i argumentach**. Pozostaw pozostałe domyślne opcje.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Wprowadź konfiguracje dla zadania npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Dodawanie zadania wiersza polecenia

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **wiersz polecenia**.

2. Nadaj zadanie **nazwę wyświetlaną** i wprowadź następujący skrypt. Zmodyfikuj skrypt przy użyciu nazwy repozytorium i nazwy projektu.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Wprowadź konfiguracje dla zadania wiersza polecenia](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Zadanie dodawania kopii plików

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **pliki do skopiowania**. Następnie wprowadź następujące konfiguracje.

   |Parametr|Dane wejściowe|
   |-|-|
   |Nazwa wyświetlana|Kopiuj pliki do: $ (Build. artifactstagingdirectory)|
   |Folder źródłowy|`$(system.defaultworkingdirectory)`| 
   |Zawartość| `**\Deploy\**` |
   |Folder docelowy| `$(build.artifactstagingdirectory)`|

   ![Wprowadź konfiguracje dla zadania kopiowania](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Zadanie dodawania artefaktów publikowania kompilacji

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **artefakty kompilacji publikowania** i wybierz opcję z czarną ikoną strzałki. 

2. Nie należy zmieniać żadnej konfiguracji domyślnej.

### <a name="save-and-run"></a>Zapisz i uruchom

Po zakończeniu dodawania npm, wiersza polecenia, kopiowania plików i publikowania artefaktów kompilacji wybierz pozycję **zapisz & kolejkę**. Po wyświetleniu monitu wprowadź komentarz do zapisu, a następnie wybierz pozycję **Zapisz i uruchom**.

## <a name="release-with-azure-pipelines"></a>Wydanie z Azure Pipelines

Otwórz przeglądarkę internetową i przejdź do projektu Visual Studio Code Azure Stream Analytics.

1. W obszarze **potoki** w menu nawigacji po lewej stronie wybierz pozycję **wersje**. Następnie wybierz pozycję **Nowy potok**.

2. Wybierz pozycję **Rozpocznij od pustego zadania**.

3. W polu **artefakty** wybierz pozycję **+ Dodaj artefakt**. W obszarze **Źródło**wybierz właśnie utworzony potok kompilacji i wybierz pozycję **Dodaj**.

   ![Wprowadź artefakt potoku kompilacji](./media/setup-cicd-vs-code/build-artifact.png)

4. Zmień nazwę **etapu 1** , aby **wdrożyć zadanie w środowisku testowym**.

5. Dodaj nowy etap i nadaj mu nazwę **Wdróż zadanie w środowisku produkcyjnym**.

### <a name="add-tasks"></a>Dodaj zadania

1. Z listy rozwijanej zadania wybierz pozycję **Wdróż zadanie w środowisku testowym**. 

2. Wybierz **+** **zadanie agent** i Wyszukaj *wdrożenie grupy zasobów platformy Azure*. Wprowadź następujące parametry:

   |Ustawienie|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdróż myASAJob*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę grupy zasobów testowych, która będzie zawierać zadanie Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację grupy zasobów testowych.|
   |Lokalizacja szablonu| *Połączony artefakt*|
   |Szablon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.jsna |
   |Parametry szablonu|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.jsna|
   |Zastąp parametry szablonu|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Tryb wdrożenia|Przyrostowy|

3. Z listy rozwijanej zadania wybierz pozycję **Wdróż zadanie w środowisku produkcyjnym**.

4. Wybierz **+** **zadanie agent** i Wyszukaj *wdrożenie grupy zasobów platformy Azure*. Wprowadź następujące parametry:

   |Ustawienie|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdróż myASAJob*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę produkcyjnej grupy zasobów, która będzie zawierać zadanie Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację produkcyjnej grupy zasobów.|
   |Lokalizacja szablonu| *Połączony artefakt*|
   |Szablon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.jsna |
   |Parametry szablonu|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.jsna|
   |Zastąp parametry szablonu|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Tryb wdrożenia|Przyrostowy|

### <a name="create-release"></a>Utwórz wydanie

Aby utworzyć wydanie, wybierz pozycję **Utwórz wydanie** w prawym górnym rogu.

![Tworzenie wydania przy użyciu Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby użyć tożsamości zarządzanej dla Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, musisz zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej na temat sposobu [wdrażania ADLS Gen1 z zarządzaną tożsamością przy użyciu szablonu Menedżer zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą Visual Studio Code (wersja zapoznawcza)](visual-studio-code-local-run.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)
