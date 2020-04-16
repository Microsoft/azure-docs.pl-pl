---
title: Tworzenie udostępnionego środowiska wykonawczego integracji hostowanego przez użytkownika za pomocą programu PowerShell
description: Dowiedz się, jak utworzyć udostępnione środowisko uruchomieniowe integracji hostowanego samodzielnie w usłudze Azure Data Factory, dzięki czemu wiele fabryk danych może uzyskać dostęp do środowiska wykonawczego integracji.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: cabdb45467f71749184c5f9a6a112242a82d618b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416603"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Tworzenie udostępnionego środowiska wykonawczego integracji hostowanego samodzielnie w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku pokazano, jak utworzyć udostępnione środowisko uruchomieniowe integracji hostowanego samodzielnie w usłudze Azure Data Factory. Następnie można użyć udostępnionego środowiska wykonawczego integracji hostowanego samodzielnie w innej fabryce danych.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Tworzenie udostępnionego, hostowanego samodzielnie podczerwonego podczerwionego interfejsu użytkownika przy użyciu interfejsu użytkownika usługi Azure Data Factory

Aby utworzyć współdzielone samodzielnie podczerwone podczerwone przy użyciu interfejsu użytkownika usługi Azure Data Factory, można wykonać następujące kroki:

1. W samodzielnym hostowanym podczerwonym podczerwonym do udostępnienia udziel uprawnień do fabryki danych, w której chcesz utworzyć połączone podczerwone podczerwone.
      
    ![Przycisk udzielania uprawnień na karcie Udostępnianie](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Wybory do przypisywania uprawnień](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Zanotuj identyfikator zasobu samodzielnie hostowanego podczerwonu do udostępnienia.
      
   ![Lokalizacja identyfikatora zasobu](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. W fabryce danych, do której przyznano uprawnienia, utwórz nową, samodzielnie hostowane podczerwone podczerwone (połączone) i wprowadź identyfikator zasobu.
      
   ![Przycisk do tworzenia połączonego środowiska wykonawczego integracji hostowanego samodzielnie](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Pola dotyczące nazwy i identyfikatora zasobu](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Tworzenie udostępnionego, hostowanego samodzielnie podczerwonu przy użyciu programu Azure PowerShell

Aby utworzyć współdzielone samodzielnie podczerwone podczerwone przy użyciu programu Azure PowerShell, można wykonać następujące kroki: 
1. Tworzenie fabryki danych. 
1. Utwórz własne środowisko Integration Runtime.
1. Udostępnianie środowiska wykonawczego integracji hostowanego samodzielnie z innymi fabrykami danych.
1. Tworzenie połączonego środowiska wykonawczego integracji.
1. Odwołaj udostępnianie.

### <a name="prerequisites"></a>Wymagania wstępne 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. 

- **Program Azure PowerShell**. Postępuj zgodnie z instrukcjami w [install Azure PowerShell w systemie Windows z programem PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Program PowerShell służy do uruchamiania skryptu w celu utworzenia środowiska uruchomieniowego integracji hostowanego samodzielnie, które można udostępniać innym fabrykom danych. 

> [!NOTE]  
> Aby uzyskać listę regionów platformy Azure, w których usługa Data Factory jest obecnie dostępna, wybierz regiony, które Cię interesują w [produktach dostępnych według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom środowisko Windows PowerShell Integrated Scripting Environment (ISE).

1. Tworzenie zmiennych. Skopiuj i wklej następujący skrypt. Zastąp zmienne, takie jak **SubscriptionName** i **ResourceGroupName,** wartościami rzeczywistymi: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Zaloguj się i wybierz subskrypcję. Dodaj następujący kod do skryptu, aby się zalogować i wybrać subskrypcję platformy Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Tworzenie grupy zasobów i fabryki danych.

    > [!NOTE]  
    > Ten krok jest opcjonalny. Jeśli masz już fabrykę danych, pomiń ten krok. 

    Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) przy użyciu polecenia [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji WestEurope: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Uruchom następujące polecenie, aby utworzyć fabrykę danych: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

> [!NOTE]  
> Ten krok jest opcjonalny. Jeśli masz już środowisko uruchomieniowe integracji hostowanego samodzielnie, które chcesz udostępnić innym fabrykom danych, pomiń ten krok.

Uruchom następujące polecenie, aby utworzyć środowisko uruchomieniowe integracji hostowanego samodzielnie:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Pobierz klucz uwierzytelniania środowiska uruchomieniowego integracji i zarejestruj węzeł

Uruchom następujące polecenie, aby uzyskać klucz uwierzytelniania dla środowiska wykonawczego integracji hostowanego samodzielnie:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpowiedź zawiera klucz uwierzytelniania dla tego środowiska wykonawczego integracji hostowanego samodzielnie. Ten klucz jest używany podczas rejestrowania węzła środowiska wykonawczego integracji.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalowanie i rejestrowanie środowiska uruchomieniowego integracji hostowanego samodzielnie

1. Pobierz instalator środowiska wykonawczego integracji hostowanego samodzielnie z środowiska wykonawczego [usługi Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Uruchom instalatora, aby zainstalować integrację hostowanego samodzielnie na komputerze lokalnym.

3. Zarejestruj nową integrację hostowanego samodzielnie z kluczem uwierzytelniania pobranym w poprzednim kroku.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Udostępnianie środowiska wykonawczego integracji hostowanego samodzielnie z inną fabryką danych

#### <a name="create-another-data-factory"></a>Tworzenie innej fabryki danych

> [!NOTE]  
> Ten krok jest opcjonalny. Jeśli masz już fabrykę danych, której chcesz udostępnić, pomiń ten krok.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Udzielanie uprawnień

Udziel uprawnień do fabryki danych, która musi uzyskać dostęp do środowiska uruchomieniowego integracji hostowanego przez ciebie, utworzonego i zarejestrowanego.

> [!IMPORTANT]  
> Nie pomijaj tego kroku!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Tworzenie połączonego środowiska wykonawczego integracji hostowanego samodzielnie

Uruchom następujące polecenie, aby utworzyć połączone środowisko wykonawcze integracji hostowanego samodzielnie:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Teraz można użyć tego połączonego środowiska wykonawczego integracji w dowolnej połączonej usłudze. Środowisko uruchomieniowe połączonej integracji używa środowiska wykonawczego integracji udostępnionej do uruchamiania działań.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odwoływanie udostępniania środowiska uruchomieniowego integracji z fabryki danych

Aby odwołać dostęp do fabryki danych ze środowiska wykonawczego integracji współużytkowej, uruchom następujące polecenie:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Aby usunąć istniejące środowisko uruchomieniowe połączonej integracji, uruchom następujące polecenie względem udostępnionego środowiska wykonawczego integracji:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Następne kroki

- Przejrzyj [koncepcje środowiska uruchomieniowego integracji w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Dowiedz się, jak [utworzyć środowisko wykonawcze integracji hostowanego samodzielnie w witrynie Azure portal.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
