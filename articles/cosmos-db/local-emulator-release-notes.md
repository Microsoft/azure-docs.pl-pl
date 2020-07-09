---
title: Informacje o wersji i pobraniu emulatora usługi Azure Cosmos
description: Zapoznaj się z informacjami o wersji emulatora usługi Azure Cosmos dla różnych wersji i Pobierz informacje.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 12e1c79e610526dec11467cc08c753bf90daa095
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083461"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos emulator — informacje o wersji i pobrania

W tym artykule przedstawiono informacje o wersji emulatora usługi Azure Cosmos z listą aktualizacji funkcji, które zostały wprowadzone w poszczególnych wersjach. Znajduje się w nim również Najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Pobierz

| | |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Uwagi do wersji

### <a name="2112-07072020"></a>2.11.2 (07/07/2020)

- Ta wersja zmienia sposób, w jaki są zbierane ślady ETL podczas rozwiązywania problemów z emulatorem Cosmos. WP (narzędzia wydajności środowiska uruchomieniowego systemu Windows) są teraz domyślnymi narzędziami do przechwytywania śladów opartych na usłudze ETL, podczas gdy stary plik przechwytywania oparty na usłudze LOGMAN jest przestarzały. Ta zmiana jest wymagana w części, ponieważ najnowsze aktualizacje zabezpieczeń systemu Windows miały nieoczekiwany wpływ na to, jak program LOGMAN działa podczas wykonywania za pomocą emulatora Cosmos.

### <a name="2111-06102020"></a>2.11.1 (06/10/2020)

- W tej wersji rozwiązano kilka usterek związanych z Eksplorator danychami emulatora. W niektórych przypadkach w przypadku używania emulatora Eksplorator danych za pośrednictwem przeglądarki sieci Web nie można nawiązać połączenia z punktem końcowym emulatora usługi Cosmos i wszystkie powiązane akcje, takie jak tworzenie bazy danych lub kontenera, będą się kończyły niepowodzeniem. Drugi problem rozwiązany jest związany z tworzeniem elementu z pliku JSON przy użyciu akcji przekazywania Eksplorator danych.

### <a name="2110"></a>2.11.0

- W tej wersji wprowadzono obsługę przepływności dla aprowizacji automatyczne skalowanie. Te nowe funkcje obejmują możliwość ustawiania niestandardowego maksymalnego poziomu przepływności w jednostkach żądania (RU/s), włączania funkcji automatycznego skalowania w istniejących bazach danych i kontenerach oraz pomocy programistycznej za pomocą zestawów SDK Azure Cosmos DB.
- Usuń problem podczas wykonywania zapytania w dużej ilości dokumentów (ponad 1 GB), ponieważ emulator zakończy się niepowodzeniem z kodem stanu błędu wewnętrznego 500.

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

- Ta wersja zapewnia parzystość dzięki funkcjom w usłudze Azure Cosmos z lipca 2019 z wyjątkami zanotowanymi w artykule [opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md). Naprawia również kilka usterek związanych z zamknięciem emulatora, gdy są wywoływane za pomocą wiersza polecenia i wewnętrznych zastąpień adresów IP dla klientów zestawu SDK używających łączności w trybie bezpośrednim.

### <a name="243"></a>zasadniczy

- Domyślnie wyłączone uruchamianie usługi MongoDB. Tylko punkt końcowy SQL jest domyślnie włączony. Użytkownik musi ręcznie uruchomić punkt końcowy przy użyciu opcji wiersza polecenia "/EnableMongoDbEndpoint" emulatora. Teraz podobnie jak w przypadku wszystkich innych punktów końcowych usługi, takich jak Gremlin, Cassandra i Table.
- Rozwiązano usterkę w emulatorze, gdy rozpoczyna się od "/AllowNetworkAccess", gdzie punkty końcowe Gremlin, Cassandra i Table nie obsługiwały prawidłowo żądań od klientów zewnętrznych.
- Dodaj bezpośrednie porty połączeń do ustawień reguł zapory.

### <a name="240"></a>2.4.0

- Rozwiązano problem z uruchomieniem emulatora, gdy na komputerze hosta znajdują się aplikacje do monitorowania sieci, na przykład klient pulsu.
