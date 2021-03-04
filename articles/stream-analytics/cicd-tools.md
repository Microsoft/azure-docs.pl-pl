---
title: Automatyzowanie kompilacji, testów i wdrożeń zadania Azure Stream Analytics przy użyciu narzędzi CI/CD
description: W tym artykule opisano sposób korzystania z Azure Stream Analytics narzędzi CI/CD do autokompilowania, testowania i wdrażania projektu Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036966"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatyzowanie kompilacji, testów i wdrożeń zadania Azure Stream Analytics przy użyciu narzędzi CI/CD

Aby automatycznie kompilować, testować i wdrażać Visual Studio Code Azure Stream Analytics lub projekty programu Visual Studio, można Azure Stream Analytics użyć pakietu npm/CD. Projekty można tworzyć przy użyciu narzędzi programistycznych lub można je eksportować z istniejących Stream Analytics zadań. W tym artykule opisano sposób korzystania z pakietu npm z dowolnym systemem ciągłej integracji/ciągłego wdrażania. Aby uzyskać wdrożenie z Azure Pipelines, zobacz [Korzystanie z usługi Azure DevOps w celu utworzenia potoku Ci/CD dla zadania Stream Analytics](set-up-cicd-pipeline.md).

## <a name="installation"></a>Instalacja

Możesz [pobrać pakiet](https://www.npmjs.com/package/azure-streamanalytics-cicd) bezpośrednio lub zainstalować go [globalnie](https://docs.npmjs.com/downloading-and-installing-packages-globally) za pomocą `npm install -g azure-streamanalytics-cicd` polecenia. Zalecamy użycie polecenia, które może być również używane w ramach zadania skryptu interfejsu wiersza polecenia platformy Azure dla potoku kompilacji w **Azure Pipelines**.

## <a name="build-the-project"></a>Kompilowanie projektu

Pakiet **ASA-streamanalytics-cicd** npm udostępnia narzędzia do generowania szablonów Azure Resource Manager [projektów Stream Analytics Visual Studio Code](./quick-create-visual-studio-code.md) lub [projektów programu Visual Studio](stream-analytics-quick-create-vs.md). Możesz również użyć pakietu npm w systemach Windows, macOS i Linux bez instalowania Visual Studio Code lub programu Visual Studio.

Po zainstalowaniu pakietu Użyj następującego polecenia, aby skompilować projekty Stream Analytics.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

Polecenie *Build* sprawdza składnię słowa kluczowego i wyprowadza szablon Azure Resource Manager.

| Parametr | Opis |
|---|---|
| `-project` | Ścieżka bezwzględna **asaproj.jsw** pliku dla projektu Visual Studio Code lub **[nazwa projektu]. asaproj** dla projektu programu Visual Studio. |
| `-outputPath` | Ścieżka folderu wyjściowego dla szablonów Azure Resource Manager. Jeśli nie jest określony, szablony zostaną umieszczone w bieżącym katalogu. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Po pomyślnym skompilowaniu projektu Stream Analytics w folderze wyjściowym generowane są następujące dwa pliki:

* Plik szablonu Azure Resource Manager

   `[ProjectName].JobTemplate.json`

* Plik parametrów Azure Resource Manager

   `[ProjectName].JobTemplate.parameters.json`

Parametry domyślne w parameters.jsna pliku pochodzą z ustawień w projekcie Visual Studio Code lub Visual Studio. Jeśli chcesz wdrożyć program w innym środowisku, Zastąp odpowiednio parametry.

Wartości domyślne dla wszystkich poświadczeń mają **wartość null**. Przed wdrożeniem na platformie Azure wymagane jest ustawienie wartości.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Aby użyć tożsamości zarządzanej dla Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, musisz zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej na temat sposobu [wdrażania ADLS Gen1 z zarządzaną tożsamością przy użyciu szablonu Menedżer zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Uruchamianie lokalne

Jeśli projekt zawiera lokalne pliki wejściowe, można uruchomić skrypt Stream Analytics lokalnie za pomocą `localrun` polecenia.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametr | Opis |
|---|---|
| `-project` | Ścieżka **asaproj.js** pliku dla projektu Visual Studio Code lub **[nazwa projektu]. asaproj** dla projektu programu Visual Studio. |
| `-outputPath` | Ścieżka do folderu wyjściowego. Jeśli nie jest określony, pliki wyników wyjściowych zostaną umieszczone w bieżącym katalogu. |
| `-customCodeZipFilePath` | Ścieżka pliku ZIP dla niestandardowego kodu języka C#, taka jak UDF lub Deserializator, jeśli są używane. Pakowanie bibliotek DLL do pliku zip i określanie tej ścieżki. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> Format UDF języka JavaScript działa tylko w systemie Windows.

## <a name="automated-test"></a>Testowanie automatyczne

Aby skonfigurować i uruchomić testy automatyczne dla skryptu Stream Analytics, można użyć pakietu npm/CD.

### <a name="add-a-test-case"></a>Dodawanie przypadku testowego

Przypadki testowe są opisane w pliku konfiguracji testu. Aby rozpocząć, użyj polecenia, `addtestcase` Aby dodać szablon przypadku testowego do pliku konfiguracji testu. Jeśli plik konfiguracji testu nie istnieje, zostanie on utworzony domyślnie.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parametr | Opis |
|---|---|
| `-project` | Ścieżka **asaproj.js** pliku dla projektu Visual Studio Code lub **[nazwa projektu]. asaproj** dla projektu programu Visual Studio. |
| `-testConfigPath` | Ścieżka pliku konfiguracji testu. Jeśli nie jest określony, plik zostanie przeszukany w **\Test** w bieżącym katalogu **asaproj.jsw** pliku, a domyślna nazwa pliku **testConfig.jswłączona**. Nowy plik zostanie utworzony, jeśli nie istnieje. |

> [!NOTE]
> `Script`Wartość w wygenerowanym **testConfig.jsw** pliku jest tylko dla zapewnienia kontekstu; Nie jest on używany w logice testowej. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Jeśli plik konfiguracji testu jest pusty, następująca zawartość jest zapisywana w pliku. W przeciwnym razie przypadek testowy zostanie dodany do tablicy **)**. Niezbędne konfiguracje wejściowe są wypełniane automatycznie zgodnie z plikami konfiguracji wejściowej, jeśli istnieją. W przeciwnym razie wartości domyślne są konfigurowane. Przed uruchomieniem testu należy określić **ścieżkę** do wszystkich danych wejściowych i oczekiwanych danych wyjściowych. Konfigurację można zmienić ręcznie.

Jeśli sprawdzanie poprawności testu ma ignorować niektóre dane wyjściowe, należy ustawić **wymagane** pole, które ma oczekiwane dane wyjściowe na **wartość false**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Obecnie jedyną dozwoloną wartością `ScriptType` elementu jest `InputMock` , która jest również wartością domyślną. Jeśli ustawisz ją na inną wartość, zostanie ona zignorowana i `InputMock` zostanie użyta wartość domyślna (). 

### <a name="run-a-unit-test"></a>Uruchom test jednostkowy

Poniższe polecenie służy do uruchamiania wielu przypadków testowych dla projektu. Podsumowanie wyników testów jest generowane w folderze wyjściowym. Proces kończy się z kodem **0** dla wszystkich testów zakończonych nieprzerwanie; **-1** w przypadku wystąpienia wyjątku; **-2** dla testów nie powiodło się.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametr | Opis |
|---|---|
| `-project` | Ścieżka **asaproj.js** pliku dla projektu Visual Studio Code lub **[nazwa projektu]. asaproj** dla projektu programu Visual Studio. |
| `-testConfigPath` | Ścieżka do pliku konfiguracji testu. Jeśli nie jest określony, plik zostanie przeszukany w **\Test** w bieżącym katalogu **asaproj.jsw** pliku, a domyślna nazwa pliku **testConfig.jswłączona**.
| `-outputPath` | Ścieżka do folderu wyjściowego z wynikami testu. Jeśli nie jest określony, pliki wyników wyjściowych zostaną umieszczone w bieżącym katalogu. |
| `-customCodeZipFilePath` | Ścieżka pliku ZIP dla niestandardowego kodu, takiego jak UDF lub Deserializator, jeśli są używane. |

Po zakończeniu wszystkich testów w folderze wyjściowym zostanie wygenerowane podsumowanie wyników testów w formacie JSON. Plik podsumowania ma nazwę **testResultSummary.jsna**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

Możesz użyć szablonu Azure Resource Manager i plików parametrów wygenerowanych z kompilacji, aby [wdrożyć zadanie na platformie Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Następne kroki

* [Ciągła integracja i ciągłe wdrażanie dla Azure Stream Analytics](cicd-overview.md)
* [Skonfiguruj potok ciągłej integracji/ciągłego wdrażania dla Stream Analytics zadania przy użyciu Azure Pipelines](set-up-cicd-pipeline.md)
