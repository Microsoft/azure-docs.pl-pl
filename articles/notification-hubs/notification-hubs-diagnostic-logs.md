---
title: Dzienniki diagnostyki usługi Azure Notification Hubs | Microsoft Docs
description: Ten artykuł zawiera omówienie wszystkich dzienników operacyjnych i diagnostycznych, które są dostępne dla usługi Azure Notification Hubs.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736240"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Włączanie dzienników diagnostycznych dla Notification Hubs

Po rozpoczęciu korzystania z przestrzeni nazw usługi Azure Notification Hubs można monitorować sposób i czas tworzenia, usuwania lub uzyskiwania dostępu do przestrzeni nazw. Ten artykuł zawiera omówienie wszystkich dostępnych dzienników operacyjnych i diagnostycznych.

Usługa Azure Notification Hubs obecnie obsługuje dzienniki aktywności i działania, które przechwytują *operacje zarządzania* wykonywane w przestrzeni nazw usługi Azure Notification Hubs.

## <a name="diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON) w następujących dwóch lokalizacjach:

- **Azure**: wyświetla dzienniki z operacji i akcji, które są wykonywane względem przestrzeni nazw w Azure Portal lub wdrożenia szablonów Azure Resource Manager.
- **AzureDiagnostics**: wyświetla dzienniki operacji i akcji, które są wykonywane względem przestrzeni nazw za pomocą interfejsu API, lub przez klientów zarządzania w zestawie SDK języka.

Ciągi JSON dzienników diagnostycznych zawierają elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ------- | ------- |
| time | Sygnatura czasowa UTC dziennika |
| resourceId | Ścieżka względna do zasobu platformy Azure |
| operationName | Nazwa operacji zarządzania |
| category | Kategoria dziennika. Prawidłowe wartości: `OperationalLogs` |
| callerIdentity | Tożsamość obiektu wywołującego, który zainicjował operację zarządzania |
| resultType | Stan operacji zarządzania. Prawidłowe wartości: `Succeeded` lub `Failed` |
| resultDescription | Opis operacji zarządzania |
| correlationId | Identyfikator korelacji operacji zarządzania (Jeśli zostanie określony) |
| callerIpAddress | Adres IP obiektu wywołującego. Puste dla wywołań, które pochodzą z Azure Portal |

Oto przykład ciągu JSON dziennika operacyjnego:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

`callerIdentity`Pole może być puste lub CIĄGIEM JSON z jednym z następujących formatów.

W przypadku wywołań pochodzących z Azure Portal `identity` pole jest puste. Dziennik może być skorelowany z dziennikami aktywności, aby określić zalogowanego użytkownika.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

W przypadku wywołań realizowanych za pomocą Azure Resource Manager `identity` pola będzie zawierać nazwę użytkownika zalogowanego użytkownika.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

W przypadku wywołań interfejsu API REST Notification Hubs `identity` pole będzie zawierać nazwę zasad dostępu użytych do wygenerowania tokenu SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Zdarzenia i operacje przechwycone w dziennikach operacyjnych

Dzienniki operacyjne przechwytują wszystkie operacje zarządzania, które są wykonywane w przestrzeni nazw usługi Azure Notification Hubs. Operacje na danych nie są przechwytywane z powodu dużej ilości operacji na danych, które są wykonywane na platformie Azure Notification Hubs.

Następujące operacje zarządzania są przechwytywane w dziennikach operacyjnych: 

| Zakres | Nazwa operacji | Opis operacji |
| :-- | :-- | :-- |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Action | Wyświetl listę reguł autoryzacji |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Delete | Usuń regułę autoryzacji |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/ListKeys/Action | Klucze list |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Read | Pobierz regułę autoryzacji |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/regenerateKeys/Action | Ponowne generowanie kluczy |
| Przestrzeń nazw | Microsoft. NotificationHubs/Namespaces/reguł autoryzacji/Write | Utwórz lub zaktualizuj regułę autoryzacji |
| Przestrzeń nazw | Microsoft. NotificationHubs/przestrzenie nazw/usuwanie | Usuń przestrzeń nazw |
| Przestrzeń nazw | Microsoft. NotificationHubs/przestrzenie nazw/odczyt | Pobierz przestrzeń nazw |
| Przestrzeń nazw | Microsoft. NotificationHubs/przestrzenie nazw/zapis | Utwórz lub zaktualizuj przestrzeń nazw |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Action | Wyświetl listę reguł autoryzacji |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Delete | Usuń regułę autoryzacji |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/ListKeys/Action | Klucze list |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Read | Odczytaj regułę autoryzacji |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/regenerateKeys/Action | Ponowne generowanie kluczy |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji/Write | Utwórz lub zaktualizuj regułę autoryzacji |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Delete | Usuwanie centrum powiadomień |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/Action | Tworzenie, aktualizowanie lub uzyskiwanie poświadczeń PNS |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Read | Pobierz centrum powiadomień |
| Centrum powiadomień | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Write | Utwórz lub zaktualizuj centrum powiadomień |

## <a name="enable-operational-logs"></a>Włączanie dzienników operacyjnych

Dzienniki operacyjne są domyślnie wyłączone. Aby włączyć dzienniki, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do przestrzeni nazw usługi Azure Notification Hubs, a następnie w obszarze **monitorowanie** wybierz pozycję  **Ustawienia diagnostyczne**.

   ![Łącze "Ustawienia diagnostyczne"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. W okienku **Ustawienia diagnostyki** wybierz pozycję **Dodaj ustawienie diagnostyczne**.  

   ![Link "Dodaj ustawienie diagnostyczne"](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Skonfiguruj ustawienia diagnostyki, wykonując następujące czynności:

   a. W polu **Nazwa** wprowadź nazwę ustawień diagnostycznych.  

   b. Wybierz jeden z następujących trzech miejsc docelowych dla dzienników diagnostycznych:  
   - W przypadku wybrania opcji **Wyślij do log Analytics obszaru roboczego** należy określić wystąpienie log Analytics, do którego zostanie wysłana Diagnostyka.  
   - W przypadku wybrania opcji **Archiwizuj na koncie magazynu** należy skonfigurować konto magazynu, na którym będą przechowywane dzienniki diagnostyczne.  
   - W przypadku wybrania **strumienia do centrum zdarzeń** należy skonfigurować centrum zdarzeń, do którego mają być przesyłane strumieniowo dzienniki diagnostyczne.

   c. Zaznacz pole wyboru **OperationalLogs** .

    ![Okienko "Ustawienia diagnostyki"](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Wybierz pozycję **Zapisz**.

Nowe ustawienia zaczną obowiązywać od około 10 minut. Dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w okienku **dzienniki diagnostyczne** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu ustawień diagnostycznych, zobacz:
* [Omówienie dzienników diagnostyki platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

Aby dowiedzieć się więcej o usłudze Azure Notification Hubs, zobacz:
* [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)