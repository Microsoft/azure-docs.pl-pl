---
title: Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory
description: Użyj różnych metod rozwiązywania problemów z wyzwalaczem potoku w Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 1a5f665627da1b08ec57b04863a58f227c673af4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944907"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uruchomienie potoku w bieżącej wersji usługi Azure Data Factory definiuje wystąpienie wykonania potoku. Załóżmy na przykład, że masz potok, który działa o godzinie 8:00, 9:00 AM i 10:00 AM. W takim przypadku istnieją trzy oddzielne uruchomienia potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Identyfikator uruchomienia jest unikatowym identyfikatorem globalnym (GUID), który definiuje określony przebieg potoku.

Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które definiujesz w potoku. Potok można uruchomić ręcznie lub przy użyciu wyzwalacza. Aby uzyskać szczegółowe informacje [, zobacz wykonywanie potoku i Wyzwalacze w Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Typowe problemy, przyczyny i rozwiązania

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Potok aplikacji Azure Functions zgłasza błąd z łącznością z prywatnym punktem końcowym
 
Masz Data Factory i aplikację funkcji platformy Azure uruchomioną w prywatnym punkcie końcowym. Próbujesz uruchomić potok, który współdziała z aplikacją funkcji. Wypróbowano trzy różne metody, ale jeden zwraca błąd "złe żądanie", a pozostałe dwie metody zwracają "103 błąd zabroniony".

**Przyczyna**: obecnie Data Factory nie obsługuje prywatnego łącznika punktu końcowego dla aplikacji funkcji. Azure Functions odrzuca wywołania, ponieważ jest ono skonfigurowane do zezwalania tylko na połączenia z prywatnego linku.

**Rozwiązanie**: Tworzenie punktu końcowego **PrivateLinkService** i udostępnianie systemu DNS aplikacji funkcji.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Uruchomienie potoku zostało anulowane, ale monitor nadal pokazuje stan postępu

Po anulowaniu uruchomienia potoku monitorowanie potoku często nadal pokazuje stan postępu. Dzieje się tak z powodu problemu z pamięcią podręczną przeglądarki. Mogą również nie mieć prawidłowych filtrów monitorowania.

**Rozwiązanie**: Odśwież przeglądarkę i Zastosuj poprawne filtry monitorowania.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Podczas kopiowania potoku pojawia się błąd "DelimitedTextMoreColumnsThanDefined"
 
Jeśli kopiowany folder zawiera pliki z różnymi schematami, takimi jak zmienna liczba kolumn, różne ograniczniki, Ustawienia znaków cudzysłowu lub niektóre problemy z danymi, potok Data Factory może zgłosić ten błąd:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Rozwiązanie**: wybierz opcję **kopiowania binarnego** podczas tworzenia działania kopiowania. W ten sposób, w przypadku kopiowania zbiorczego lub migrowania danych z jednej usługi Data Lake do innej, Data Factory nie będzie można otworzyć plików, aby odczytać schemat. Zamiast tego Data Factory będzie traktować każdy plik jako binarny i skopiować go do innej lokalizacji.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>Uruchomienie potoku kończy się niepowodzeniem po osiągnięciu limitu pojemności środowiska Integration Runtime

Komunikat o błędzie:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Przyczyna**: osiągnięto limit pojemności środowiska Integration Runtime. Może być uruchomiona duża ilość przepływu danych przy użyciu tego samego środowiska Integration Runtime w tym samym czasie. Aby uzyskać szczegółowe informacje [, zobacz limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Rozwiązanie**:
 
- Uruchamianie potoków o różnych porach wyzwalacza.
- Utwórz nowe środowisko Integration Runtime i Podziel potoki na wiele środowisk Integration Runtime.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Występują błędy i błędy na poziomie aktywności w potokach

Azure Data Factory aranżacji umożliwia logikę warunkową i umożliwia użytkownikom korzystanie z różnych ścieżek na podstawie wyniku poprzedniej aktywności. Umożliwia ona cztery ścieżki warunkowe: **po powodzeniu** (domyślny przebieg), **po awarii**, **po zakończeniu** i **po pominięciu**. 

Azure Data Factory oblicza wynik wszystkich działań na poziomie liścia. Wyniki potoku są pomyślne tylko wtedy, gdy wszystkie opuszczenia zostały zakończone pomyślnie. Jeśli działanie liścia zostało pominięte, ocenimy jego działanie nadrzędne. 

**Rozwiązanie**

1. Zaimplementuj kontrole na poziomie działania, wykonując następujące czynności [, jak obsługiwać awarie i błędy potoków](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. Użyj Azure Logic Apps, aby monitorować potoki w regularnych odstępach czasu [, wykonując zapytania według fabryki](/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>Monitoruj błędy potoku w regularnych odstępach czasu

Może być konieczne monitorowanie niezakończonych potoków Data Factory w interwałach, powiedzmy 5 minut. Można wykonywać zapytania i filtrować uruchomienia potoku z fabryki danych przy użyciu punktu końcowego. 

Skonfiguruj aplikację logiki platformy Azure, aby wykonywać zapytania dotyczące wszystkich zakończonych niepowodzeniem potoków co 5 minut, zgodnie z opisem w temacie [zapytanie według fabryki](/rest/api/datafactory/pipelineruns/querybyfactory). Następnie możesz zgłosić zdarzenia do naszego systemu biletów.

Aby uzyskać więcej informacji, przejdź do [strony wysyłanie powiadomień z Data Factory, część 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)