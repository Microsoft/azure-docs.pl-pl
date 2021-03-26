---
title: Informacje o wersji i pobraniu emulatora Azure Cosmos DB
description: Uzyskaj informacje o wersji emulatora Azure Cosmos DB dla różnych wersji i informacji o pobieraniu.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 442fa1c76d4667a54f1c13b411b1c052c9a5b134
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566646"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB emulator — informacje o wersji i pobrania
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule przedstawiono informacje o wersji emulatora Azure Cosmos DB z listą aktualizacji funkcji, które zostały wprowadzone w poszczególnych wydaniach. Znajduje się w nim również Najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Pobierz

| | Link |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Programowanie lokalne przy użyciu emulatora Azure Cosmos DB](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="21111-22-february-2021"></a>2.11.11 (22 lutego 2021)

 - W tej wersji zaktualizowano zawartość lokalną Eksplorator danych do najnowszej wersji Azure Portal.


### <a name="21110-5-january-2021"></a>2.11.10 (5 stycznia 2021)

 - Ta wersja aktualizuje zawartość lokalną Eksplorator danych do najnowszej wersji Azure Portal i dodaje nową opcję publiczną "/ExportPemCert", która umożliwia użytkownikowi emulatora bezpośrednie Eksportowanie certyfikatu emulatora publicznego jako. Plik PEM.

### <a name="2119-3-december-2020"></a>2.11.9 (3 grudnia 2020)

 - Ta wersja rozwiązuje kilka problemów z funkcją emulatora Azure Cosmos DB, a także ogólną aktualizację zawartości odzwierciedlającą najnowsze funkcje i ulepszenia w programie Azure Cosmos DB:
 * Rozwiązano problem polegający na tym, że żądania dotyczące dużego dokumentu nie powiodły się podczas korzystania z trybu bezpośredniego i aplikacji klienckich Java.
 * Rozwiązanie problemu z łącznością z punktem końcowym MongoDB w wersji 3,6, które są przeznaczone dla aplikacji opartych na platformie .NET.

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

- Ta wersja zapewnia parzystość z funkcjami usługi Cosmos platformy Azure od lipca 2019 z wyjątkami zanotowanymi w artykule [programowanie lokalne przy użyciu emulatora Azure Cosmos DB](local-emulator.md). Naprawia również kilka usterek odnoszących się do zamykania emulatora, gdy są wywoływane za pośrednictwem wiersza polecenia i wewnętrznych zastąpień adresów IP dla klientów zestawu SDK używających trybu bezpośredniego łączności.

### <a name="243"></a>zasadniczy

- Domyślnie wyłączone uruchamianie usługi MongoDB. Tylko punkt końcowy SQL jest domyślnie włączony. Użytkownik musi ręcznie uruchomić punkt końcowy przy użyciu opcji wiersza polecenia "/EnableMongoDbEndpoint" emulatora. Teraz podobnie jak w przypadku wszystkich innych punktów końcowych usługi, takich jak Gremlin, Cassandra i Table.
- Rozwiązano usterkę w emulatorze, gdy rozpoczyna się od "/AllowNetworkAccess", gdzie punkty końcowe Gremlin, Cassandra i Table nie obsługiwały prawidłowo żądań od klientów zewnętrznych.
- Dodaj bezpośrednie porty połączeń do ustawień reguł zapory.

### <a name="240"></a>2.4.0

- Rozwiązano problem z uruchomieniem emulatora, gdy na komputerze hosta znajdują się aplikacje do monitorowania sieci, na przykład klient pulsu.
