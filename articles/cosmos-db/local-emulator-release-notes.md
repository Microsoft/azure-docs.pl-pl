---
title: Informacje o wersji i pobraniu emulatora usługi Azure Cosmos
description: Zapoznaj się z informacjami o wersji emulatora usługi Azure Cosmos dla różnych wersji i Pobierz informacje.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: e9f28aeb2a47c2ce7b439cbe46dcc39df3011c33
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95997158"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos emulator — informacje o wersji i pobrania
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule przedstawiono informacje o wersji emulatora usługi Azure Cosmos z listą aktualizacji funkcji, które zostały wprowadzone w poszczególnych wersjach. Znajduje się w nim również Najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Pobierz

| | |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="2118-6-november-2020"></a>2.11.8 (6 listopada 2020)

 - Ta wersja zawiera aktualizację Eksplorator danych emulatora usługi Cosmos i rozwiązuje problem polegający na tym, że klienci protokołu TLS 1,3 próbują otworzyć Eksplorator danych.

### <a name="2116-6-october-2020"></a>2.11.6 (6 października 2020)

 - Ta wersja rozwiązuje problem związany z współbieżnością, gdy można utworzyć wiele kontenerów w tym samym czasie. W takich przypadkach dane emulatora są pozostawione w stanie uszkodzonym, a następujące żądania interfejsu API do punktu końcowego emulatora mogą zakończyć się niepowodzeniem z błędami "Usługa niedostępna", wymagając ponownego uruchomienia i zresetowania danych lokalnych emulatora.

### <a name="2115-23-august-2020"></a>2.11.5 (23 sierpnia 2020)

W tej wersji dodano dwie nowe opcje uruchamiania emulatora Cosmos: 

* "/EnablePreview" — umożliwia korzystanie z funkcji w wersji zapoznawczej dla emulatora. Funkcje w wersji zapoznawczej, które są nadal w fazie tworzenia i są dostępne za pośrednictwem CI i przykład pisania.
* "/EnableAadAuthentication" — umożliwia emulatorowi akceptowanie niestandardowych tokenów Azure Active Directory jako alternatywę dla kluczy podstawowych usługi Azure Cosmos. Ta funkcja jest nadal w fazie opracowywania. określone przypisania ról i inne ustawienia związane z uprawnieniami nie są obecnie obsługiwane.

### <a name="2112-07-july-2020"></a>2.11.2 (07 lipca 2020)

- Ta wersja zmienia sposób, w jaki są zbierane ślady ETL podczas rozwiązywania problemów z emulatorem Cosmos. Żądanie WP (narzędzia do środowiska uruchomieniowego systemu Windows) to teraz domyślne narzędzia do przechwytywania śladów opartych na usłudze ETL, podczas gdy stary plik przechwytywania oparty na usłudze LOGMAN jest przestarzały. Ta zmiana jest wymagana w części, ponieważ najnowsze aktualizacje zabezpieczeń systemu Windows miały nieoczekiwany wpływ na to, jak program LOGMAN działa podczas wykonywania za pomocą emulatora Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 czerwca 2020)

- W tej wersji rozwiązano kilka usterek związanych z Eksplorator danychami emulatora. W niektórych przypadkach w przypadku używania emulatora Eksplorator danych za pośrednictwem przeglądarki sieci Web nie można nawiązać połączenia z punktem końcowym emulatora Cosmos i wszystkie powiązane akcje, takie jak tworzenie bazy danych lub kontenera, spowodują błąd. Drugi problem rozwiązany jest związany z tworzeniem elementu z pliku JSON przy użyciu akcji przekazywania Eksplorator danych.

### <a name="2110"></a>2.11.0

- W tej wersji wprowadzono obsługę przepływności dla aprowizacji automatyczne skalowanie. Te nowe funkcje obejmują możliwość ustawiania niestandardowego maksymalnego poziomu przepływności w jednostkach żądania (RU/s), włączania funkcji automatycznego skalowania w istniejących bazach danych i kontenerach oraz pomocy programistycznej za pomocą zestawów SDK Azure Cosmos DB.
- Usuń problem podczas wykonywania zapytania przez dużą liczbę dokumentów (ponad 1 GB), ponieważ emulator zakończy się niepowodzeniem z kodem stanu błędu wewnętrznego 500.

### <a name="292"></a>2.9.2

- W tej wersji rozwiązano błąd podczas włączania obsługi punktu końcowego MongoDb w wersji 3,2. Dodaje również obsługę generowania śladów ETL na potrzeby rozwiązywania problemów przy użyciu funkcji żądanie WP zamiast LOGMAN.

### <a name="291"></a>2.9.1

- W tej wersji rozwiązano kilka problemów z obsługą interfejsu API zapytań i przywraca zgodność ze starszymi serwerami OSs, takimi jak Windows Server 2012.

### <a name="290"></a>2.9.0

- W tej wersji dodano opcję ustawiania spójności spójnego prefiksu i zwiększenia maksymalnych limitów dla użytkowników i uprawnień.

### <a name="272"></a>2.7.2

- W tej wersji dodano obsługę serwera MongoDB w wersji 3,6 do emulatora Cosmos. Aby uruchomić punkt końcowy MongoDB, którego celem jest wersja 3,6 usługi, uruchom emulator z wiersza polecenia administratora z opcją "/EnableMongoDBEndpoint = 3.6".

### <a name="270"></a>2.7.0

- W tej wersji rozwiązano regresję, która uniemożliwiła użytkownikom wykonywanie zapytań dotyczących konta interfejsu API SQL z emulatora w przypadku korzystania z klientów platformy .NET Core lub x86.

### <a name="246"></a>2.4.6

- Ta wersja zapewnia parzystość dzięki funkcjom w usłudze Azure Cosmos z lipca 2019 z wyjątkami zanotowanymi w artykule [opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md). Naprawia również kilka usterek odnoszących się do zamykania emulatora, gdy są wywoływane za pośrednictwem wiersza polecenia i wewnętrznych zastąpień adresów IP dla klientów zestawu SDK używających trybu bezpośredniego łączności.

### <a name="243"></a>zasadniczy

- Domyślnie wyłączone uruchamianie usługi MongoDB. Tylko punkt końcowy SQL jest domyślnie włączony. Użytkownik musi ręcznie uruchomić punkt końcowy przy użyciu opcji wiersza polecenia "/EnableMongoDbEndpoint" emulatora. Teraz podobnie jak w przypadku wszystkich innych punktów końcowych usługi, takich jak Gremlin, Cassandra i Table.
- Rozwiązano usterkę w emulatorze, gdy rozpoczyna się od "/AllowNetworkAccess", gdzie punkty końcowe Gremlin, Cassandra i Table nie obsługiwały prawidłowo żądań od klientów zewnętrznych.
- Dodaj bezpośrednie porty połączeń do ustawień reguł zapory.

### <a name="240"></a>2.4.0

- Rozwiązano problem z uruchomieniem emulatora, gdy na komputerze hosta znajdują się aplikacje do monitorowania sieci, na przykład klient pulsu.
