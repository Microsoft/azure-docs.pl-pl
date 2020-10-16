---
title: Dzienniki usług komunikacyjnych
titleSuffix: An Azure Communication Services concept document
description: Informacje o rejestrowaniu w usłudze Azure Communications Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: aad4cdfe38ee9dd7530cb8ebe21cded18cb0a1ec
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128690"
---
# <a name="communication-services-logs"></a>Dzienniki usług komunikacyjnych

Usługi komunikacyjne platformy Azure oferują funkcje rejestrowania, których można używać do monitorowania i debugowania rozwiązania usług komunikacyjnych. Te możliwości można skonfigurować za pomocą Azure Portal.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Włączanie dzienników diagnostycznych w Twoim zasobie

Rejestrowanie jest domyślnie wyłączone podczas tworzenia zasobu. Aby włączyć rejestrowanie, przejdź do bloku **Ustawienia diagnostyczne** w menu zasób w sekcji **monitorowanie** . Następnie kliknij pozycję **Dodaj ustawienie diagnostyczne**.

Następnie wybierz żądany cel archiwum. Obecnie obsługujemy konta magazynu i Log Analytics jako elementy docelowe archiwum. Po wybraniu typów dzienników, które chcesz przechwycić, Zapisz ustawienia diagnostyczne.
 
Nowe ustawienia zaczną obowiązywać od około 10 minut. Dzienniki będą wyświetlane w skonfigurowanym miejscu docelowym Archiwum w okienku dzienniki zasobu usług komunikacyjnych.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Opcje ustawień diagnostycznych ACS.":::

Więcej informacji o konfigurowaniu diagnostyki znajduje się w temacie Omówienie [dzienników zasobów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview).

## <a name="resource-log-categories"></a>Kategorie dzienników zasobów

Usługi komunikacyjne oferują trzy typy dzienników, które można włączyć:

* **Dzienniki użycia** — udostępnia dane użycia skojarzone z każdą rozliczną ofertą usługi
* **Dzienniki operacyjne rozmowy** — zawiera podstawowe informacje dotyczące usługi czatu
* **Dzienniki operacyjne programu SMS** — zawiera podstawowe informacje dotyczące usługi programu SMS

### <a name="usage-logs-schema"></a>Schemat dzienników użycia

| Właściwość | Opis |
| -------- | ---------------|
| Timestamp | Sygnatura czasowa (UTC) wygenerowania dziennika. |
| Nazwa operacji | Operacja skojarzona z rekordem dziennika. |
| Wersja operacji | `api-version`Skojarzona z operacją, jeśli operacjaname została wykonana przy użyciu interfejsu API. Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| Kategoria | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. |
| Identyfikator korelacji | Identyfikator dla zdarzeń skorelowanych. Może służyć do identyfikowania skorelowanych zdarzeń między wieloma tabelami. |
| Właściwości | Inne dane mające zastosowanie do różnych trybów usług komunikacyjnych. |
| Identyfikator rekordu | Unikatowy identyfikator danego rekordu użycia. |
| Typ użycia | Tryb użycia. (na przykład rozmowy, PSTN, NAT itp.) |
| Typ jednostki | Typ jednostki, która jest oparta na wykorzystaniu dla danego trybu użycia. (na przykład minuty, megabajty, wiadomości itp.). |
| Liczba | Liczba jednostek używanych lub zużytych dla tego rekordu. |

### <a name="chat-operational-logs"></a>Dzienniki operacyjne rozmowy

| Właściwość | Opis |
| -------- | ---------------|
| TimeGenerated | Sygnatura czasowa (UTC) wygenerowania dziennika. |
| OperationName | Operacja skojarzona z rekordem dziennika. |
| Korelacj | Identyfikator dla zdarzeń skorelowanych. Może służyć do identyfikowania skorelowanych zdarzeń między wieloma tabelami. |
| OperationVersion | Wersja interfejsu API skojarzona z operacją, jeśli OperationName została wykonana przy użyciu interfejsu API. Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| Kategoria | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. |
| ResultType | Stan operacji. |
| ResultSignature | Stan podrzędny operacji. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, to pole jest kodem stanu HTTP odpowiedniego wywołania REST. |
| ResultDescription | Opis tekstu statycznego tej operacji. |
| DurationMs | Czas trwania operacji w milisekundach. |
| CallerIpAddress | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| Poziom | Poziom ważności zdarzenia. |
| URI | Identyfikator URI żądania. |
| UserId | Identyfikator użytkownika nadawcy żądania. |
| ChatThreadId | Identyfikator wątku rozmowy skojarzony z żądaniem. |
| ChatMessageId | Identyfikator komunikatu rozmowy skojarzonej z żądaniem. |
| SdkType | Typ zestawu SDK używany w żądaniu. |
| PlatformType | Typ platformy używany w żądaniu. |

### <a name="sms-operational-logs"></a>Dzienniki operacyjne programu SMS

| Właściwość | Opis |
| -------- | ---------------|
| TimeGenerated | Sygnatura czasowa (UTC) wygenerowania dziennika. |
| OperationName | Operacja skojarzona z rekordem dziennika. |
| Korelacj | Identyfikator dla zdarzeń skorelowanych. Może służyć do identyfikowania skorelowanych zdarzeń między wieloma tabelami. |
| OperationVersion | Wersja interfejsu API skojarzona z operacją, jeśli OperationName została wykonana przy użyciu interfejsu API. Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| Kategoria | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. |
| ResultType | Stan operacji. |
| ResultSignature | Stan podrzędny operacji. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, to pole jest kodem stanu HTTP odpowiedniego wywołania REST. |
| ResultDescription | Opis tekstu statycznego tej operacji. |
| DurationMs | Czas trwania operacji w milisekundach. |
| CallerIpAddress | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| Poziom | Poziom ważności zdarzenia. |
| URI | Identyfikator URI żądania. |
| OutgoingMessageLength | Liczba znaków w komunikacie wychodzącym. |
| IncomingMessageLength | Liczba znaków w komunikacie przychodzącym. |
| DeliveryAttempts | Liczba prób dostarczenia tej wiadomości. |
| PhoneNumber | Numer telefonu, na który jest wysyłany wiadomość SMS. |
| SdkType | Typ zestawu SDK używany w żądaniu. |
| PlatformType | Typ platformy używany w żądaniu. |
| Metoda | Metoda używana w żądaniu. |
