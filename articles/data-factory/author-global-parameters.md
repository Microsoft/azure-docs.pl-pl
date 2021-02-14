---
title: Parametry globalne
description: Ustaw parametry globalne dla każdego środowiska Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 08/31/2020
ms.openlocfilehash: fb9439bc37fcecf1cb5299a09916ebe21c5bc1cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393823"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parametry globalne w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Parametry globalne są stałymi w fabryce danych, które mogą być używane przez potok w dowolnym wyrażeniu. Są one przydatne, gdy istnieje wiele potoków o identycznych nazwach i wartościach parametrów. Podczas podwyższania poziomu fabryki danych przy użyciu procesu integracji ciągłej (CI/CD) można zastąpić te parametry w każdym środowisku. 

## <a name="creating-global-parameters"></a>Tworzenie parametrów globalnych

Aby utworzyć parametr globalny, przejdź do karty *parametry globalne* w sekcji *Zarządzanie* . Wybierz pozycję **Nowy** , aby otworzyć stronę tworzenie.

![Zrzut ekranu, który podświetla nowy przycisk, który wybierzesz, aby utworzyć parametry globalne.](media/author-global-parameters/create-global-parameter-1.png)

W bocznej części, wprowadź nazwę, wybierz typ danych i określ wartość parametru.

![Zrzut ekranu pokazujący, gdzie dodać nazwę, typ danych i wartość nowego parametru globalnego.](media/author-global-parameters/create-global-parameter-2.png)

Po utworzeniu parametru globalnego można go edytować, klikając nazwę parametru. Aby jednocześnie zmienić wiele parametrów, wybierz pozycję **Edytuj wszystko**.

![Utwórz parametry globalne](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Używanie parametrów globalnych w potoku

Parametry globalne mogą być używane w dowolnym [wyrażeniu potoku](control-flow-expression-language-functions.md). Jeśli potok odwołuje się do innego zasobu, takiego jak zestaw danych lub przepływ danych, można przekazać wartość parametru globalnego za pomocą parametrów tego zasobu. Parametry globalne są przywoływane jako `pipeline().globalParameters.<parameterName>` .

![Używanie parametrów globalnych](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Parametry globalne w ciągłej integracji/ciągłego dostarczania

Istnieją dwa sposoby integrowania parametrów globalnych w rozwiązaniu ciągłej integracji i wdrażania:

* Uwzględnij parametry globalne w szablonie ARM
* Wdrażanie parametrów globalnych za pośrednictwem skryptu programu PowerShell

W przypadku większości przypadków użycia zaleca się uwzględnienie parametrów globalnych w szablonie ARM. Ta integracja zostanie zintegrowana z rozwiązaniem przedstawionym w [dokumencie Ci/CD](continuous-integration-deployment.md). Parametry globalne zostaną dodane jako parametr szablonu ARM domyślnie, ponieważ często zmieniają się ze środowiska na środowisko. Dołączanie parametrów globalnych do szablonu ARM można włączyć z poziomu centrum zarządzania.

![Uwzględnij w szablonie ARM](media/author-global-parameters/include-arm-template.png)

Dodanie parametrów globalnych do szablonu ARM powoduje dodanie ustawienia poziomu fabryki, które zastąpi inne ustawienia na poziomie fabryki, takie jak klucz zarządzany przez klienta lub konfiguracja Git w innych środowiskach. Jeśli te ustawienia są włączone w środowisku z podwyższonym poziomem uprawnień, takim jak przeprowadzających lub PROD, lepiej jest wdrożyć parametry globalne za pośrednictwem skryptu programu PowerShell w poniższych krokach.

### <a name="deploying-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Poniższe kroki przedstawiają sposób wdrażania parametrów globalnych za pośrednictwem programu PowerShell. Jest to przydatne, gdy fabryka docelowa ma ustawienia poziomu fabryki, takie jak klucz zarządzany przez klienta.

Podczas publikowania fabryki lub eksportowania szablonu ARM z parametrami globalnymi folder o nazwie *globalParameters* jest tworzony przy użyciu pliku o nazwie *your-factory-name_GlobalParameters.json*. Ten plik jest obiektem JSON, który zawiera wszystkie typy i wartości parametrów globalnych w opublikowanej fabryce.

![Publikowanie parametrów globalnych](media/author-global-parameters/global-parameters-adf-publish.png)

W przypadku wdrażania do nowego środowiska, takiego jak TEST lub PRODUKCYJNy, zaleca się utworzenie kopii tego pliku parametrów globalnych i zastępowanie odpowiednich wartości specyficznych dla środowiska. Po ponownym opublikowaniu oryginalnego pliku parametrów globalnych zostanie on nadpisany, ale kopia dla tego środowiska będzie niedostępna.

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
