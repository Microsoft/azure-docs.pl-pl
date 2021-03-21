---
title: Dzienniki diagnostyki Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera omówienie wszystkich dzienników operacyjnych i diagnostycznych, które są dostępne dla Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585268"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Włączanie dzienników diagnostycznych dla Service Bus

Po rozpoczęciu korzystania z przestrzeni nazw Azure Service Bus można monitorować sposób i czas tworzenia, usuwania lub uzyskiwania dostępu do przestrzeni nazw. Ten artykuł zawiera omówienie wszystkich dostępnych dzienników operacyjnych i diagnostycznych.

Azure Service Bus obecnie obsługuje dzienniki aktywności i działania, które przechwytują *operacje zarządzania* wykonywane w przestrzeni nazw Azure Service Bus. W szczególności te dzienniki przechwytują typ operacji, w tym Tworzenie kolejki, używane zasoby i stan operacji.

## <a name="operational-logs-schema"></a>Schemat dzienników operacyjnych

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON) w następujących dwóch lokalizacjach:

- **Azure**: wyświetla dzienniki z operacji i akcji, które są wykonywane względem przestrzeni nazw w Azure Portal lub wdrożenia szablonów Azure Resource Manager.
- **AzureDiagnostics**: wyświetla dzienniki operacji i akcji, które są wykonywane względem przestrzeni nazw za pomocą interfejsu API, lub przez klientów zarządzania w zestawie SDK języka.

Ciągi JSON dziennika operacyjnego zawierają elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ------- | ------- |
| ActivityId | Wewnętrzny identyfikator używany do identyfikowania określonego działania |
| EventName | Nazwa operacji |
| ResourceId | Identyfikator zasobu Azure Resource Manager |
| SubscriptionId | Identyfikator subskrypcji |
| EventTimeString | Czas operacji |
| EventProperties | Właściwości operacji |
| Stan | Stan operacji |
| Obiekt wywołujący | Obiekt wywołujący operacji (Azure Portal lub klient zarządzania) |
| Kategoria | OperationalLogs |

Oto przykład ciągu JSON dziennika operacyjnego:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Zdarzenia i operacje przechwycone w dziennikach operacyjnych

Dzienniki operacyjne przechwytują wszystkie operacje zarządzania, które są wykonywane w przestrzeni nazw Azure Service Bus. Operacje na danych nie są przechwytywane z powodu dużej ilości operacji na danych, które są wykonywane w Azure Service Bus.

> [!NOTE]
> Aby ułatwić lepsze śledzenie operacji na danych, zalecamy korzystanie z funkcji śledzenia po stronie klienta.

Następujące operacje zarządzania są przechwytywane w dziennikach operacyjnych: 

| Zakres | Operacja|
|-------| -------- |
| Przestrzeń nazw | <ul> <li> Utwórz przestrzeń nazw</li> <li> Aktualizowanie przestrzeni nazw </li> <li> Usuń przestrzeń nazw </li> <li> Aktualizowanie zasad SharedAccess obszaru nazw </li> </ul> | 
| Kolejka | <ul> <li> Utwórz kolejkę</li> <li> Aktualizuj kolejkę</li> <li> Usuń kolejkę </li> <li> Autousuwanie kolejki usuwania </li> </ul> | 
| Temat | <ul> <li> Utwórz temat </li> <li> Aktualizowanie tematu </li> <li> Usuń temat </li> <li> Usuwanie tematu usuwania </li> </ul> |
| Subskrypcja | <ul> <li> Tworzenie subskrypcji </li> <li> Aktualizowanie subskrypcji </li> <li> Usuń subskrypcję </li> <li> Usuń autosubskrypcję usuwania </li> </ul> |

> [!NOTE]
> Obecnie operacje *odczytu* nie są śledzone w dziennikach operacyjnych.

## <a name="enable-operational-logs"></a>Włączanie dzienników operacyjnych

Dzienniki operacyjne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do przestrzeni nazw Azure Service Bus, a następnie w obszarze **monitorowanie** wybierz pozycję  **Ustawienia diagnostyczne**.

   ![Łącze "Ustawienia diagnostyczne"](./media/service-bus-diagnostic-logs/image1.png)

1. W okienku **Ustawienia diagnostyki** wybierz pozycję **Dodaj ustawienie diagnostyczne**.  

   ![Link "Dodaj ustawienie diagnostyczne"](./media/service-bus-diagnostic-logs/image2.png)

1. Skonfiguruj ustawienia diagnostyki, wykonując następujące czynności:

   a. W polu **Nazwa** wprowadź nazwę ustawień diagnostycznych.  

   b. Wybierz jeden z następujących trzech miejsc docelowych dla dzienników diagnostycznych:  
   - W przypadku wybrania opcji **Archiwizuj na koncie magazynu** należy skonfigurować konto magazynu, na którym będą przechowywane dzienniki diagnostyczne.  
   - W przypadku wybrania **strumienia do centrum zdarzeń** należy skonfigurować centrum zdarzeń, do którego mają być przesyłane strumieniowo dzienniki diagnostyczne.
   - W przypadku wybrania opcji **Wyślij do log Analytics** należy określić wystąpienie log Analytics, do którego zostanie wysłana Diagnostyka.  

   c. Zaznacz pole wyboru **OperationalLogs** .

    ![Okienko "Ustawienia diagnostyki"](./media/service-bus-diagnostic-logs/image3.png)

1. Wybierz pozycję **Zapisz**.

Nowe ustawienia zaczną obowiązywać od około 10 minut. Dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w okienku **dzienniki diagnostyczne** .

Więcej informacji o konfigurowaniu ustawień diagnostycznych znajduje się w temacie [Omówienie dzienników diagnostyki platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus, zobacz:

* [Wprowadzenie do Service Bus](service-bus-messaging-overview.md)
* [Rozpoczynanie pracy z usługą Service Bus](service-bus-dotnet-get-started-with-queues.md)