---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563358"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Przygotowywanie do uwierzytelniania żądań Azure Resource Manager
Musisz uwierzytelnić wszystkie operacje wykonywane na zasobach przy użyciu [Azure Resource Manager][lnk-authenticate-arm] z Azure Active Directory (AD). Najprostszym sposobem konfiguracji jest użycie programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Przed kontynuowaniem zainstaluj [Azure PowerShell polecenia cmdlet][lnk-powershell-install] .

Poniższe kroki pokazują, jak skonfigurować uwierzytelnianie hasła dla aplikacji usługi AD przy użyciu programu PowerShell. Te polecenia można uruchomić w standardowej sesji programu PowerShell.

1. Zaloguj się do subskrypcji platformy Azure przy użyciu następującego polecenia:

    ```powershell
    Connect-AzAccount
    ```

1. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się na platformie Azure spowoduje przyznanie dostępu do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Użyj następującego polecenia, aby wyświetlić listę dostępnych subskrypcji platformy Azure do użycia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj poniższego polecenia, aby wybrać subskrypcję, która ma być używana do uruchamiania poleceń zarządzania centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Zanotuj swój **TenantId** i identyfikator **subskrypcji**. Będą one potrzebne później.
3. Utwórz nową aplikację Azure Active Directory przy użyciu następującego polecenia, zastępując posiadaczy miejsc:
   
   * **{Display Name}:** nazwa wyświetlana aplikacji, taka jak **MySampleApp**
   * **{Adres URL strony głównej}:** adres URL strony głównej aplikacji, na przykład **http: \/ /mysampleapp/Home**. Ten adres URL nie musi wskazywać prawdziwej aplikacji.
   * **{Identyfikator aplikacji}:** Unikatowy identyfikator, taki jak **http: \/ /mysampleapp**. Ten adres URL nie musi wskazywać prawdziwej aplikacji.
   * **{Password}:** Hasło używane do uwierzytelniania w aplikacji.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Zanotuj **Identyfikator aplikacji utworzony** przez Ciebie. Będzie on potrzebny później.
5. Utwórz nową nazwę główną usługi przy użyciu następującego polecenia, zastępując element **{MyApplicationId}** identyfikatorem **aplikacji** z poprzedniego kroku:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Skonfiguruj przypisanie roli przy użyciu następującego polecenia, zastępując element **{MyApplicationId}** identyfikatorem Twojej **aplikacji**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Teraz można utworzyć aplikację usługi Azure AD, która umożliwia uwierzytelnianie z poziomu niestandardowej aplikacji w języku C#. W dalszej części tego samouczka potrzebne są następujące wartości:

* TenantId
* SubscriptionId
* ApplicationId
* Hasło

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps