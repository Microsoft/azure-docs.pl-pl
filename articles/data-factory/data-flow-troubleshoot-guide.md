---
title: Rozwiązywanie problemów z przepływami danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w usłudze Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: e9e9b10cc9bae029fe11fb2bd1f8b76cf120744a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417814"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z przepływami danych w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano typowe metody rozwiązywania problemów z przepływami danych w usłudze Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-Executor-SourceInvalidPayload
- **Komunikat:** Nie można wyświetlić podglądu danych, debugowania i wykonania przepływu danych potoku, ponieważ kontener nie istnieje
- **Przyczyny:** Gdy zestaw danych zawiera kontener, który nie istnieje w magazynie
- **Zalecenie:** Upewnij się, że kontener, do którego odwołuje się w zestawie danych, istnieje lub jest dostępny.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-Executor-SystemImplicitCartesian

- **Komunikat:** Niejawny produkt kartezjański dla sprzężenia wewnętrznego nie jest obsługiwany, zamiast tego należy użyć funkcji CROSS JOIN. Kolumny używane w sprzężeniu powinny utworzyć unikatowy klucz dla wierszy.
- **Przyczyny:** Niejawny produkt kartezjański dla sprzężenia INNER między planami logicznymi nie jest obsługiwany. Jeśli kolumny używane w sprzęcie tworzy unikatowy klucz, co najmniej jedna kolumna z obu stron relacji są wymagane.
- **Zalecenie:** W przypadku sprzężeń nieobjętych równością musisz wybrać CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-Executor-SystemInvalidJson

- **Komunikat**: Błąd analizowania JSON, nieobsługiwanie kodowaniem lub wielowierszowe
- **Przyczyny:** Możliwe problemy z plikiem JSON: nieobsługiwał kodowanie, uszkodzone bajty lub używanie źródła JSON jako pojedynczego dokumentu w wielu liniach zagnieżdżonych
- **Zalecenie:** Sprawdź, czy kodowanie pliku JSON jest obsługiwane. W przypadku transformacji źródła, która używa zestawu danych JSON, rozwiń węzeł "Ustawienia JSON" i włącz "Pojedynczy dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-Executor-BroadcastTimeout

- **Komunikat:** Błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji generuje dane w ciągu 60 sekund w przebiegach debugowania i 300 sekund w przebiegach zadań
- **Przyczyny:** Emisja ma domyślny limit czasu 60 sekund w przebiegach debugowania i 300 sekund w przebiegach zadań. Strumień wybrany do emisji wydaje się duży, aby utworzyć dane w ramach tego limitu.
- **Zalecenie:** Unikaj emitowania dużych strumieni danych, w których przetwarzanie może trwać dłużej niż 60 sekund. Wybierz mniejszy strumień do emisji. Duże tabele SQL/DW i pliki źródłowe są zazwyczaj złymi kandydatami.

### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-Executor-Conversion

- **Komunikat**: Przekonwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyny**: Dane nie są w oczekiwanym formacie
- **Zalecenie**: Użyj właściwego typu danych

### <a name="error-code-df-executor-invalidcolumn"></a>Kod błędu: DF-Executor-InvalidColumn

- **Komunikat:** Nazwa kolumny musi być określona w kwerendzie, ustaw alias, jeśli używasz funkcji SQL
- **Przyczyny**: Nie określono nazwy kolumny
- **Zalecenie:** Ustaw alias, jeśli używasz funkcji SQL, takiej jak min()/max(), itp.

### <a name="error-code-getcommand-outputasync-failed"></a>Kod błędu: GetCommand OutputAsync nie powiodło się

- **Komunikat**: Podczas debugowania przepływu danych i podglądu danych: GetCommand OutputAsync nie powiodło się z ...
- **Przyczyny:** Jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie:** Jeśli ponowić próbę i ponownie uruchomić nie rozwiąż problemu, skontaktuj się z działem obsługi klienta.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Kod błędu: Trafienie nieoczekiwanego wyjątku i wykonanie nie powiodło się

- **Komunikat:** Podczas wykonywania działania przepływu danych: Trafienie nieoczekiwanego wyjątku i wykonanie nie powiodło się.
- **Przyczyny:** Jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie:** Jeśli ponowić próbę i ponownie uruchomić nie rozwiąż problemu, skontaktuj się z działem obsługi klienta.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów

1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródła i ujścia odwiedź usługę połączony dla każdego zestawu danych, którego używasz i testują połączenia.
1. Sprawdź stan połączeń plików i tabel z projektanta przepływu danych. Włącz debugowanie i kliknij podgląd danych na przekształceniach źródła, aby upewnić się, że masz dostęp do danych.
1. Jeśli wszystko wygląda dobrze z podglądu danych, przejdź do projektanta potoku i umieścić przepływ danych w działaniu potoku. Debugowanie potoku dla testu end-to-end.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, wypróbuj następujące zasoby:
*  [Blog Usługi data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji fabryki danych](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum przepełnienia stosu dla fabryki danych](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informacje o fabryce danych](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik wydajności przepływów danych mapowania usługi ADF](concepts-data-flow-performance.md)
