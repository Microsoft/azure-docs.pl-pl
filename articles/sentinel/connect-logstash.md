---
title: Łączenie źródeł danych za pomocą logstash z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak przekazywać dzienniki z zewnętrznych źródeł danych do platformy Azure, korzystając z logstash.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 79d29ef228fc27655da30edbeb64abcb01e45d5e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237178"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Korzystanie z logstash do łączenia źródeł danych z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Pozyskiwanie danych przy użyciu wtyczki wyjściowej logstash jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Korzystając z nowej wtyczki danych wyjściowych dla **aparatu zbierania danych logstash**, możesz teraz wysyłać dowolny typ dziennika za pośrednictwem usługi logstash bezpośrednio do obszaru roboczego log Analytics na platformie Azure. Dzienniki zostaną wysłane do tabeli niestandardowej, która zostanie zdefiniowana przy użyciu wtyczki wyjściowej.

Aby dowiedzieć się więcej o pracy z aparatem zbierania danych logstash, zobacz [wprowadzenie do logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Omówienie

### <a name="architecture-and-background"></a>Architektura i tło

   ![Architektura logstash](./media/connect-logstash/logstash-architecture.png)

Silnik logstash składa się z trzech składników:

- Wtyczki wejściowe — dostosowana kolekcja danych z różnych źródeł
- Filtrowanie wtyczek — manipulowanie i normalizowanie danych zgodnie z określonymi kryteriami
- Wtyczki wyjściowe — dostosowane wysyłanie zebranych i przetworzonych danych do różnych miejsc docelowych

> [!NOTE]
> Platforma Azure wskaźnikowa obsługuje tylko własne dostarczone wtyczki. Nie obsługuje wtyczek wyjściowych innych firm dla platformy Azure, ani innych wtyczek logstash dowolnego typu.

Wtyczka Azure wskaźnikowego danych wyjściowych dla logstash wysyła dane sformatowane w formacie JSON do obszaru roboczego Log Analytics przy użyciu Log Analytics interfejsu API REST modułu zbierającego dane HTTP. Dane są pozyskiwane w dziennikach niestandardowych.

- [Dowiedz się więcej o interfejsie API REST log Analytics](https://docs.microsoft.com/rest/api/loganalytics/create-request).
- [Dowiedz się więcej o dziennikach niestandardowych](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs).

## <a name="installing-and-configuring-the-azure-sentinel-output-plugin-in-logstash"></a>Instalowanie i Konfigurowanie wtyczki danych wyjściowych platformy Azure na platformie logstash

1. **Instalacja**

    Wtyczka danych wyjściowych na platformie Azure jest dostępna w kolekcji logstash.

    - Postępuj zgodnie z instrukcjami zawartymi w dokumencie logstash [Work with](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) plugins, aby zainstalować wtyczkę ***Microsoft-logstash-Output-Azure-loganalytics*** .
    - Jeśli system logstash nie ma dostępu do Internetu, postępuj zgodnie z instrukcjami zawartymi w dokumencie [Zarządzanie wtyczkami w trybie offline](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) logstash, aby przygotować pakiet wtyczek w trybie offline i korzystać z niego. (Będzie to wymagało skompilowania innego systemu logstash z dostępem do Internetu).

1. **Konfiguracja**

    Korzystając z informacji w strukturze logstash dokumentu [pliku konfiguracji](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) , Dodaj wtyczkę wyjściową platformy Azure do konfiguracji przy użyciu następujących kluczy i wartości (zobacz poprawną składnię pliku konfiguracji poniżej tabeli):

    | **Nazwa pola** | **Typ danych** | **Opis** |
    |----------------|---------------|-----------------|
    | `workspace_id` | ciąg | Wprowadź identyfikator GUID identyfikatora obszaru roboczego |
    | `workspace_key` | ciąg | Wprowadź identyfikator GUID klucza podstawowego obszaru roboczego |
    | `custom_log_table_name` | ciąg | Ustaw nazwę tabeli, w której zostaną pozyskane dzienniki. Można skonfigurować tylko jedną nazwę tabeli na wtyczkę wyjściową. Tabela dzienników zostanie wyświetlona w obszarze Azure, w obszarze **dzienniki** **, w tabeli** w kategorii **dzienniki niestandardowe** z `_CL` sufiksem. |
    | `endpoint` | ciąg | Pole opcjonalne. Domyślnie jest to Log Analytics punkt końcowy. To pole służy do ustawiania alternatywnego punktu końcowego. |
    | `time_generated_field` | ciąg | Pole opcjonalne. Ta właściwość zastępuje domyślne pole **TimeGenerated** w log Analytics. Wprowadź nazwę pola sygnatury czasowej w źródle danych. Dane w polu muszą być zgodne z formatem ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ) |
    | `key_names` | tablica | Wprowadź listę Log Analytics pól schematu danych wyjściowych. Każdy element listy powinien być ujęty w apostrofy i elementy oddzielone przecinkami, a cała lista ujęta w nawiasy kwadratowe. Zobacz przykład poniżej. |
    | `plugin_flush_interval` | liczba | Pole opcjonalne. Ustaw, aby określić maksymalny interwał (w sekundach) między transmisjami komunikatów do Log Analytics. Wartość domyślna to 5. |
    | `amount_resizing` | wartość logiczna | PRAWDA/FAŁSZ. Włącza lub wyłącza mechanizm skalowania automatycznego, który dostosowuje rozmiar buforu komunikatów zgodnie z odebraną ilością danych dziennika. |
    | `max_items` | liczba | Pole opcjonalne. Stosuje się tylko wtedy `amount_resizing` , gdy ustawiono wartość "false". Użyj, aby ustawić limit rozmiaru buforu wiadomości (w rekordach). Wartość domyślna to 2000.  |

    **Przykładowa konfiguracja**

        input {
            tcp {
                port => 514
                type => syslog
            }
        }

        filter {
            grok {
                match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
            }
        }

        output {
            logstash-output-azure-loganalytics {
                workspace_id => "<WS_ID>"
                workspace_key => "${WS_KEY}"
                custom_log_table_name => "logstashCustomTable"
                key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
                plugin_flush_interval => 5
            }
        } 

    > [!NOTE]
    > Aby dowiedzieć się więcej na temat jego wewnętrznych ustawień roboczych, konfiguracji i wydajności, odwiedź witrynę [GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) wtyczki.

1. **Uruchom ponownie logstash**

1. **Wyświetlanie dzienników przychodzących na platformie Azure — wskaźnik**

    1. Sprawdź, czy komunikaty są wysyłane do wtyczki wyjściowej.

    1. W menu nawigacji do usługi Azure wskaźnikowego kliknij pozycję **dzienniki**. W obszarze nagłówek **tabeli** rozwiń kategorię **dzienniki niestandardowe** . Znajdź i kliknij nazwę określonej tabeli (z `_CL` sufiksem) w konfiguracji.

        ![Logstash dzienniki niestandardowe](./media/connect-logstash/logstash-custom-logs-menu.png)

    1. Aby wyświetlić rekordy w tabeli, należy zbadać tabelę przy użyciu nazwy tabeli jako schematu.

        ![Zapytanie dotyczące dzienników niestandardowych logstash](./media/connect-logstash/logstash-custom-logs-query.png)

## <a name="monitor-output-plugin-audit-logs"></a>Monitoruj dzienniki inspekcji wtyczki wyjściowej

Aby monitorować łączność i działanie wtyczki danych wyjściowych platformy Azure, Włącz odpowiedni plik dziennika logstash. Zapoznaj się z dokumentem [układu katalogu logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) dla lokalizacji pliku dziennika.

Jeśli nie widzisz żadnych danych w tym pliku dziennika, wygeneruj i Wyślij pewne zdarzenia lokalnie (za pomocą wtyczek wejściowych i filtru), aby upewnić się, że wtyczka wyjściowa otrzymuje dane. Wskaźnik produktów platformy Azure będzie obsługiwał tylko problemy związane z wtyczką danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z programu logstash do łączenia zewnętrznych źródeł danych z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.