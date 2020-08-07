---
title: Parametry globalne
description: Ustaw parametry globalne dla każdego środowiska Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854354"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parametry globalne w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Parametry globalne są stałymi w fabryce danych, które mogą być używane przez potok w dowolnym wyrażeniu. Są one przydatne, gdy istnieje wiele potoków o identycznych nazwach i wartościach parametrów. Podczas podwyższania poziomu fabryki danych przy użyciu procesu integracji ciągłej (CI/CD) można zastąpić te parametry w każdym środowisku. 

## <a name="creating-global-parameters"></a>Tworzenie parametrów globalnych

Aby utworzyć parametr globalny, przejdź do karty *parametry globalne* w sekcji *Zarządzanie* . Wybierz pozycję **Nowy** , aby otworzyć stronę tworzenie.

![Utwórz parametry globalne](media/author-global-parameters/create-global-parameter-1.png)

W bocznej części, wprowadź nazwę, wybierz typ danych i określ wartość parametru.

![Utwórz parametry globalne](media/author-global-parameters/create-global-parameter-2.png)

Po utworzeniu parametru globalnego można go edytować, klikając nazwę parametru. Aby jednocześnie zmienić wiele parametrów, wybierz pozycję **Edytuj wszystko**.

![Utwórz parametry globalne](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Używanie parametrów globalnych w potoku

Parametry globalne mogą być używane w dowolnym [wyrażeniu potoku](control-flow-expression-language-functions.md). Jeśli potok odwołuje się do innego zasobu, takiego jak zestaw danych lub przepływ danych, można przekazać wartość parametru globalnego za pomocą parametrów tego zasobu. Parametry globalne są przywoływane jako `pipeline().globalParameters.<parameterName>` .

![Używanie parametrów globalnych](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Parametry globalne w ciągłej integracji/ciągłego dostarczania

Parametry globalne mają unikatowy proces ciągłej integracji/ciągłego wdrażania względem innych jednostek w Azure Data Factory. Podczas publikowania fabryki lub eksportowania szablonu ARM z parametrami globalnymi folder o nazwie *globalParameters* jest tworzony przy użyciu pliku o nazwie *your-factory-name_GlobalParameters.json*. Ten plik jest obiektem JSON, który zawiera wszystkie typy i wartości parametrów globalnych w opublikowanej fabryce.

![Publikowanie parametrów globalnych](media/author-global-parameters/global-parameters-adf-publish.png)

Jeśli wdrażasz program do nowego środowiska, takiego jak TEST lub PRODUKCYJNy, zaleca się utworzenie kopii tego pliku parametrów globalnych i zastępowanie odpowiednich wartości specyficznych dla środowiska. Po ponownym opublikowaniu oryginalnego pliku parametrów globalnych zostanie on nadpisany, ale kopia dla tego środowiska będzie niedostępna.

Na przykład jeśli masz fabrykę o nazwie "ADF-DEV" i parametr globalny typu String o nazwie "Environment" z wartością "dev", podczas publikowania pliku o nazwie *ADF-DEV_GlobalParameters.jsna* zostanie wygenerowany. W przypadku wdrażania do fabryki testowej o nazwie "ADF_TEST" Utwórz kopię pliku JSON (na przykład nazwane ADF-TEST_GlobalParameters.json) i Zastąp wartości parametrów wartościami specyficznymi dla środowiska. Parametr "Environment" może mieć teraz wartość "test". 

![Wdrażanie parametrów globalnych](media/author-global-parameters/powershell-task.png)

Użyj poniższego skryptu programu PowerShell, aby podwyższyć poziom globalnych parametrów do dodatkowych środowisk. Dodaj Azure PowerShell zadanie DevOps przed Template deployment ARM. W zadaniu DevOps należy określić lokalizację nowego pliku parametrów, docelową grupę zasobów i docelową fabrykę danych.

> [!NOTE]
> Aby wdrożyć parametry globalne przy użyciu programu PowerShell, należy użyć co najmniej wersji 4.4.0 polecenia AZ module.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Następne kroki

* Informacje o [procesie ciągłej integracji i wdrażania](continuous-integration-deployment.md) Azure Data Factory
* Dowiedz się, jak używać [języka wyrażeń przepływu sterowania](control-flow-expression-language-functions.md)