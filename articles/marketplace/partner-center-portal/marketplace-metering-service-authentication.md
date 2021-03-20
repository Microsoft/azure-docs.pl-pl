---
title: Strategie uwierzytelniania usługi pomiaru w portalu Marketplace | Portal Azure Marketplace
description: Strategie uwierzytelniania usługi pomiarów obsługiwane w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657077"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Strategie uwierzytelniania usługi pomiaru w portalu Marketplace

Usługa pomiaru Marketplace obsługuje dwie strategie uwierzytelniania:

* [Token zabezpieczający usługi Azure AD](../../active-directory/develop/access-tokens.md)
* [Tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/overview.md) 

Wyjaśnimy, kiedy i jak używać różnych strategii uwierzytelniania do bezpiecznego przesyłania niestandardowych liczników przy użyciu usługi pomiaru Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Korzystanie z tokenu zabezpieczeń usługi Azure AD

Odpowiednie typy ofert to transakcyjne SaaS i aplikacje platformy Azure z typem planu zarządzanej aplikacji.  

Prześlij niestandardowe liczniki przy użyciu wstępnie zdefiniowanego identyfikatora aplikacji usługi Azure AD do uwierzytelnienia.

W przypadku ofert SaaS jest to jedyna dostępna opcja. Jest to obowiązkowy krok do opublikowania dowolnej oferty SaaS zgodnie z opisem w temacie [Rejestrowanie aplikacji SaaS](./pc-saas-registration.md).

W przypadku aplikacji platformy Azure z zarządzanym planem aplikacji należy rozważyć użycie tej strategii w następujących przypadkach:

* Masz już mechanizm komunikowania się z usługami zaplecza i chcesz rozłożyć ten mechanizm, aby emitować niestandardowe liczniki z centralnej usługi.
* Istnieje złożona logika niestandardowych liczników.  Uruchom tę logikę w centralnej lokalizacji zamiast zasobów aplikacji zarządzanej.

Po zarejestrowaniu aplikacji można programowo zażądać tokenu zabezpieczeń usługi Azure AD. Wydawca powinien używać tego tokenu i wysłać żądanie rozwiązania problemu.

Aby uzyskać więcej informacji na temat tych tokenów, zobacz [Azure Active Directory tokeny dostępu](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Uzyskaj token oparty na aplikacji usługi Azure AD

#### <a name="http-method"></a>Metoda HTTP

**POUBOJOWEGO**

#### <a name="request-url"></a>*Adres URL żądania*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parametr URI*

|  **Nazwa parametru** |  **Wymagane**  |  **Opis**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Prawda         | Identyfikator dzierżawy zarejestrowanej aplikacji usługi Azure AD.   |
| | | |

#### <a name="request-header"></a>*Nagłówek żądania*

|  **Nazwa nagłówka**    |  **Wymagane**  |  **Opis**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Prawda         | Typ zawartości skojarzony z żądaniem. Wartość domyślna to `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Treść żądania*

|  **Nazwa właściwości**  |  **Wymagane**  |  **Opis**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Prawda         | Typ udzielania. Użyj polecenia `client_credentials`. |
|  `Client_id`        |   Prawda         | Identyfikator klienta/aplikacji skojarzony z aplikacją usługi Azure AD.|
|  `client_secret`    |   Prawda         | Wpis tajny skojarzony z aplikacją usługi Azure AD.  |
|  `Resource`         |   Prawda         | Zasób docelowy, dla którego zażądano tokenu. Użyj polecenia `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Odpowiedź*

|  **Nazwa**    |  **Typ**  |  **Opis**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Żądanie powiodło się.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Przykładowy token odpowiedzi:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Korzystanie z tokenu tożsamości zarządzanych przez platformę Azure

Odpowiedni typ oferty to aplikacje platformy Azure z typem planu aplikacji zarządzanej.

Zastosowanie tej metody umożliwi uwierzytelnienie wdrożonych zasobów tożsamości w celu wysyłania niestandardowych zdarzeń użycia liczników.  Możesz osadzić kod, który emituje użycie w granicach wdrożenia.

>[!Note]
>Wydawca powinien upewnić się, że zasoby, które emitują użycie, są zablokowane, więc nie zostanie naruszone.

Aplikacja zarządzana może zawierać różne typy zasobów, od Virtual Machines do Azure Functions.  Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu tożsamości zarządzanych dla różnych usług, zobacz [jak używać tożsamości zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources).

Na przykład postępuj zgodnie z poniższymi instrukcjami, aby uwierzytelnić się za pomocą maszyny wirtualnej z systemem Windows.

1. Upewnij się, że zarządzana tożsamość została skonfigurowana przy użyciu jednej z metod:
    * [Interfejs użytkownika Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [Interfejs wiersza polecenia](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [Program PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Szablon Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [Rest](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Zestawy Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Uzyskaj token dostępu dla identyfikatora aplikacji usługi pomiaru Marketplace ( `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` ) przy użyciu tożsamości systemowej, protokołu RDP z maszyną wirtualną, Otwórz konsolę programu PowerShell i uruchom poniższe polecenie

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Pobierz identyfikator aplikacji zarządzanej z właściwości "zarządzane" bieżącej grupy zasobów

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Usługa pomiaru Marketplace wymaga, aby raportować użycie w `resourceID` i w `resourceUsageId` przypadku aplikacji zarządzanej.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Użyj [interfejsu API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md) , aby emitować użycie.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie oferty aplikacji platformy Azure](../create-new-azure-apps-offer.md)
* [Planowanie oferty SaaS](../plan-saas-offer.md)
