---
title: Użyj usługi Azure DevOps, aby utworzyć potok ciągłej integracji/ciągłego wdrażania dla zadania Stream Analytics
description: W tym artykule opisano sposób konfigurowania potoku ciągłej integracji i wdrażania (CI/CD) dla zadania Azure Stream Analytics w usłudze Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 82a2c3047f851c9fbc273cd13e730572c38b6bcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640372"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Użyj usługi Azure DevOps, aby utworzyć potok ciągłej integracji/ciągłego wdrażania dla zadania Stream Analytics

W tym artykule dowiesz się, jak utworzyć potoki [kompilacji](/azure/devops/pipelines/get-started/pipelines-get-started) i [wydania](/azure/devops/pipelines/release/define-multistage-release-process) usługi Azure DevOps za pomocą Azure Stream Analytics narzędzia Ci/CD.

## <a name="commit-your-stream-analytics-project"></a>Zatwierdzanie projektu Stream Analytics

Przed rozpoczęciem należy zatwierdzić pełne Stream Analytics projekty jako pliki źródłowe w repozytorium [Azure DevOps](/azure/devops/user-guide/source-control) . Możesz odwoływać się do tego [przykładowego repozytorium](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo) i [Stream Analytics kodu źródłowego projektu](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo) w Azure Pipelines.

W procedurach przedstawionych w tym artykule jest używany projekt Stream Analytics Visual Studio Code. Jeśli używasz projektu programu Visual Studio, postępuj zgodnie z instrukcjami [automatyzacji kompilacji, testów i wdrożeń zadania Azure Stream Analytics przy użyciu narzędzi Ci/CD](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Tworzenie potoku kompilacji

W tej sekcji dowiesz się, jak utworzyć potok kompilacji. 

1. Otwórz przeglądarkę internetową i przejdź do projektu w usłudze Azure DevOps.  

2. W obszarze **potoki** w menu nawigacji po lewej stronie wybierz pozycję **kompilacje**. Następnie wybierz pozycję **Nowy potok**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Tworzenie nowego potoku platformy Azure":::

3. Wybierz pozycję **Użyj klasycznego edytora** , aby utworzyć potok bez YAML.

4. Wybierz typ źródła, projekt zespołowy i repozytorium. Następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Wybierz projekt Azure Stream Analytics":::

5. Na stronie **Wybierz szablon** wybierz pozycję **puste zadanie**.

## <a name="install-npm-package"></a>Zainstaluj pakiet npm

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wprowadź *npm* w polu wyszukiwania zadania i wybierz pozycję **npm**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Wybieranie zadania npm":::

2. Nadaj zadanie **nazwę wyświetlaną**. Zmień opcję **polecenia** na *niestandardową* i wprowadź następujące polecenie w **poleceniu i argumentach**. Pozostaw pozostałe domyślne opcje.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Wprowadź konfiguracje dla zadania npm":::

Jeśli musisz użyć agenta hostowanego z systemem Linux, wykonaj następujące czynności:
1.  Wybierz **specyfikację agenta**
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="Zrzut ekranu przedstawiający Wybieranie specyfikacji agenta.":::

2.  Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wprowadź *wiersz polecenia* w polu Wyszukiwanie zadania i wybierz pozycję **wiersz polecenia**.
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="Zrzut ekranu przedstawiający zadanie wiersza polecenia wyszukiwania. ":::

3.  Nadaj zadanie **nazwę wyświetlaną**. Wprowadź następujące polecenie w **skrypcie**. Pozostaw pozostałe domyślne opcje.

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="Zrzut ekranu przedstawiający wprowadzanie skryptu dla zadania cmd.":::

## <a name="add-a-build-task"></a>Dodawanie zadania kompilacji

1. Na stronie **zmienne** wybierz pozycję **+ Dodaj** w polu **zmienne potoku**. Dodaj następujące zmienne. Ustaw następujące wartości zgodnie z preferencjami:

   |Nazwa zmiennej|Wartość|
   |-|-|
   |projectRootPath|[YourProjectName]|
   |outputPath|Dane wyjściowe|
   |deployPath|Wdróż|

2. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **wiersz polecenia**.

3. Nadaj zadanie **nazwę wyświetlaną** i wprowadź następujący skrypt. Zmodyfikuj skrypt przy użyciu nazwy repozytorium i nazwy projektu.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   Na poniższym obrazie jest stosowany projekt Stream Analytics Visual Studio Code.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Wprowadź konfiguracje dla zadania wiersza polecenia programu Visual Studio Code":::

## <a name="add-a-test-task"></a>Dodawanie zadania testowego

1. Na stronie **zmienne** wybierz pozycję **+ Dodaj** w polu **zmienne potoku**. Dodaj następujące zmienne. Zmodyfikuj wartości przy użyciu ścieżki wyjściowej i nazwy repozytorium.

   |Nazwa zmiennej|Wartość|
   |-|-|
   |testPath|Testowanie|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Dodaj zmienne potoku":::

2. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **wiersz polecenia**.

3. Nadaj zadanie **nazwę wyświetlaną** i wprowadź następujący skrypt. Zmodyfikuj skrypt przy użyciu nazwy pliku projektu i ścieżki do pliku konfiguracji testu. 

   Zobacz [instrukcje dotyczące testów automatycznych](cicd-tools.md#automated-test) , aby uzyskać szczegółowe informacje na temat dodawania i konfigurowania przypadków testowych.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Wprowadź konfiguracje dla zadania wiersza polecenia":::

## <a name="add-a-copy-files-task"></a>Zadanie dodawania plików do kopiowania

Należy dodać zadanie kopiowania pliku, aby skopiować plik podsumowania testu i Azure Resource Manager pliki szablonów do folderu artefaktu. 

1. Na stronie **zadania** wybierz pozycję **+** obok **zadania agenta 1**. Wyszukaj **pliki do skopiowania**. Następnie wprowadź następujące konfiguracje. Przypisanie `**` do **zawartości** powoduje skopiowanie wszystkich plików wyników testu.

   |Parametr|Dane wejściowe|
   |-|-|
   |Nazwa wyświetlana|Kopiuj pliki do: $ (Build. artifactstagingdirectory)|
   |Folder źródłowy|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Zawartość| `**` |
   |Folder docelowy| `$(build.artifactstagingdirectory)`|

2. Rozwiń węzeł **Opcje kontrolki**. Wybierz **nawet wtedy, gdy poprzednie zadanie zakończyło się niepowodzeniem, chyba że kompilacja została anulowana** w trakcie **wykonywania tego zadania**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Wprowadź konfiguracje dla zadania kopiowania":::

## <a name="add-a-publish-build-artifacts-task"></a>Dodaj zadanie publikowania artefaktów kompilacji

1. Na stronie **zadania** wybierz znak plus obok pozycji **zadanie agenta 1**. Wyszukaj **artefakty kompilacji publikowania** i wybierz opcję z czarną ikoną strzałki.

2. Rozwiń węzeł **Opcje kontrolki**. Wybierz **nawet wtedy, gdy poprzednie zadanie zakończyło się niepowodzeniem, chyba że kompilacja została anulowana** w trakcie **wykonywania tego zadania**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Wprowadź konfiguracje dla zadania publikowania":::

## <a name="save-and-run"></a>Zapisz i uruchom

Po zakończeniu dodawania pakietu npm, wiersza polecenia, kopiowania plików i publikowania zadań kompilacji artefaktów wybierz pozycję **zapisz & kolejkę**. Po wyświetleniu monitu wprowadź komentarz do zapisu, a następnie wybierz pozycję **Zapisz i uruchom**. Wyniki testów można pobrać ze strony **podsumowania** potoku.

## <a name="check-the-build-and-test-results"></a>Sprawdź kompilacje i wyniki testów

Plik podsumowania testu i pliki szablonów Azure Resource Manager można znaleźć w folderze **opublikowanym** .

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Sprawdź kompilację i wynik testu":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Sprawdź artefakty":::

## <a name="release-with-azure-pipelines"></a>Wydanie z Azure Pipelines

W tej sekcji dowiesz się, jak utworzyć potok wersji. 

Otwórz przeglądarkę internetową i przejdź do projektu Visual Studio Code Azure Stream Analytics.

1. W obszarze **potoki** w menu nawigacji po lewej stronie wybierz pozycję **wersje**. Następnie wybierz pozycję **Nowy potok**.

2. Wybierz pozycję **Rozpocznij od pustego zadania**.

3. W polu **artefakty** wybierz pozycję **+ Dodaj artefakt**. W obszarze **Źródło** wybierz utworzony potok kompilacji i wybierz pozycję **Dodaj**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Wprowadź artefakt potoku kompilacji":::

4. Zmień nazwę **etapu 1** , aby **wdrożyć zadanie w środowisku testowym**.

5. Dodaj nowy etap i nadaj mu nazwę **Wdróż zadanie w środowisku produkcyjnym**.

### <a name="add-deploy-tasks"></a>Dodaj zadania wdrażania

1. Z listy rozwijanej zadania wybierz pozycję **Wdróż zadanie w środowisku testowym**.

2. Wybierz **+** **zadanie obok zadania agenta** i Wyszukaj **wdrożenie szablonu ARM**. Wprowadź następujące parametry:

   |Parametr|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdróż myASAProject*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę grupy zasobów testowych, która będzie zawierać zadanie Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację grupy zasobów testowych.|
   |Lokalizacja szablonu| Połączony artefakt|
   |Template| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demonstracyjne-CI — Wdróż/upuść/myASAProject.JobTemplate.jsna |
   |Parametry szablonu|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demonstracyjne-CI — Wdróż/upuść/myASAProject.JobTemplate.parameters.jsna |
   |Zastąp parametry szablonu|-<arm_template_parameter> "wartość". Parametry można definiować przy użyciu **zmiennych**.|
   |Tryb wdrożenia|Przyrostowy|

3. Z listy rozwijanej zadania wybierz pozycję **Wdróż zadanie w środowisku produkcyjnym**.

4. Wybierz **+** **zadanie obok zadania agenta** i Wyszukaj *wdrożenie szablonu ARM*. Wprowadź następujące parametry:

   |Parametr|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdróż myASAProject*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę produkcyjnej grupy zasobów, która będzie zawierać zadanie Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację produkcyjnej grupy zasobów.|
   |Lokalizacja szablonu| *Połączony artefakt*|
   |Template| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demonstracyjne-CI — Wdróż/upuść/myASAProject.JobTemplate.jsna |
   |Parametry szablonu|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demonstracyjne-CI — Wdróż/upuść/myASAProject.JobTemplate.parameters.jsna |
   |Zastąp parametry szablonu|-<arm_template_parameter> "wartość"|
   |Tryb wdrożenia|Przyrostowy|

### <a name="create-a-release"></a>Tworzenie wersji

Aby utworzyć wydanie, wybierz pozycję **Utwórz wydanie** w prawym górnym rogu.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Tworzenie wydania przy użyciu Azure Pipelines":::

## <a name="next-steps"></a>Następne kroki

* [Ciągła integracja i ciągłe wdrażanie dla Azure Stream Analytics](cicd-overview.md)
* [Automatyzowanie kompilowania, testowania i wdrażania zadania Azure Stream Analytics przy użyciu narzędzi CI/CD](cicd-tools.md)