---
title: Wyjątki Menedżer zasobów Azure Service Bus | Microsoft Docs
description: Lista wyjątków Service Bus nastawionych przez Azure Resource Manager i sugerowane akcje.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85336980"
---
# <a name="service-bus-resource-manager-exceptions"></a>Wyjątki Menedżer zasobów Service Bus

W tym artykule wymieniono wyjątki generowane podczas korzystania z Azure Service Bus przy użyciu szablonów Azure Resource Manager i bezpośrednich wywołań.

> [!IMPORTANT]
> Ten dokument jest często aktualizowany. Sprawdź aktualizacje.

Poniżej znajdują się różne wyjątki/błędy, które są przedstawione w Azure Resource Manager.

## <a name="error-bad-request"></a>Błąd: Nieprawidłowe żądanie

"Złe żądanie" oznacza, że żądanie odebrane przez Menedżer zasobów nie powiodło się.

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Nie można ustawić właściwości *"Property Name"* podczas tworzenia kolejki, ponieważ przestrzeń nazw *"name* " ma używaną warstwę "Basic". Ta operacja jest obsługiwana tylko w warstwie Standardowa lub Premium. | W warstwie Podstawowa Azure Service Bus nie można ustawić ani zaktualizować poniższych właściwości — <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> Prześlij dalej </li> <li> Tematy </li> </ul> | Rozważ uaktualnienie z warstwy Podstawowa do standardowa lub Premium, aby korzystać z tej funkcji. |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Nie można zmienić wartości właściwości "requiresDuplicateDetection" istniejącej kolejki (lub tematu). | Wykrywanie duplikatów musi być włączone/wyłączone w momencie tworzenia jednostki. Nie można zmienić parametru konfiguracji wykrywania duplikatów po utworzeniu. | Aby włączyć wykrywanie duplikatów dla wcześniej utworzonej kolejki/tematu, można utworzyć nową kolejkę/temat z wykrywaniem duplikatów, a następnie przekazać ją z oryginalnej kolejki do nowej kolejki lub tematu. |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Określona wartość 16384 jest nieprawidłowa. Właściwość "MaxSizeInMegabytes" musi mieć jedną z następujących wartości: 1024; 2048; 3072; 4096; 5120. | Wartość MaxSizeInMegabytes jest nieprawidłowa. | Upewnij się, że MaxSizeInMegabytes ma jedną z następujących wartości: 1024, 2048, 3072, 4096, 5120. |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Nie można zmienić partycjonowania dla kolejki/tematu. | Nie można zmienić partycjonowania dla jednostki. | Utwórz nową jednostkę (kolejkę lub temat) i Włącz partycje. | 
| Nieprawidłowe żądanie | brak | Przestrzeń nazw *"name"* nie istnieje. | Przestrzeń nazw nie istnieje w ramach Twojej subskrypcji platformy Azure. | Aby rozwiązać ten problem, spróbuj wykonać poniższe czynności. <ul> <li> Upewnij się, że subskrypcja platformy Azure jest poprawna. </li> <li> Upewnij się, że przestrzeń nazw istnieje. </li> <li> Sprawdź, czy nazwa przestrzeni nazw jest poprawna (nie występują błędy pisowni ani ciągi o wartości null). </li> </ul> | 
| Nieprawidłowe żądanie | 40400 | Subcode = 40400. Jednostka docelowa autoprzekazywania nie istnieje. | Miejsce docelowe dla jednostki docelowej autoprzesyłania dalej nie istnieje. | Jednostka docelowa (Kolejka lub temat) musi istnieć przed utworzeniem źródła. Ponów próbę po utworzeniu jednostki docelowej. |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Długość podanego czasu blokady przekracza dozwolone maksimum wynoszące "5" minut. | Czas, dla którego można zablokować komunikat, musi być z przedziału od 1 minuty (minimum) do 5 minut (maksimum). | Upewnij się, że podany czas blokady wynosi od 1 do 5 minut. |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Nie można jednocześnie włączyć właściwości DelayedPersistence i RequiresDuplicateDetection. | Jednostki z włączonym wykrywaniem duplikatów muszą być trwałe, więc nie można opóźnić trwałości. | Dowiedz się więcej o [wykrywaniu duplikatów](duplicate-detection.md) |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. Nie można zmienić wartości właściwości RequiresSession istniejącej kolejki. | W momencie tworzenia jednostki należy włączyć obsługę sesji. Po utworzeniu nie można włączyć/wyłączyć sesji dla istniejącej jednostki (kolejki lub subskrypcji) | Usuń i ponownie utwórz nową kolejkę (lub subskrypcję) z włączoną właściwością "RequiresSession". |
| Nieprawidłowe żądanie | 40000 | Kod Subcode = 40000. "URI_PATH" zawiera znaki, które nie są dozwolone przez Service Bus. Segmenty jednostek mogą zawierać tylko litery, cyfry, kropki (.), łączniki (-) i znaki podkreślenia (_). | Segmenty jednostek mogą zawierać tylko litery, cyfry, kropki (.), łączniki (-) i znaki podkreślenia (_). Wszystkie inne znaki powodują niepowodzenie żądania. | Upewnij się, że w ścieżce URI nie ma żadnych nieprawidłowych znaków. |


## <a name="error-code-429"></a>Kod błędu: 429

Podobnie jak w przypadku protokołu HTTP "kod błędu 429" wskazuje "zbyt wiele żądań". Oznacza to, że określony zasób (przestrzeń nazw) jest ograniczany z powodu zbyt dużej liczby żądań (lub z powodu powodujących konflikt operacji) dla tego zasobu.

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. Żądanie zostało przerwane, ponieważ przestrzeń nazw *obszaru nazw jest* ograniczana. | Ten warunek błędu zostaje osiągnięty, gdy liczba żądań przychodzących przekracza ograniczenie zasobu. | Poczekaj kilka sekund i spróbuj ponownie. <br/> <br/> Dowiedz się więcej na temat [limitów](../azure-resource-manager/management/request-limits-and-throttling.md) [przydziałów](service-bus-quotas.md) i żądań Azure Resource Manager|
| 429 | 40901 | Subcode = 40901. Inna operacja powodująca konflikt jest w toku. | Inna operacja powodująca konflikt jest w toku dla tego samego zasobu/jednostki | Poczekaj na zakończenie bieżącej operacji w toku przed ponowną próbą. |
| 429 | 40900 | Subcode = 40900. Kolizj. Żądasz operacji, która nie jest dozwolona w bieżącym stanie zasobu. | Ten stan może być osiągnięty w przypadku wykonywania wielu żądań w celu wykonania operacji na tej samej jednostce (Kolejka, temat, subskrypcja lub reguła) w tym samym czasie. | Poczekaj kilka sekund i spróbuj ponownie |
| 429 | 40901 | Wystąpił konflikt żądania w jednostce *"Nazwa jednostki* " z innym żądaniem | Inna operacja powodująca konflikt jest w toku dla tego samego zasobu/jednostki | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą |
| 429 | 40901 | Inne żądanie aktualizacji jest w toku dla jednostki *"Nazwa jednostki"*. | Inna operacja powodująca konflikt jest w toku dla tego samego zasobu/jednostki | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą |
| 429 | brak | Wystąpił konflikt zasobów. Inna operacja powodująca konflikt może być w toku. Jeśli ponowienie operacji nie powiodło się, czyszczenie w tle nadal oczekuje. Spróbuj ponownie później. | Ten stan może wystąpić, gdy istnieje oczekująca operacja w odniesieniu do tej samej jednostki. | Poczekaj na zakończenie poprzedniej operacji przed ponowną próbą. |


## <a name="error-code-not-found"></a>Kod błędu: nie znaleziono

Ta klasa błędów wskazuje, że nie znaleziono zasobu.

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nie znaleziono | brak | Nie znaleziono jednostki *"Nazwa jednostki"* . | Jednostka, względem której nie znaleziono operacji. | Sprawdź, czy jednostka istnieje, a następnie spróbuj ponownie wykonać operację. |
| Nie znaleziono | brak | Nie znaleziono. Operacja nie istnieje. | Operacja, którą próbujesz wykonać, nie istnieje. | Sprawdź operację i spróbuj ponownie. |
| Nie znaleziono | brak | Żądanie przychodzące nie zostało rozpoznane jako żądanie przełączenia zasad przestrzeni nazw. | Treść żądania przychodzącego ma wartość null, dlatego nie można jej wykonać jako żądania Put. | Sprawdź treść żądania, aby upewnić się, że nie jest ona pusta. | 
| Nie znaleziono | brak | Nie można odnaleźć jednostki obsługi komunikatów *"Nazwa jednostki"* . | Nie można odnaleźć jednostki, w której próbujesz wykonać operację. | Sprawdź, czy jednostka istnieje, a następnie spróbuj ponownie wykonać operację. |

## <a name="error-code-internal-server-error"></a>Kod błędu: wewnętrzny błąd serwera

Ta klasa błędów wskazuje, że wystąpił wewnętrzny błąd serwera

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Wewnętrzny błąd serwera | 50000 | Podkod = 50 000. Wewnętrzny błąd serwera| Może się zdarzyć z różnych powodów. Niektóre objawy to- <ul> <li> Żądanie klienta/treść jest uszkodzona i prowadzi do błędu. </li> <li> Upłynął limit czasu żądania klienta z powodu problemów z przetwarzaniem w usłudze. </li> </ul> | Aby rozwiązać ten problem <ul> <li> Upewnij się, że parametry żądań nie mają wartości null ani nie są źle sformułowane. </li> <li> Ponów żądanie. </li> </ul> |

## <a name="error-code-unauthorized"></a>Kod błędu: Brak autoryzacji

Ta klasa błędów wskazuje Brak autoryzacji do uruchomienia polecenia.

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Brak autoryzacji | brak | Nieprawidłowa operacja dla pomocniczej przestrzeni nazw. Pomocnicza przestrzeń nazw jest tylko do odczytu. | Operacja została wykonana względem pomocniczej przestrzeni nazw, która jest konfiguracją jako przestrzeń nazw tylko do odczytu. | Spróbuj ponownie wykonać polecenie względem głównej przestrzeni nazw. Dowiedz się więcej o [pomocniczej przestrzeni nazw](service-bus-geo-dr.md) |
| Brak autoryzacji | brak | MissingToken: nie znaleziono nagłówka autoryzacji. | Ten błąd występuje, gdy autoryzacja ma wartości null lub są nieprawidłowe. | Upewnij się, że wartość tokenu wymieniona w nagłówku autoryzacji jest poprawna, a nie wartością null. |