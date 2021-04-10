---
title: Tworzenie współużytkowanego środowiska Integration Runtime przy użyciu programu PowerShell
description: Dowiedz się, jak utworzyć udostępnione środowisko Integration Runtime w Azure Data Factory, dzięki czemu wiele fabryk danych może uzyskać dostęp do środowiska Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389437"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Utwórz udostępnione środowisko Integration Runtime w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku pokazano, jak utworzyć udostępnione środowisko Integration Runtime w Azure Data Factory. Następnie możesz użyć udostępnionego środowiska Integration Runtime w innej fabryce danych.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Utwórz udostępnione środowisko Integration Runtime w Azure Data Factory

Można ponownie użyć istniejącej infrastruktury środowiska Integration Runtime, która została już skonfigurowana w fabryce danych. To ponowne użycie pozwala utworzyć połączone środowisko Integration Runtime w innej fabryce danych, odwołując się do istniejącego udostępnionego własnego środowiska IR.

Aby zapoznać się z wprowadzeniem i pokazem tej funkcji, Obejrzyj następujący 12-minutowy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Udostępnione środowisko IR**: oryginalne środowisko IR, które działa w ramach infrastruktury fizycznej.  
- **Połączone środowisko IR**: IR, który odwołuje się do innego udostępnionego środowiska IR. Połączone środowisko IR jest logicznym portem IR i używa infrastruktury innego udostępnionego samodzielnego środowiska IR.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Tworzenie udostępnionego samoobsługowego środowiska IR przy użyciu Azure Data Factory interfejsu użytkownika

Aby utworzyć udostępniony samoobsługowy środowisko IR przy użyciu interfejsu użytkownika Azure Data Factory, można wykonać następujące czynności:

1. W obszarze samoobsługowe środowisko IR do udostępnienia wybierz pozycję **Udziel uprawnienia do innej fabryki danych** , a następnie na stronie "Instalacja środowiska Integration Runtime" Wybierz fabrykę danych, w której chcesz utworzyć połączony IR.
      
    ![Przycisk do przyznawania uprawnień na karcie Udostępnianie](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Zwróć uwagę i Skopiuj powyższy "Identyfikator zasobu" samodzielnego środowiska IR do udostępnienia.
         
3. W fabryce danych, do której udzielono uprawnień, Utwórz nowe środowisko IR (połączone), a następnie wprowadź identyfikator zasobu.
      
    ![Przycisk służący do tworzenia własnego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Przycisk służący do tworzenia połączonego środowiska Integration Runtime (własny)](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Pola nazwy i identyfikatora zasobu](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Tworzenie udostępnionego samoobsługowego środowiska IR przy użyciu Azure PowerShell

Aby utworzyć udostępniony samoobsługowy środowisko IR przy użyciu Azure PowerShell, można wykonać następujące czynności: 
1. Tworzenie fabryki danych. 
1. Utwórz własne środowisko Integration Runtime.
1. Udostępnij własne środowisko Integration Runtime innym fabrykom danych.
1. Utwórz połączone środowisko Integration Runtime.
1. Odwołaj udostępnianie.

### <a name="prerequisites"></a>Wymagania wstępne 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/). 

- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami w temacie [Install Azure PowerShell in Windows with PowerShellGet](/powershell/azure/install-az-ps). Program PowerShell służy do uruchamiania skryptu w celu utworzenia własnego środowiska Integration Runtime, które może być współużytkowane z innymi fabrykami danych. 

> [!NOTE]  
> Aby zapoznać się z listą regionów świadczenia usługi Azure, w których Data Factory są obecnie dostępne, wybierz regiony, które interesują Cię z  [produktami dostępnymi według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom środowisko Windows PowerShell Integrated Scripting Environment (ISE).

1. Utwórz zmienne. Skopiuj i wklej poniższy skrypt. Zastąp zmienne, takie jak **subscriptionname** i **ResourceGroupName**, wartościami rzeczywistymi: 

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

1. Zaloguj się i wybierz subskrypcję. Dodaj następujący kod do skryptu, aby zalogować się i wybrać subskrypcję platformy Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Tworzenie grupy zasobów i fabryki danych.

    > [!NOTE]  
    > Ta czynność jest opcjonalna. Jeśli masz już fabrykę danych, Pomiń ten krok. 

    Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji WestEurope: 

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
> Ta czynność jest opcjonalna. Jeśli masz już własne środowisko Integration Runtime, które chcesz udostępnić innym fabrykom danych, Pomiń ten krok.

Uruchom następujące polecenie, aby utworzyć własne środowisko Integration Runtime:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Pobierz klucz uwierzytelniania środowiska Integration Runtime i zarejestruj węzeł

Uruchom następujące polecenie, aby uzyskać klucz uwierzytelniania dla własnego środowiska Integration Runtime:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpowiedź zawiera klucz uwierzytelniania dla własnego środowiska Integration Runtime. Ten klucz jest używany podczas rejestrowania węzła Integration Runtime.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalowanie i Rejestrowanie własnego środowiska Integration Runtime

1. Pobierz własny Instalator środowiska Integration Runtime z [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Uruchom Instalatora, aby zainstalować samodzielną integrację na komputerze lokalnym.

3. Zarejestruj nową integrację samodzielną z kluczem uwierzytelniania pobranym w poprzednim kroku.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Udostępnianie własnego środowiska Integration Runtime innym fabrykom danych

#### <a name="create-another-data-factory"></a>Tworzenie innej fabryki danych

> [!NOTE]  
> Ta czynność jest opcjonalna. Jeśli masz już fabrykę danych, którą chcesz udostępnić, Pomiń ten krok. Jednak w celu dodania lub usunięcia przypisań ról do innej fabryki danych wymagane są `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) lub [właściciel](../role-based-access-control/built-in-roles.md#owner).

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Udziel uprawnienia

Udziel uprawnienia do fabryki danych, która musi mieć dostęp do utworzonego i zarejestrowanego środowiska Integration Runtime.

> [!IMPORTANT]  
> Nie pomijaj tego kroku!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Tworzenie połączonego środowiska Integration Runtime (własny)

Uruchom następujące polecenie, aby utworzyć połączone środowisko Integration Runtime (własne):

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Teraz można użyć tego połączonego środowiska Integration Runtime w dowolnej połączonej usłudze. Połączone środowisko Integration runtime używa udostępnionego środowiska Integration Runtime do uruchamiania działań.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odwołaj udostępnianie środowiska Integration Runtime z fabryki danych

Aby odwołać dostęp do fabryki danych z udostępnionego środowiska Integration Runtime, uruchom następujące polecenie:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Aby usunąć istniejące połączone środowisko Integration Runtime, uruchom następujące polecenie w ramach udostępnionego środowiska Integration Runtime:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>Monitorowanie

#### <a name="shared-ir"></a>Udostępniony IR

![Opcje znajdowania udostępnionego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorowanie udostępnionego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Połączony IR

![Opcje znajdowania połączonego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorowanie połączonego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Znane ograniczenia dotyczące samodzielnego udostępniania środowiska IR

* Fabryka danych, w której jest tworzone połączone środowisko IR, musi mieć [zarządzaną tożsamość](../active-directory/managed-identities-azure-resources/overview.md). Domyślnie fabryki danych utworzone w Azure Portal lub polecenia cmdlet programu PowerShell mają niejawnie utworzoną tożsamość zarządzaną. Jednak gdy Fabryka danych jest tworzona za pomocą szablonu Azure Resource Manager lub zestawu SDK, należy jawnie ustawić właściwość **Identity** . To ustawienie zapewnia, że Menedżer zasobów tworzy fabrykę danych, która zawiera zarządzaną tożsamość.

* Zestaw Data Factory .NET SDK, który obsługuje tę funkcję, musi być w wersji 1.1.0 lub nowszej.

* Aby udzielić uprawnień, potrzebna jest rola właściciela lub dziedziczona rola właściciela w fabryce danych, w której istnieje udostępniony środowisko IR.

* Funkcja udostępniania działa tylko dla fabryk danych w ramach tej samej dzierżawy usługi Azure AD.

* W przypadku [użytkowników Gości](../active-directory/governance/manage-guest-access-with-access-reviews.md)usługi Azure AD funkcja wyszukiwania w interfejsie użytkownika, która wyświetla listę wszystkich fabryk danych za pomocą słowa kluczowego wyszukiwania, nie działa. Jednak o ile użytkownik-Gość jest właścicielem fabryki danych, możesz udostępnić środowisko IR bez funkcji wyszukiwania. W przypadku zarządzanej tożsamości fabryki danych, która musi udostępniać środowisko IR, wprowadź tę tożsamość zarządzaną w polu **Przypisz uprawnienia** i wybierz pozycję **Dodaj** w interfejsie użytkownika Data Factory.

  > [!NOTE]
  > Ta funkcja jest dostępna tylko w Data Factory v2.


### <a name="next-steps"></a>Następne kroki

- Zapoznaj [się z tematami dotyczącymi środowiska Integration Runtime w Azure Data Factory](./concepts-integration-runtime.md).

- Dowiedz się [, jak utworzyć własne środowisko Integration Runtime w Azure Portal](./create-self-hosted-integration-runtime.md).