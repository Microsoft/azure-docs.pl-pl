---
title: Kody błędów klienta dla aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Ten dokument zawiera tabelę kodów błędów klienta dla różnych składników aktualizacji urządzeń.
author: lichris
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: 5251d0cb09e40305d1efd89c31d3af0fa36ad385
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663649"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Aktualizacja urządzenia dla IoT Hub kodów błędów

Ten dokument zawiera tabelę kodów błędów dla różnych składników aktualizacji urządzeń. Jest to przeznaczone dla użytkowników, którzy chcą próbować przeanalizować własne kody błędów w celu zdiagnozowania i rozwiązania problemów.

Istnieją dwa podstawowe składniki po stronie klienta, które mogą zgłosić kody błędów: Agent aktualizacji urządzeń i Agent optymalizacji dostarczania.

## <a name="device-update-agent"></a>Agent aktualizacji urządzeń

### <a name="resultcode-and-extendedresultcode"></a>ResultCode i ExtendedResultCode

Aktualizacja urządzenia dla IoT Hub podstawowych interfejsów PnP `ResultCode` i `ExtendedResultCode` , których można użyć do diagnozowania błędów. [Dowiedz się więcej](device-update-plug-and-play.md) o podstawowym interfejsie PnP aktualizacji urządzenia.

#### <a name="resultcode"></a>ResultCode

`ResultCode` jest ogólnym kodem stanu i jest zgodna z Konwencją kodu stanu HTTP.
[Dowiedz się więcej](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) na temat kodów stanu HTTP.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` jest liczbą całkowitą z kodowanymi informacjami o błędzie.

Najprawdopodobniej zobaczysz `ExtendedResultCode` jako liczbę całkowitą ze znakiem w interfejsie PnP. Aby zdekodować `ExtendedResultCode` , przekonwertuj liczbę całkowitą ze znakiem na wartość szesnastkową bez znaku. Używane są tylko pierwsze 4 bajty `ExtendedResultCode` i mają postać, `F` `FFFFFFF` gdzie pierwszy Nibble to **kod funkcji** , a pozostałe bity to **Kod błędu**.

**Kody funkcji**

| Kod obiektu     | Opis  |
|-------------------|--------------|
| D                 | Błąd wywoływany z zestawu SDK DO|
| E                 | Kod błędu to errno |


Na przykład:

`ExtendedResultCode` to `-536870781`

Reprezentacja szesnastkowa bez znaku `-536870781` is ma wartość `FFFFFFFF E0000083` .

| Zignoruj    | Kod obiektu  | Kod błędu   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` wartość szesnastkowa jest `131` w postaci dziesiętnej, która jest wartością errno dla `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Agent optymalizacji dostarczania
Poniższa tabela zawiera listę kodów błędów odnoszących się do składnika optymalizacji dostarczania (DO) klienta aktualizacji urządzenia. Składnik do jest odpowiedzialny za pobieranie zawartości aktualizacji na urządzenie IoT.

Kod błędu DO można uzyskać, sprawdzając wyjątki zgłoszone w odpowiedzi na wywołanie interfejsu API.

| Kod błędu  | Błąd ciągu                       | Typ                 | Opis |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n/d                  | Optymalizacja dostarczania nie mogła zapewnić usługi |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Pobierz zadanie         | Pobieranie pliku nie podano postępu w określonym okresie |
| 0x80D02003L | DO_E_JOB_NOT_FOUND                 | Pobierz zadanie         | Nie znaleziono zadania |
| 0x80D02005L | DO_E_NO_DOWNLOADS                  | Pobierz zadanie         | Obecnie nie istnieją żadne pobrania |
| 0x80D0200CL | DO_E_JOB_TOO_OLD                   | Pobierz zadanie         | Zadanie nie zostało ukończone lub zostało anulowane przed osiągnięciem maksymalnego progu wieku |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Pobierz zadanie         | Metoda SetProperty () lub GetProperty () wywołana z nieznanym IDENTYFIKATORem właściwości |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Pobierz zadanie         | Nie można wywołać metody SetProperty () na właściwości tylko do odczytu |
| 0x80D02013L | DO_E_INVALID_STATE                 | Pobierz zadanie         | Żądana akcja jest niedozwolona w bieżącym stanie zadania. Zadanie mogło zostać anulowane lub zakończone transfer. Jest to teraz w stanie tylko do odczytu. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Pobierz zadanie         | Nie można rozpocząć pobierania, ponieważ nie określono ujścia pobierania (lokalny plik lub interfejs strumienia) |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload, interfejs| Pobieranie zostało rozpoczęte bez podawania identyfikatora URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Warunki przejściowe | Pobieranie zostało wstrzymane ze względu na utratę łączności sieciowej |
| 0x80D05001L | DO_E_HTTP_BLOCKSIZE_MISMATCH       | HTTP                 | Serwer HTTP zwrócił odpowiedź o rozmiarze danych, która nie jest równa zażądanemu |
| 0x80D05002L | DO_E_HTTP_CERT_VALIDATION          | HTTP                 | Sprawdzanie poprawności certyfikatu serwera HTTP nie powiodło się |
| 0x80D05010L | DO_E_INVALID_RANGE                 | HTTP                 | Określony zakres bajtów jest nieprawidłowy |
| 0x80D05011L | DO_E_INSUFFICIENT_RANGE_SUPPORT    | HTTP                 | Serwer nie obsługuje wymaganego protokołu HTTP. Optymalizacja dostarczania (DO) wymaga, aby serwer obsługiwał nagłówek protokołu zakresu |
| 0x80D05012L | DO_E_OVERLAPPING_RANGES            | HTTP                 | Lista zakresów bajtów zawiera nakładające się zakresy, które nie są obsługiwane. |
## <a name="device-update-content-service"></a>Aktualizacja urządzenia — usługa zawartości
Poniższa tabela zawiera listę kodów błędów dotyczących składnika usługi zawartości usługi aktualizacji urządzenia. Składnik usługi zawartości jest odpowiedzialny za obsługę importowania zawartości aktualizacji.

| Kod błędu                    | Błąd ciągu                                                               | Następne kroki                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Aktualizacja o tej samej tożsamości już istnieje.                              | Upewnij się, że importujesz aktualizację, która nie została jeszcze zaimportowana do tego wystąpienia aktualizacji urządzenia dla IoT Hub. |
| "DuplicateContentImport"      | Identyczna zawartość zaimportowana jednocześnie wielokrotnie.                  | Analogicznie jak w przypadku UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Wystąpił błąd podczas przetwarzania manifestu importu.                                          | Zapoznaj się z artykułem [Importowanie koncepcji](./import-concepts.md) i [zaimportuj dokumentację aktualizacji](./import-update.md) , aby uzyskać odpowiednie formatowanie manifestu importu. |
| "CannotDownload"              | Nie można pobrać manifestu importu.                                           | Upewnij się, że adres URL pliku manifestu importowania jest nadal ważny. |
| "CannotParse"                 | Nie można przeanalizować manifestu importu.                                              | Sprawdź, czy manifest zaimportowano w celu uzyskania dokładności względem schematu zdefiniowanego w dokumentacji [aktualizacji importu](./import-update.md) . |
| "UnsupportedVersion"          | Wersja schematu manifestu importowania nie jest obsługiwana.                           | Upewnij się, że manifest importu korzysta z najnowszego schematu zdefiniowanego w dokumentacji [aktualizacji importu](./import-update.md) . |
| "UpdateLimitExceeded"         | Wystąpił błąd podczas importowania aktualizacji z powodu przekroczenia limitu.                              | Osiągnięto limit liczby różnych dostawców, nazw lub wersji dozwolonych w wystąpieniu aktualizacji urządzenia dla IoT Hub. Usuń niektóre aktualizacje z wystąpienia i spróbuj ponownie. |
| "UpdateProvider"              | Nie można zaimportować nowego dostawcy aktualizacji.                                       | Osiągnięto limit liczby różnych __dostawców__ dozwolonych w wystąpieniu aktualizacji urządzenia dla IoT Hub. Usuń niektóre aktualizacje z wystąpienia i spróbuj ponownie. |
| "Updatename"                  | Nie można zaimportować nowej nazwy aktualizacji dla określonego dostawcy.                | Osiągnięto limit liczby różnych __nazw__ dozwolonych w ramach jednego dostawcy w wystąpieniu aktualizacji urządzenia dla IoT Hub. Usuń niektóre aktualizacje z wystąpienia i spróbuj ponownie. |
| UpdateVersion               | Nie można zaimportować nowej wersji aktualizacji dla określonego dostawcy i nazwy.    | Osiągnięto limit liczby różnych __wersji__ dozwolonych w ramach jednego dostawcy i nazwy w wystąpieniu aktualizacji urządzenia dla IoT Hub. Usuń niektóre aktualizacje o tej nazwie z Twojego wystąpienia i spróbuj ponownie. |
| "UpdateProviderCompatibility" | Nie można zaimportować dodatkowego dostawcy aktualizacji z określoną zgodnością. | Podczas definiowania właściwości zgodności modelu sprzętu i urządzenia w manifeście importu należy pamiętać, że aktualizacja urządzenia dla IoT Hub obsługuje jednego dostawcę i nazwę dla danego producenta/modelu. Oznacza to, że jeśli spróbujesz użyć tego samego producenta/właściwości zgodności modelu z więcej niż jedną kombinacją dostawcy/nazwy, zobaczysz te błędy. Aby rozwiązać ten problem, upewnij się, że wszystkie aktualizacje dla danego urządzenia (zgodnie z definicją producenta/modelu) używają tego samego dostawcy i nazwy. Chociaż nie jest to wymagane, warto rozważyć przeprowadzenie dostawcy w taki sam sposób, jak w przypadku producenta i nazwy takiej samej jak model, tylko dla uproszczenia. |
| "UpdateNameCompatibility"     | Nie można zaimportować dodatkowej nazwy aktualizacji z określoną zgodnością.     | Analogicznie jak dla UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Nie można zaimportować dodatkowej wersji aktualizacji z określoną zgodnością.  | Analogicznie jak dla UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Błąd podczas przetwarzania pliku źródłowego.                                              |                                    |
| "ContentFileCannotDownload"   | Nie można pobrać pliku źródłowego.                                               | Upewnij się, że adres URL plików aktualizacji jest nadal ważny. |

**[Następny krok: Rozwiązywanie problemów z aktualizacją urządzenia](.\troubleshoot-device-update.md)**