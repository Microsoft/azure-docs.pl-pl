---
title: Użyj Azure Stream Analytics pakiet NuGet ciągłej integracji/ciągłego wdrażania
description: W tym artykule opisano sposób użycia pakietu NuGet pozostałej integracji i ciągłego wdrażania programu Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 175abe54ce5476bece309bbfaf7858cd2e214f52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187663"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Użyj pakietu NuGet pozostałej CI/CD Azure Stream Analytics na potrzeby integracji i programowania 
W tym artykule opisano sposób użycia pakietu NuGet Azure Stream Analytics/CD w celu skonfigurowania ciągłego procesu integracji i wdrażania.

Skorzystaj z wersji 2.3.0000.0 lub nowszej [narzędzia Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) , aby uzyskać pomoc techniczną dla programu MSBuild.

Dostępny jest pakiet NuGet: [Microsoft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Zapewnia ona narzędzia programu MSBuild, uruchomienia lokalnego i wdrażania, które obsługują proces ciągłej integracji i wdrażania [Stream Analytics projektów programu Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> Pakiet NuGet może być używany tylko z 2.3.0000.0 lub nowszą wersją narzędzi Stream Analytics Tools for Visual Studio. Jeśli masz projekty utworzone w poprzednich wersjach narzędzi Visual Studio, po prostu otwórz je przy użyciu wersji 2.3.0000.0 lub nowszej i Zapisz. Następnie nowe możliwości są włączone. 

Aby uzyskać więcej informacji, zobacz [Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Podobnie jak w przypadku standardowego środowiska MSBuild programu Visual Studio, w celu utworzenia projektu dostępne są dwie opcje. Możesz kliknąć prawym przyciskiem myszy projekt, a następnie wybrać polecenie **Kompiluj**. Można również użyć programu **MSBuild** w pakiecie NuGet z wiersza polecenia.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Po pomyślnym skompilowaniu projektu programu Stream Analytics Visual Studio generowane są dwa Azure Resource Manager pliki szablonów w folderze **bin/[Debug/retails]/Deploy** : 

* Plik szablonu Menedżer zasobów

   `[ProjectName].JobTemplate.json`

* Plik parametrów Menedżer zasobów
   
   `[ProjectName].JobTemplate.parameters.json`

Parametry domyślne w parameters.jsna pliku pochodzą z ustawień w projekcie programu Visual Studio. Jeśli chcesz wdrożyć program w innym środowisku, Zastąp odpowiednio parametry.

> [!NOTE]
> Dla wszystkich poświadczeń wartości domyślne są ustawiane na wartość null. Musisz ustawić **required** wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej na temat sposobu [wdrażania przy użyciu pliku szablonu Menedżer zasobów i Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej na temat [używania obiektu jako parametru w szablonie Menedżer zasobów](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Aby użyć tożsamości zarządzanej dla Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, musisz zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej na temat sposobu [wdrażania ADLS Gen1 z zarządzaną tożsamością przy użyciu szablonu Menedżer zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Narzędzie wiersza polecenia

### <a name="build-the-project"></a>Kompilowanie projektu
Pakiet NuGet ma narzędzie wiersza polecenia o nazwie **SA.exe**. Obsługuje ona kompilację projektu i testowanie lokalne na dowolnym komputerze, którego można użyć w procesie ciągłej integracji i ciągłego dostarczania. 

Pliki wdrożenia są domyślnie umieszczane w bieżącym katalogu. Ścieżkę wyjściową można określić za pomocą następującego parametru-OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Lokalne testowanie skryptu

Jeśli projekt zawiera lokalne pliki wejściowe w programie Visual Studio, można uruchomić zautomatyzowany test skryptu przy użyciu polecenia *localrun* . Wynik wyjściowy jest umieszczany w bieżącym katalogu.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generuj plik definicji zadania do użycia z interfejsem API programu Stream Analytics PowerShell

Polecenie *ARM* pobiera szablon zadania i pliki parametrów szablonu zadania generowane przez kompilację jako dane wejściowe. Następnie łączy je w plik JSON definicji zadania, który może być używany z interfejsem API programu Stream Analytics PowerShell.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Przykład:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie Azure Stream Analytics zadania w chmurze w programie Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Eksplorowanie zadań Azure Stream Analytics za pomocą programu Visual Studio](stream-analytics-vs-tools.md)
