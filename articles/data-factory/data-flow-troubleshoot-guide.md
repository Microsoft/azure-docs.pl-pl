---
title: Rozwiązywanie problemów z przepływami danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: c9ac8d7ea465a26d29bf8f8fbc15dcefaf9d7575
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187283"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z przepływami danych w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z przepływami danych w Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-wykonawca-SourceInvalidPayload
- **Komunikat**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje
- **Przyczyny**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie
- **Zalecenie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje lub jest dostępny.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian

- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyny**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego między planami logicznymi nie jest obsługiwany. Jeśli kolumny używane w sprzężeniu tworzą unikatowy klucz, wymagana jest co najmniej jedna kolumna z obu stron relacji.
- **Zalecenie**: w przypadku sprzężeń opartych na nierówności należy wybrać opcję niestandardowego SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson

- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyny**: możliwe problemy dotyczące pliku JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń węzeł Ustawienia JSON i Włącz opcję "pojedynczy dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-wykonawca-BroadcastTimeout

- **Komunikat**: błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji produkuje dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w uruchomieniach zadania
- **Przyczyny**: emisja ma domyślny limit czasu wynoszący 60 s podczas przebiegów debugowania i 300 s w przebiegach zadania. Strumień wybierany do emisji jest duży, aby można było utworzyć dane w ramach tego limitu.
- **Zalecenie**: Sprawdź, czy na karcie Optymalizacja przekształceń przepływu danych jest włączona funkcja join, EXISTS i Lookup. Domyślną opcją emisji jest "Auto". Jeśli ta opcja jest ustawiona, lub jeśli ręcznie ustawisz lewą lub prawą stronę do emisji w obszarze "Fixed" (stała), możesz ustawić większą konfigurację Azure Integration Runtime lub wyłączyć opcję emisji. Zalecanym podejściem do uzyskania najlepszej wydajności w przepływach danych jest umożliwienie usłudze Spark emisji przy użyciu opcji "Auto" i użycia zoptymalizowanej pod kątem pamięci Azure IR.

### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion

- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyny**: dane mają nieoczekiwany format
- **Zalecenie**: Użyj poprawnego typu danych

### <a name="error-code-df-executor-invalidcolumn"></a>Kod błędu: DF-wykonawca-InvalidColumn

- **Komunikat**: Nazwa kolumny musi być określona w zapytaniu, Ustawianie aliasu, jeśli używana jest funkcja SQL
- **Przyczyny**: nie określono nazwy kolumny
- **Zalecenie**: Ustaw alias, jeśli używana jest funkcja SQL, taka jak min ()/Max () itp.

### <a name="error-code-getcommand-outputasync-failed"></a>Kod błędu: GetCommand OutputAsync nie powiodła się

- **Komunikat**: podczas debugowania przepływu danych i podglądu danych: GetCommand OutputAsync nie powiodła się z...
- **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Kod błędu: Wystąpił nieoczekiwany wyjątek i wykonywanie nie powiodło się

- **Komunikat**: podczas wykonywania działania przepływu danych: Wystąpił nieoczekiwany wyjątek, a wykonywanie nie powiodło się.
- **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów

1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródłowej i ujścia należy odwiedzić połączoną usługę dla każdego zestawu danych, którego używasz, i przetestować połączenia.
1. Sprawdź stan połączeń plików i tabel z projektanta przepływów danych. Aby upewnić się, że masz dostęp do danych, przejdź na Debugowanie i kliknij pozycję Podgląd danych w obszarze przekształcenia źródłowe.
1. Jeśli wszystko wygląda dobrze z wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)
