---
title: Omówienie replikacji obiektów
titleSuffix: Azure Storage
description: Replikacja obiektów asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Użyj replikacji obiektów, aby zminimalizować opóźnienia w żądaniach odczytu, zwiększyć wydajność obciążeń obliczeniowych, zoptymalizować dystrybucję danych i zminimalizować koszty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7fa6b1ee7c92f82c3e15335991f5a240c7acc52
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762880"
---
# <a name="object-replication-for-block-blobs"></a>Replikacja obiektów dla blokowych obiektów BLOB

Replikacja obiektów asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Niektóre scenariusze obsługiwane przez replikację obiektów obejmują:

- **Minimalizacja opóźnień.** Replikacja obiektów może zmniejszyć opóźnienia żądań odczytu przez umożliwienie klientom korzystania z danych z regionu, który znajduje się bliżej fizycznej bliskości.
- **Zwiększenie wydajności obciążeń obliczeniowych.** W przypadku replikacji obiektów obciążenia obliczeniowe mogą przetwarzać te same zestawy blokowych obiektów BLOB w różnych regionach.
- **Optymalizacja dystrybucji danych.** Dane można przetwarzać lub analizować w jednej lokalizacji, a następnie replikować tylko wyniki do dodatkowych regionów.
- **Optymalizowanie kosztów.** Po zreplikowaniu danych możesz obniżyć koszty, przenosząc je do warstwy archiwum, korzystając z zasad zarządzania cyklem życia.

Na poniższym diagramie pokazano, jak replikacja obiektów replikuje blokowe obiekty blob z konta magazynu źródłowego w jednym regionie do kont docelowych w dwóch różnych regionach.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram przedstawiający sposób działania replikacji obiektów":::

Aby dowiedzieć się, jak skonfigurować replikację obiektów, zobacz [Konfigurowanie replikacji obiektów](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Wymagania wstępne dotyczące replikacji obiektów

Replikacja obiektów wymaga również włączenia następujących funkcji usługi Azure Storage:

- [Źródło zmian](storage-blob-change-feed.md): musi być włączone na koncie źródłowym. Aby dowiedzieć się, jak włączyć Źródło zmian, zobacz [Włączanie i wyłączanie kanału informacyjnego zmian](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Wersja obiektów BLOB](versioning-overview.md): musi być włączona zarówno na koncie źródłowym, jak i docelowym. Aby dowiedzieć się, jak włączyć obsługę wersji, zobacz Włączanie obsługi wersji [obiektów blob i zarządzanie nimi](versioning-enable.md).

Włączenie kanału informacyjnego zmiany i przechowywanie wersji obiektów BLOB może pociągnąć za sobą dodatkowe koszty. Więcej informacji można znaleźć na [stronie cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-object-replication-works"></a>Jak działa replikacja obiektów

Replikacja obiektów asynchronicznie kopiuje blokowe obiekty blob w kontenerze zgodnie z skonfigurowanymi regułami. Zawartość obiektu BLOB, wszystkie wersje skojarzone z obiektem BLOB, a metadane i właściwości obiektu BLOB są kopiowane z kontenera źródłowego do kontenera docelowego.

> [!IMPORTANT]
> Ponieważ dane blokowych obiektów BLOB są replikowane asynchronicznie, konto źródłowe i docelowe nie są natychmiast synchronizowane. Obecnie nie ma umowy SLA dotyczącej tego, jak długo trwa replikowanie danych do konta docelowego. Aby określić, czy replikacja została ukończona, można sprawdzić stan replikacji w źródłowym obiekcie blob. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu replikacji obiektu BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Przechowywanie wersji obiektów BLOB

Replikacja obiektów wymaga włączenia obsługi wersji obiektów BLOB na kontach źródłowych i docelowych. Po zmodyfikowaniu zreplikowanego obiektu BLOB na koncie źródłowym na koncie źródłowym zostanie utworzona nowa wersja obiektu BLOB, która odzwierciedla poprzedni stan obiektu BLOB przed modyfikacją. Bieżąca wersja (lub podstawowy obiekt BLOB) na koncie źródłowym odzwierciedla najnowsze aktualizacje. Zaktualizowana bieżąca wersja i Nowa Poprzednia wersja są replikowane na konto docelowe. Aby uzyskać więcej informacji na temat sposobu, w jaki operacje zapisu wpływają na wersje obiektów blob, zobacz [przechowywanie wersji na potrzeby operacji zapisu](versioning-overview.md#versioning-on-write-operations).

Po usunięciu obiektu BLOB na koncie źródłowym bieżąca wersja obiektu BLOB jest przechwytywana w poprzedniej wersji, a następnie usuwana. Wszystkie poprzednie wersje obiektu BLOB są zachowywane nawet po usunięciu bieżącej wersji. Ten stan jest replikowany na konto docelowe. Aby uzyskać więcej informacji na temat sposobu, w jaki operacje usuwania wpływają na wersje obiektów blob, zobacz [przechowywanie wersji przy operacjach usuwania](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Migawki

Replikacja obiektów nie obsługuje migawek obiektów BLOB. Wszystkie migawki obiektu BLOB na koncie źródłowym nie są replikowane na konto docelowe.

### <a name="blob-tiering"></a>Obsługa warstw obiektów BLOB

Replikacja obiektów jest obsługiwana, gdy konta źródłowe i docelowe znajdują się w warstwie gorąca lub chłodna. Konta źródłowe i docelowe mogą znajdować się w różnych warstwach. Jednak replikacja obiektów zakończy się niepowodzeniem, jeśli obiekt BLOB na koncie źródłowym lub docelowym został przeniesiony do warstwy archiwum. Aby uzyskać więcej informacji na temat warstw obiektów blob, zobacz [warstwy dostępu dla platformy Azure Blob Storage — gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

### <a name="immutable-blobs"></a>Niezmienne obiekty blob

Replikacja obiektów nie obsługuje niemodyfikowalnych obiektów BLOB. Jeśli kontener źródłowy lub docelowy ma zasady przechowywania oparte na czasie lub blokadę prawną, replikacja obiektów kończy się niepowodzeniem. Aby uzyskać więcej informacji na temat niezmiennych obiektów blob, zobacz temat [przechowywanie danych obiektów BLOB o kluczowym znaczeniu w niezmiennym magazynie](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Zasady i reguły replikacji obiektów

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji określające źródłowe konto magazynu i konto docelowe. Zasady replikacji obejmują co najmniej jedną regułę określającą kontener źródłowy i kontener docelowy i wskazujący, które blokowe obiekty blob w kontenerze źródłowym zostaną zreplikowane.

Po skonfigurowaniu replikacji obiektów usługa Azure Storage sprawdza źródło zmian na koncie źródłowym okresowo i asynchronicznie replikuje wszelkie operacje zapisu lub usuwania na koncie docelowym. Opóźnienie replikacji zależy od rozmiaru zreplikowanego zablokowanego obiektu BLOB.

### <a name="replication-policies"></a>Zasady replikacji

Podczas konfigurowania replikacji obiektów zasady replikacji są tworzone zarówno na koncie źródłowym, jak i na koncie docelowym za pośrednictwem dostawcy zasobów usługi Azure Storage. Zasady replikacji są identyfikowane przez identyfikator zasad. Zasady na kontach źródłowych i docelowych muszą mieć ten sam identyfikator zasad, aby replikacja była przeprowadzana.

Konto magazynu może stanowić konto źródłowe dla maksymalnie dwóch kont docelowych. Konta źródłowe i docelowe mogą znajdować się w tym samym regionie lub w różnych regionach. Mogą również znajdować się w różnych subskrypcjach i w różnych dzierżawach Azure Active Directory (Azure AD). Dla każdej pary kont źródłowych/docelowych można utworzyć tylko jedną zasadę replikacji.

### <a name="replication-rules"></a>Reguły replikacji

Reguły replikacji określają, w jaki sposób usługa Azure Storage będzie replikować obiekty blob z kontenera źródłowego do kontenera docelowego. Dla każdej zasady replikacji można określić maksymalnie 10 reguł replikacji. Każda reguła replikacji definiuje pojedynczy kontener źródłowy i docelowy, a każdy kontener źródłowy i docelowy może być używany tylko w jednej regule.

Podczas tworzenia reguły replikacji domyślnie kopiowane są tylko nowe blokowe obiekty blob, które są następnie dodawane do kontenera źródłowego. Można określić, że kopiowane są zarówno nowe, jak i istniejące blokowe obiekty blob, albo można zdefiniować niestandardowy zakres kopiowania, który kopiuje blokowe obiekty blob utworzone od określonego momentu.

Można również określić jeden lub więcej filtrów jako część reguły replikacji, aby odfiltrować blokowe obiekty blob według prefiksu. Po określeniu prefiksu tylko obiekty blob pasujące do tego prefiksu w kontenerze źródłowym zostaną skopiowane do kontenera docelowego.

Oba kontenery źródłowe i docelowe muszą istnieć przed określeniem ich w regule. Po utworzeniu zasad replikacji operacje zapisu w kontenerze docelowym są niedozwolone. Wszystkie próby zapisu w kontenerze docelowym kończą się niepowodzeniem z kodem błędu 409 (konflikt). Aby zapisać do kontenera docelowego, dla którego skonfigurowano regułę replikacji, należy usunąć regułę skonfigurowaną dla tego kontenera lub usunąć zasady replikacji. Operacje odczytu i usuwania do kontenera docelowego są dozwolone, gdy zasady replikacji są aktywne.

Możesz wywołać operację [ustawiania warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier) na obiekcie BLOB w kontenerze docelowym, aby przenieść ją do warstwy archiwum. Aby uzyskać więcej informacji o warstwie archiwum, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="replication-status"></a>Stan replikacji

Stan replikacji obiektu BLOB można sprawdzić na koncie źródłowym. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu replikacji obiektu BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

Jeśli stan replikacji obiektu BLOB na koncie źródłowym wskazuje błąd, zbadaj następujące możliwe przyczyny:

- Upewnij się, że na koncie docelowym są skonfigurowane zasady replikacji obiektów.
- Sprawdź, czy kontener docelowy nadal istnieje.
- Jeśli źródłowy obiekt BLOB został zaszyfrowany za pomocą klucza dostarczonego przez klienta w ramach operacji zapisu, replikacja obiektów zakończy się niepowodzeniem. Więcej informacji o kluczach dostarczonych przez klienta znajduje się [w temacie zapewnianie klucza szyfrowania w żądaniu usługi BLOB Storage](encryption-customer-provided-keys.md).

## <a name="billing"></a>Rozliczenia

Replikacja obiektów wiąże się z dodatkowymi kosztami dotyczącymi transakcji odczytu i zapisu na kontach źródłowych i docelowych, a także naliczanie opłat za replikację danych z konta źródłowego na konto docelowe oraz naliczane są opłaty za odczytanie do źródła zmian procesu.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie replikacji obiektów](object-replication-configure.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
