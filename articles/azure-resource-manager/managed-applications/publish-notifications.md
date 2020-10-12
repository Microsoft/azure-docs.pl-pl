---
title: Aplikacje zarządzane z powiadomieniami
description: Skonfiguruj zarządzane aplikacje za pomocą punktów końcowych elementu webhook, aby otrzymywać powiadomienia dotyczące tworzenia, aktualizacji, usunięć i błędów w zarządzanych wystąpieniach aplikacji.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 3632a34678c7a0f0e6fa93e5ce8000b07bb413a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054529"
---
# <a name="azure-managed-applications-with-notifications"></a>Aplikacje zarządzane przez platformę Azure z powiadomieniami

Powiadomienia aplikacji zarządzanych przez platformę Azure umożliwiają wydawcom Automatyzowanie akcji opartych na zdarzeniach cyklu życia zarządzanych wystąpień aplikacji. Wydawcy mogą określić niestandardowe punkty końcowe elementu webhook powiadomień, aby otrzymywać powiadomienia o zdarzeniach o nowych i istniejących wystąpieniach aplikacji zarządzanych. Wydawcy mogą konfigurować niestandardowe przepływy pracy w momencie aprowizacji, aktualizacji i usuwania aplikacji.

## <a name="getting-started"></a>Wprowadzenie
Aby zacząć otrzymywać zarządzane aplikacje, uruchom publiczny punkt końcowy HTTPS i określ go podczas publikowania definicji aplikacji katalogu usług lub oferty portalu Azure Marketplace.

Poniżej przedstawiono zalecane kroki umożliwiające szybkie rozpoczęcie pracy:
1. Uruchom publiczny punkt końcowy HTTPS, który rejestruje przychodzące żądania POST i zwraca `200 OK` .
2. Dodaj punkt końcowy do definicji aplikacji katalogu usług lub oferty portalu Azure Marketplace zgodnie z opisem w dalszej części tego artykułu.
3. Utwórz wystąpienie aplikacji zarządzanej, które odwołuje się do definicji aplikacji lub oferty portalu Azure Marketplace.
4. Sprawdź, czy powiadomienia są odbierane.
5. Włącz autoryzację zgodnie z opisem w sekcji **uwierzytelnianie punktu końcowego** w tym artykule.
6. Postępuj zgodnie z instrukcjami w sekcji **Schemat powiadomień** tego artykułu, aby przeanalizować żądania powiadomień i wdrożyć logikę biznesową na podstawie powiadomienia.

## <a name="add-service-catalog-application-definition-notifications"></a>Dodawanie powiadomień definicji aplikacji katalogu usług
#### <a name="azure-portal"></a>Azure Portal
Aby rozpocząć, zobacz [publikowanie aplikacji katalogu usług za pomocą Azure Portal](./publish-portal.md).

![Powiadomienia definicji aplikacji katalogu usług w Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>Interfejs API REST

> [!NOTE]
> Obecnie można podać tylko jeden punkt końcowy we `notificationEndpoints` właściwościach definicji aplikacji.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Dodawanie powiadomień aplikacji zarządzanych przez portal Azure Marketplace
Aby uzyskać więcej informacji, zobacz [Tworzenie oferty aplikacji platformy Azure](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md).

![Powiadomienia aplikacji zarządzane w portalu Azure Marketplace w Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Wyzwalacze zdarzeń
W poniższej tabeli opisano wszystkie możliwe kombinacje elementów EventType i ProvisioningState oraz ich wyzwalacze:

Typ zdarzenia | ProvisioningState | Wyzwalacz dla powiadomienia
---|---|---
PUT | Zaakceptowano | Zarządzana Grupa zasobów została utworzona i została pomyślnie zastawiona po rozpoczęciu aplikacji (przed rozpoczęciem wdrażania w ramach zarządzanej grupy zasobów).
PUT | Sukces | Pełna aprowizacji aplikacji zarządzanej zakończyła się pomyślnie po UMIESZCZENIU.
PUT | Niepowodzenie | Niepowodzenie inicjowania aprowizacji wystąpienia aplikacji w dowolnym momencie.
WYSŁANA | Sukces | Po pomyślnej poprawek w wystąpieniu aplikacji zarządzanej w celu zaktualizowania tagów, zasad dostępu JIT lub tożsamości zarządzanej.
DELETE | Usuwanie | Zaraz po zainicjowaniu przez użytkownika usunięcia wystąpienia aplikacji zarządzanej.
DELETE | Usunięte | Po pełnym i pomyślnym usunięciu zarządzanej aplikacji.
DELETE | Niepowodzenie | Po wystąpieniu błędu w trakcie anulowania aprowizacji, który blokuje usunięcie.
## <a name="notification-schema"></a>Schemat powiadomień
Po utworzeniu punktu końcowego elementu webhook w celu obsługi powiadomień musisz przeanalizować ładunek, aby uzyskać ważne właściwości, które będą działać po powiadomieniu. Powiadomienia dotyczące katalogu usług i aplikacji zarządzanych przez portal Azure Marketplace zapewniają wiele właściwości. Dwie małe różnice przedstawiono w tabeli, która jest zgodna z przykładami.

#### <a name="service-catalog-application-notification-schema"></a>Schemat powiadomień aplikacji katalogu usług
Oto przykładowe powiadomienie dotyczące katalogu usług po pomyślnej aprowizacji wystąpienia aplikacji zarządzanej:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Jeśli Inicjowanie obsługi nie powiedzie się, zostanie wysłane powiadomienie z informacjami o błędzie do określonego punktu końcowego.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Schemat powiadomień aplikacji portalu Azure Marketplace

Oto przykładowe powiadomienie dotyczące katalogu usług po pomyślnej aprowizacji wystąpienia aplikacji zarządzanej:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Jeśli Inicjowanie obsługi nie powiedzie się, zostanie wysłane powiadomienie z informacjami o błędzie do określonego punktu końcowego.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parametr | Opis
---|---
eventType | Typ zdarzenia, które wyzwoliło powiadomienie. (Na przykład PUT, PATCH, DELETE.)
applicationId | W pełni kwalifikowany identyfikator zasobu zarządzanej aplikacji, dla którego zostało wyzwolone powiadomienie.
eventTime | Sygnatura czasowa zdarzenia, które wyzwoliło powiadomienie. (Data i godzina w formacie UTC ISO 8601).
provisioningState | Stan aprowizacji wystąpienia aplikacji zarządzanej. (Na przykład pomyślne, Niepowodzenie, usunięcie, usunięcie.)
error | *Określany tylko wtedy, gdy provisioningState się nie powiodło*. Zawiera kod błędu, komunikat i szczegóły problemu, który spowodował awarię.
applicationDefinitionId | *Określony tylko dla aplikacji zarządzanych przez katalog usług*. Reprezentuje w pełni kwalifikowany identyfikator zasobu definicji aplikacji, dla którego Zainicjowano obsługę administracyjną wystąpienia aplikacji zarządzanej.
plan | *Określona tylko dla aplikacji zarządzanych przez portal Azure Marketplace*. Reprezentuje wydawcę, ofertę, jednostkę SKU i wersję wystąpienia aplikacji zarządzanej.
billingDetails | *Określona tylko dla aplikacji zarządzanych przez portal Azure Marketplace.* Szczegóły rozliczeń wystąpienia aplikacji zarządzanej. Zawiera resourceUsageId, którego można użyć do wysyłania zapytań do witryny Azure Marketplace w celu uzyskania szczegółów dotyczących użycia.

## <a name="endpoint-authentication"></a>Uwierzytelnianie punktu końcowego
Aby zabezpieczyć punkt końcowy elementu webhook i zapewnić autentyczność powiadomienia:
1. Podaj parametr zapytania na górze identyfikatora URI elementu webhook, np.: https \: //Your-Endpoint.com? SIG = GUID. W przypadku każdego powiadomienia Sprawdź, czy parametr zapytania `sig` ma oczekiwaną wartość `Guid` .
2. Wydaj element GET w wystąpieniu aplikacji zarządzanej przy użyciu aplikacji. Sprawdź, czy provisioningState pasuje do provisioningState powiadomienia, aby zapewnić spójność.

## <a name="notification-retries"></a>Ponowne próby powiadomień

Usługa powiadamiania aplikacji zarządzanej oczekuje `200 OK` odpowiedzi z punktu końcowego elementu webhook do powiadomienia. Usługa powiadamiania ponowi próbę, jeśli punkt końcowy elementu webhook zwróci kod błędu HTTP o wartości większej lub równej 500, jeśli zwróci kod błędu 429 lub gdy punkt końcowy jest tymczasowo nieosiągalny. Jeśli punkt końcowy elementu webhook nie stanie się dostępny w ciągu 10 godzin, komunikat powiadomienia zostanie porzucony i ponowne próby zakończą się.
