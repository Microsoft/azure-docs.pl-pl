---
title: Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory
description: Użyj różnych metod rozwiązywania problemów z wyzwalaczem potoku w Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 72f2a5eec25b9acc2aedd7b006fe3380141781c8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563416"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Rozwiązywanie problemów z aranżacją i wyzwalaczami potoku w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uruchomienie potoku w bieżącej wersji usługi Azure Data Factory definiuje wystąpienie wykonania potoku. Załóżmy na przykład, że masz potok, który działa o godzinie 8:00, 9:00 AM i 10:00 AM. W takim przypadku istnieją trzy oddzielne uruchomienia potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Identyfikator uruchomienia jest unikatowym identyfikatorem globalnym (GUID), który definiuje określony przebieg potoku.

Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które definiujesz w potoku. Potok można uruchomić ręcznie lub przy użyciu wyzwalacza. Aby uzyskać szczegółowe informacje [, zobacz wykonywanie potoku i Wyzwalacze w Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Typowe problemy, przyczyny i rozwiązania

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Potok aplikacji Azure Functions zgłasza błąd z łącznością z prywatnym punktem końcowym
 
Masz Data Factory i aplikację funkcji platformy Azure uruchomioną w prywatnym punkcie końcowym. Próbujesz uruchomić potok, który współdziała z aplikacją funkcji. Wypróbowano trzy różne metody, ale jeden zwraca błąd "złe żądanie", a pozostałe dwie metody zwracają "103 błąd zabroniony".

**Przyczyna**

Data Factory obecnie nie obsługuje łącznika prywatnego punktu końcowego dla aplikacji funkcji. Azure Functions odrzuca wywołania, ponieważ jest ono skonfigurowane do zezwalania tylko na połączenia z prywatnego linku.

**Rozwiązanie**

Utwórz punkt końcowy **PrivateLinkService** i podaj system DNS swojej aplikacji funkcji.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Uruchomienie potoku zostało anulowane, ale monitor nadal pokazuje stan postępu

**Przyczyna**

Po anulowaniu uruchomienia potoku monitorowanie potoku często nadal pokazuje stan postępu. Dzieje się tak z powodu problemu z pamięcią podręczną przeglądarki. Mogą również nie mieć prawidłowych filtrów monitorowania.

**Rozwiązanie**

Odśwież przeglądarkę i Zastosuj poprawne filtry monitorowania.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Podczas kopiowania potoku pojawia się błąd "DelimitedTextMoreColumnsThanDefined"
 
 **Przyczyna**
 
Jeśli kopiowany folder zawiera pliki z różnymi schematami, takimi jak zmienna liczba kolumn, różne ograniczniki, Ustawienia znaków cudzysłowu lub niektóre problemy z danymi, potok Data Factory może zgłosić ten błąd:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Rozwiązanie**

Wybierz opcję **kopiowania binarnego** podczas tworzenia działania kopiowania. W ten sposób, w przypadku kopiowania zbiorczego lub migrowania danych z jednej usługi Data Lake do innej, Data Factory nie będzie można otworzyć plików, aby odczytać schemat. Zamiast tego Data Factory będzie traktować każdy plik jako binarny i skopiować go do innej lokalizacji.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Uruchomienie potoku kończy się niepowodzeniem po osiągnięciu limitu pojemności środowiska Integration Runtime dla przepływu danych

**Wykonaj**

Komunikat o błędzie:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Przyczyna**

Osiągnięto limit pojemności środowiska Integration Runtime. Może być uruchomiona duża ilość przepływu danych przy użyciu tego samego środowiska Integration Runtime w tym samym czasie. Aby uzyskać szczegółowe informacje [, zobacz limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Rozwiązanie**
 
- Uruchamianie potoków o różnych porach wyzwalacza.
- Utwórz nowe środowisko Integration Runtime i Podziel potoki na wiele środowisk Integration Runtime.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Jak wykonać błędy na poziomie działania i błędy w potokach

**Przyczyna**

Azure Data Factory aranżacji umożliwia logikę warunkową i umożliwia użytkownikom korzystanie z różnych ścieżek na podstawie wyniku poprzedniej aktywności. Umożliwia ona cztery ścieżki warunkowe: **po powodzeniu** (domyślny przebieg), **po awarii**, **po zakończeniu** i **po pominięciu**. 

Azure Data Factory oblicza wynik wszystkich działań na poziomie liścia. Wyniki potoku są pomyślne tylko wtedy, gdy wszystkie opuszczenia zostały zakończone pomyślnie. Jeśli działanie liścia zostało pominięte, ocenimy jego działanie nadrzędne. 

**Rozwiązanie**

* Zaimplementuj kontrole na poziomie działania, wykonując następujące czynności [, jak obsługiwać awarie i błędy potoków](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Użyj Azure Logic Apps, aby monitorować potoki w regularnych odstępach czasu [, wykonując zapytania według fabryki](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Wizualnie Monitoruj potok](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Jak monitorować błędy potoków w regularnych odstępach czasu

**Przyczyna**

Może być konieczne monitorowanie niezakończonych potoków Data Factory w interwałach, powiedzmy 5 minut. Można wykonywać zapytania i filtrować uruchomienia potoku z fabryki danych przy użyciu punktu końcowego. 

**Rozwiązanie**
* Można skonfigurować aplikację logiki platformy Azure, która będzie wysyłać zapytania do wszystkich zakończonych niepowodzeniem potoków co 5 minut, zgodnie z opisem w temacie [zapytania według fabryki](/rest/api/datafactory/pipelineruns/querybyfactory). Następnie możesz raportować zdarzenia do systemu biletów.
* [Wizualnie Monitoruj potok](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Stopień wzrostu równoległości nie powoduje większej przepływności

**Przyczyna** 

Stopień równoległości w instrukcji *foreach* to faktycznie maksymalny stopień równoległości. Nie możemy zagwarantować, że liczba wykonanych wykonań w tym samym czasie, ale ten parametr gwarantuje, że nigdy nie przejdzie powyżej ustawionej wartości. Powinna zostać wyświetlona jako limit, który ma być używany podczas kontrolowania współbieżnego dostępu do źródeł i ujścia.

Znane fakty dotyczące instrukcji *foreach*
 * Instrukcja foreach ma właściwość o nazwie Batch Count (n), gdzie wartość domyślna to 20, a maksymalna to 50.
 * Liczba partii, n, jest używana do tworzenia kolejek n. W dalszej części omówiono niektóre szczegóły dotyczące sposobu konstruowania tych kolejek.
 * Każda kolejka jest uruchamiana sekwencyjnie, ale można równolegle pracować z kilkoma kolejkami.
 * Kolejki są wstępnie utworzone. Oznacza to, że w czasie wykonywania nie ma potrzeby ponownego równoważenia kolejek.
 * W dowolnym momencie masz co najwyżej jeden element przetwarzany na kolejkę. Oznacza to, że co najwyżej n elementów jest przetwarzanych w dowolnym momencie.
 * Łączny czas przetwarzania foreach jest równy czasowi przetwarzania najdłuższej kolejki. Oznacza to, że działanie foreach jest zależne od tego, jak są konstruowane kolejki.
 
**Rozwiązanie**

 * Nie należy używać działania *setvariable* wewnątrz *dla każdego* uruchomionego równolegle.
 * Biorąc pod uwagę sposób konstruowania kolejek, klient może poprawić wydajność foreach przez ustawienie wielu instrukcji *foreach* , w których każda instrukcja foreach będzie miała elementy o podobnym czasie przetwarzania. Zapewni to, że długie uruchomienia są przetwarzane równolegle, a nie sekwencyjnie.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>Stan potoku jest umieszczany w kolejce lub zablokowany przez długi czas
 
 **Przyczyna**
 
 Może się to zdarzyć z różnych powodów, takich jak naciśnięcie ograniczeń współbieżności, przestoje usług, awarie sieci i tak dalej.
 
 **Rozwiązanie**
 
* Limit współbieżności: Jeśli potok ma zasady współbieżności, sprawdź, czy nie ma żadnych starych uruchomień potoków w toku. Maksymalna wartość współbieżności potoku w Azure Data Factory wynosi 10 potoków. 
* Limity monitorowania: Przejdź do kanwy tworzenia ADF, wybierz potok i ustal, czy ma do niej przypisaną Właściwość współbieżności. Jeśli tak, przejdź do widoku monitorowanie i upewnij się, że nie ma żadnych danych w ciągu ostatnich 45 dni, które są w toku. Jeśli coś jest w toku, można je anulować i rozpocząć nowe uruchomienie potoku.
* Problemy przejściowe: możliwe, że w danym przebiegu wystąpił przejściowy problem z siecią, niepowodzenia poświadczeń, przestoje usług itp.  W takim przypadku Azure Data Factory ma wewnętrzny proces odzyskiwania, który monitoruje wszystkie uruchomienia i uruchamia je po zauważeniu błędu. Ten proces odbywa się co godzinę, więc jeśli przebieg jest zablokowany przez więcej niż godzinę, utwórz przypadek pomocy technicznej.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Dłuższe czasy uruchamiania działań w ramach kopiowania i przepływu danych usługi ADF

**Przyczyna**

Taka sytuacja może wystąpić, jeśli nie zaimplementowano funkcji Time to Live na potrzeby przepływu danych lub zoptymalizowanej SHIR.

**Rozwiązanie**

* Jeśli uruchomienie każdego działania kopiowania trwa do 2 minut, a problem występuje głównie w sprzężeniu sieci wirtualnej (w porównaniu do środowiska Azure IR), może to być problem z wydajnością kopiowania. Aby zapoznać się z krokami rozwiązywania problemów, przejdź do pozycji [Kopiowanie ulepszeń wydajności.](./copy-activity-performance-troubleshooting.md)
* Aby zmniejszyć czas uruchamiania klastra dla działań przepływu danych, można użyć funkcji Time to Live. Przejrzyj [Integration Runtime przepływu danych.](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Problemy z wydajnością w SHIR (samodzielnie hostowane Integration Runtime)
 
 **Przyczyna**
 
Taka sytuacja może wystąpić, jeśli nie przeprowadzono skalowania SHIR w dół zgodnie z obciążeniem.

**Rozwiązanie**

* Jeśli wystąpi problem z pojemnością SHIR, Uaktualnij maszynę wirtualną, aby zwiększyć jej obciążenie. Jeśli zostanie wyświetlony komunikat o błędzie dotyczący ogólnego błędu lub nieznanego błędu środowiska IR, samodzielnego uaktualnienia IR lub samodzielnego połączenia IR, które może wygenerować długą kolejkę, przejdź do obszaru [Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime.](./self-hosted-integration-runtime-troubleshoot-guide.md)

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Komunikaty o błędach ze względu na długie kolejki kopiowania i przepływu danych usługi ADF

**Przyczyna**

Komunikaty o błędach związanych z kolejką Long mogą się pojawiać z różnych powodów. 

**Rozwiązanie**
* Jeśli zostanie wyświetlony komunikat o błędzie z dowolnego źródła lub miejsca docelowego za pośrednictwem łączników, które mogą generować długą kolejkę, przejdź do [przewodnika rozwiązywania problemów łącznika.](./connector-troubleshoot-guide.md)
* Jeśli zostanie wyświetlony komunikat o błędzie dotyczący mapowania przepływu danych, który może generować długą kolejkę, przejdź do [przewodnika rozwiązywania problemów z przepływami danych.](./data-flow-troubleshoot-guide.md)
* Jeśli zostanie wyświetlony komunikat o błędzie dotyczący innych działań, takich jak datakostki, działania niestandardowe lub HDI, które mogą generować długą kolejkę, przejdź do [przewodnika rozwiązywania problemów dotyczących działań.](./data-factory-troubleshoot-guide.md)
* Jeśli zostanie wyświetlony komunikat o błędzie dotyczący uruchamiania pakietów usług SSIS, które mogą generować długą kolejkę, przejdź do [przewodnika rozwiązywania problemów z wykonywaniem pakietu Azure-SSIS](./ssis-integration-runtime-ssis-activity-faq.md) i [Przewodnik rozwiązywania problemów dotyczących zarządzania Integration Runtime.](./ssis-integration-runtime-management-troubleshoot.md)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)