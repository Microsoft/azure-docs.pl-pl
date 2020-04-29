---
title: Informacje o wersji i pobraniu emulatora usługi Azure Cosmos
description: Zapoznaj się z informacjami o wersji emulatora usługi Azure Cosmos dla różnych wersji i Pobierz informacje.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77168650"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos emulator — informacje o wersji i pobrania

W tym artykule przedstawiono informacje o wersji emulatora usługi Azure Cosmos z listą aktualizacji funkcji, które zostały wprowadzone w poszczególnych wersjach. Znajduje się w nim również Najnowsza wersja emulatora do pobrania i użycia.

## <a name="download"></a>Pliki do pobrania

| | |
|---------|---------|
|**Pobieranie pliku MSI**|[Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Wprowadzenie**|[Opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Informacje o wersji

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
