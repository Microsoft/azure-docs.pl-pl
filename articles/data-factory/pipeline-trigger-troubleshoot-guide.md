---
title: Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory
description: Użyj różnych metod rozwiązywania problemów z wyzwalaczem potoku w Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589172"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uruchomienie potoku w bieżącej wersji usługi Azure Data Factory definiuje wystąpienie wykonania potoku. Na przykład masz potok, który jest wykonywany o 8:00 AM, 9:00 AM i 10:00 AM. W takim przypadku istnieją trzy osobne uruchomienia potoku lub przebiegów potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Identyfikator uruchomienia jest identyfikatorem GUID (unikatowym identyfikatorem globalnym), który definiuje określony przebieg potoku.

Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które definiujesz w potoku. Potok możesz wykonywać ręcznie albo za pomocą wyzwalacza. Aby uzyskać szczegółowe informacje [, zobacz wykonywanie potoku i Wyzwalacze w Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Typowe problemy, przyczyny i rozwiązania

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Potok z funkcją platformy Azure zgłasza błąd z prywatnym połączeniem punktu końcowego
 
#### <a name="issue"></a>Problem
W przypadku pewnego kontekstu masz Data Factory i aplikacja funkcji na platformie Azure uruchomione w prywatnym punkcie końcowym. Podjęto próbę uzyskania potoku, który współdziała z aplikacja funkcjiem platformy Azure. Wypróbowano trzy różne metody, ale jeden z nich zwraca błąd `Bad Request` , pozostałe dwie metody zwracają `103 Error Forbidden` .

#### <a name="cause"></a>Przyczyna 
Data Factory obecnie nie obsługuje łącznika prywatnego punktu końcowego dla usługi Azure aplikacja funkcji. Powinna to być powód, dla którego usługa Azure aplikacja funkcji odrzuca wywołania, ponieważ byłyby skonfigurowane do zezwalania tylko na połączenia z prywatnego linku.

#### <a name="resolution"></a>Rozwiązanie
Można utworzyć prywatny punkt końcowy typu **PrivateLinkService** i udostępnić system DNS aplikacji funkcji, a połączenie powinno działać.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>Uruchomienie potoku zostało przerwane, ale monitor nadal pokazuje stan postępu

#### <a name="issue"></a>Problem
Często w przypadku kasowania uruchomienia potoku monitorowanie potoku nadal pokazuje stan postępu. Dzieje się tak z powodu problemu z pamięcią podręczną w przeglądarce i nie masz odpowiednich filtrów do monitorowania.

#### <a name="resolution"></a>Rozwiązanie
Odśwież przeglądarkę i zastosuj odpowiednie filtry do monitorowania.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Błąd potoku kopiowania — znaleziono więcej kolumn niż liczba oczekiwanych kolumn (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problem  
Jeśli pliki w określonym folderze kopiowanym zawierają pliki o różnych schematach, takich jak zmienna liczba kolumn, różne ograniczniki, Ustawienia znaków cudzysłowu lub problemy z danymi, potok Data Factory zostanie uruchomiony w ramach tego błędu:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Rozwiązanie
Podczas tworzenia działania Kopiowanie danych wybierz opcję "kopia binarna". W ten sposób w przypadku kopiowania zbiorczego lub migrowania danych z jednego Data Lake do innego, przy użyciu opcji **Binary** , Data Factory nie będzie otwierać plików w celu odczytywania schematu, ale po prostu Traktuj każdy plik jako binarny i skopiuj je do innej lokalizacji.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>Uruchomienie potoku kończy się niepowodzeniem, gdy zostanie osiągnięty limit pojemności środowiska Integration Runtime

#### <a name="issue"></a>Problem
Komunikat o błędzie:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

Ten błąd wskazuje na ograniczenie środowiska Integration Runtime, które jest obecnie 50. Aby uzyskać szczegółowe informacje, zobacz [limity](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Jeśli wykonujesz dużą ilość przepływu danych przy użyciu tego samego środowiska Integration Runtime w tym samym czasie, może to spowodować wystąpienie błędu.

#### <a name="resolution"></a>Rozwiązanie 
- Oddziel te potoki dla różnych czasów wyzwalacza do wykonania.
- Utwórz nowe środowisko Integration Runtime i Podziel te potoki na wiele środowisk Integration Runtime.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Jak monitorować błędy potoków w regularnych odstępach czasu

#### <a name="issue"></a>Problem
Często istnieje potrzeba monitorowania Data Factory potoków w odstępach czasu, powiedzmy 5 minut. Można wykonywać zapytania i filtrować uruchomienia potoku z fabryki danych przy użyciu punktu końcowego. 

#### <a name="recommendation"></a>Zalecenie
1. Skonfiguruj aplikację logiki platformy Azure, aby wykonywać zapytania dotyczące wszystkich zakończonych niepowodzeniem potoków co 5 minut.
2. Następnie można zgłosić incydenty do naszego systemu biletów na [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Dokumentacja
- [Powiadomienia zewnętrzne wysyłane z Data Factory](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Jak obsłużyć błędy i błędy na poziomie działania w potokach

#### <a name="issue"></a>Problem
Azure Data Factory aranżacji umożliwia logikę warunkową i umożliwia użytkownikowi korzystanie z różnych ścieżek w oparciu o wyniki poprzedniej aktywności. Zezwala na cztery ścieżki warunkowe: "po powodzeniu (domyślne przejście)", "po niepowodzeniu", "po ukończeniu" i "przy pominięciu". Dozwolone są różne ścieżki.

Azure Data Factory definiuje powodzenie przebiegu potoku i Niepowodzenie w następujący sposób:

- Oceń wyniki wszystkich działań na poziomie liścia. Jeśli działanie liścia zostało pominięte, ocenimy jego działanie nadrzędne.
- Wynik potoku jest zakończony pomyślnie, jeśli tylko w przypadku pomyślnego zakończenia.

#### <a name="recommendation"></a>Zalecenie
- Zaimplementuj operacje sprawdzania poziomu działania po [dojściu do niepowodzeń i błędów potoku](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Za pomocą aplikacji logiki platformy Azure można monitorować potoki w regularnych odstępach czasu [, wykonując zapytania przez fabrykę]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)