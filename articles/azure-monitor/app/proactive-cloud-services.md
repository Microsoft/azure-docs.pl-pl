---
title: Alerty dotyczące problemów z usługą Azure Cloud Services przy użyciu integracji Diagnostyka Azure z platformą Azure Application Insights | Microsoft Docs
description: Monitoruj problemy, takie jak awarie uruchamiania, awarie i pętle odtwarzania ról w usłudze Azure Cloud Services z platformą Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309294"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerty dotyczące problemów z usługą Azure Cloud Services przy użyciu integracji diagnostyki platformy Azure z platformą Azure Application Insights

W tym artykule opisano sposób konfigurowania reguł alertów, które monitorują pod kątem problemów, takich jak błędy uruchomienia, awarie i pętle odtwarzania roli w usłudze Azure Cloud Services (role sieci Web i proces roboczy).

Metoda opisana w tym artykule jest oparta na [Diagnostyka Azure integracji z Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)i ostatnio wydane [alerty dziennika dla możliwości Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) .

## <a name="define-a-base-query"></a>Zdefiniuj zapytanie podstawowe

Aby rozpocząć, zdefiniujemy podstawowe zapytanie, które pobiera zdarzenia dziennika zdarzeń systemu Windows z kanału systemu Windows Azure, które są przechwytywane do Application Insights jako rekordy śledzenia.
Te rekordy mogą służyć do wykrywania różnych problemów na platformie Azure Cloud Services, takich jak awarie uruchamiania, awarie środowiska uruchomieniowego i pętle odtwarzania.

> [!NOTE]
> Poniższe zapytanie podstawowe sprawdza, czy występują problemy w przedziale czasu 30 minut, i zakłada opóźnienia 10 minut podczas pozyskiwania rekordów telemetrii. Te ustawienia domyślne można skonfigurować zgodnie z potrzebami.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Sprawdzanie określonych identyfikatorów zdarzeń

Po pobraniu zdarzeń dziennika zdarzeń systemu Windows można wykryć konkretne problemy, sprawdzając odpowiednie identyfikatory zdarzeń i właściwości komunikatów (Zobacz przykłady poniżej).
Wystarczy połączyć bazowe zapytanie powyżej z jednym z poniższych zapytań i używało tego połączonego zapytania podczas definiowania reguły alertu dziennika.

> [!NOTE]
> W poniższych przykładach zostanie wykryty problem, jeśli podczas przeanalizowanego przedziału czasu zostanie znalezionych więcej niż trzy zdarzenia. Tę wartość domyślną można skonfigurować w celu zmiany czułości reguły alertu.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Utworzenie alertu

W menu nawigacji w ramach zasobu Application Insights przejdź do pozycji **alerty**, a następnie wybierz pozycję **Nowa reguła alertu**.

![Zrzut ekranu przedstawiający tworzenie reguły](./media/proactive-cloud-services/001.png)

W oknie **Tworzenie reguły** w sekcji **Zdefiniuj warunek alertu** kliknij pozycję **Dodaj kryteria**, a następnie wybierz pozycję Wyszukiwanie w **dzienniku niestandardowym**.

![Zrzut ekranu przedstawiający kryteria definiowania warunków dla alertu](./media/proactive-cloud-services/002.png)

W polu **zapytania wyszukiwania** wklej połączone zapytanie przygotowane w poprzednim kroku.

Następnie przejdź do pola **PROG** i ustaw jego wartość na 0. Opcjonalnie możesz dostosować **pola** **okresów** i częstotliwości.
Kliknij przycisk **Gotowe**.

![Zrzut ekranu przedstawiający zapytanie dotyczące konfiguracji logiki sygnałów](./media/proactive-cloud-services/003.png)

W sekcji **Zdefiniuj szczegóły alertu** Podaj **nazwę** i **Opis** reguły alertu, a następnie ustaw jej **ważność**.
Upewnij się również, że przycisk **Włącz regułę przy tworzeniu** ma wartość **tak**.

![Szczegóły alertu zrzutu ekranu](./media/proactive-cloud-services/004.png)

W sekcji **Zdefiniuj grupę akcji** możesz wybrać istniejącą **grupę akcji** lub utworzyć nową.
Można zdecydować, że grupa akcji będzie zawierać wiele akcji różnych typów.

![Grupa akcji zrzutu ekranu](./media/proactive-cloud-services/005.png)

Po zdefiniowaniu grupy akcji potwierdź zmiany i kliknij przycisk **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

[Anomalie błędów](./proactive-failure-diagnostics.md) 
 [Przecieki pamięci](./proactive-potential-memory-leak.md) 
 [Anomalie wydajności](./proactive-performance-diagnostics.md)

