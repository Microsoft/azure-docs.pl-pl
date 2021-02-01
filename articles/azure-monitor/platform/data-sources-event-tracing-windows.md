---
title: Zbieranie zdarzeń śledzenia zdarzeń systemu Windows (ETW) dla dzienników Azure Monitor analizy
description: Dowiedz się, jak zbierać śledzenie zdarzeń systemu Windows (ETW) do analizy w dziennikach Azure Monitor.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223803"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Zbieranie zdarzeń śledzenia zdarzeń systemu Windows (ETW) dla dzienników Azure Monitor analizy

*Śledzenie zdarzeń systemu Windows (ETW)* zapewnia mechanizm instrumentacji aplikacji w trybie użytkownika i sterowników trybu jądra. Agent Log Analytics służy do [zbierania zdarzeń systemu Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events) , które są zapisywane do administracyjnych i operacyjnych [kanałów ETW](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype). Jednak czasami trzeba przechwycić i przeanalizować inne zdarzenia, takie jak te, które są zapisywane w kanale analitycznym.  

## <a name="event-flow"></a>Przepływ zdarzeń

Aby pomyślnie zbierać [zdarzenia ETW oparte na manifeście](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers) dla analizy w dziennikach Azure monitor, należy użyć [rozszerzenia diagnostyki Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) dla systemu Windows (funkcji wad). W tym scenariuszu rozszerzenie diagnostyki działa jako odbiorca ETW, pisząc zdarzenia do usługi Azure Storage (tabele) jako magazyn pośredni. W tym miejscu zostanie on zapisany w tabeli o nazwie **WADETWEventTable**. Log Analytics następnie zbiera dane tabeli z usługi Azure Storage, wyrażając je jako tabelę o nazwie **ETWEvent**.

![Przepływ zdarzeń](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Konfigurowanie zbierania dzienników ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Krok 1. lokalizowanie poprawnego dostawcy ETW

Użyj jednego z poniższych poleceń, aby wyliczyć dostawców ETW w źródłowym systemie Windows.

Wiersz polecenia:

```
logman query providers
```

Program PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Opcjonalnie możesz wybrać potok tego danych wyjściowych programu PowerShell do Out-Gridview w celu ułatwienia nawigacji.

Zapisz nazwę dostawcy ETW i identyfikator GUID, który jest wyrównany do dziennika analitycznego lub debugowania, który jest przedstawiony w Podgląd zdarzeń lub do modułu, dla którego zamierzasz zbierać dane zdarzenia.

### <a name="step-2-diagnostics-extension"></a>Krok 2. rozszerzenie diagnostyki

Upewnij się, że *rozszerzenie diagnostyki systemu Windows* jest [zainstalowane](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal) we wszystkich systemach źródłowych.

### <a name="step-3-configure-etw-log-collection"></a>Krok 3. Konfigurowanie zbierania dzienników ETW

1. Przejdź do bloku **ustawień diagnostycznych** maszyny wirtualnej

2. Wybierz kartę **dzienniki**

3. Przewiń w dół i Włącz zrzut ekranu opcji **zdarzenia śledzenia zdarzeń systemu Windows (ETW) dla** ![ ustawień diagnostyki](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Ustaw identyfikator GUID dostawcy lub klasę dostawcy na podstawie dostawcy, dla którego jest konfigurowana kolekcja

5. Ustaw [**poziom dziennika**](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session) zgodnie z potrzebami

6. Kliknij wielokropek obok dostarczonego dostawcy, a następnie kliknij pozycję **Konfiguruj** .

7. Upewnij się, że **Domyślna tabela docelowa** jest ustawiona na **etweventtable**

8. W razie potrzeby ustaw [**Filtr słów kluczowych**](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events)

9. Zapisz ustawienia dostawcy i dziennika

Po wygenerowaniu pasujących zdarzeń należy zacząć wyświetlać zdarzenia ETW, które pojawiają się w tabeli **WADetweventtable** w usłudze Azure Storage. Aby to potwierdzić, możesz użyć Eksplorator usługi Azure Storage.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Krok 4. Konfigurowanie kolekcji kont magazynu Log Analytics

Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) , aby zebrać dzienniki z usługi Azure Storage. Po skonfigurowaniu dane zdarzenia ETW powinny pojawić się w Log Analytics w tabeli **ETWEvent** .

## <a name="next-steps"></a>Następne kroki
- Używanie [pól niestandardowych](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields) do tworzenia struktury w ZDARZENIAch ETW
- Informacje na temat [zapytań dzienników](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
