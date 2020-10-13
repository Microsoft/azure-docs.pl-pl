---
title: Omówienie replikacji obiektów
titleSuffix: Azure Storage
description: Replikacja obiektów asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Użyj replikacji obiektów, aby zminimalizować opóźnienia w żądaniach odczytu, zwiększyć wydajność obciążeń obliczeniowych, zoptymalizować dystrybucję danych i zminimalizować koszty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4105698198e6fb7f4e3d3526ff9590ebca4898f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612170"
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

## <a name="object-replication-policies-and-rules"></a>Zasady i reguły replikacji obiektów

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji określające źródłowe konto magazynu i konto docelowe. Zasady replikacji obejmują co najmniej jedną regułę określającą kontener źródłowy i kontener docelowy i wskazujący, które blokowe obiekty blob w kontenerze źródłowym zostaną zreplikowane.

Po skonfigurowaniu replikacji obiektów usługa Azure Storage sprawdza źródło zmian na koncie źródłowym okresowo i asynchronicznie replikuje wszelkie operacje zapisu lub usuwania na koncie docelowym. Opóźnienie replikacji zależy od rozmiaru zreplikowanego zablokowanego obiektu BLOB.

> [!IMPORTANT]
> Ponieważ dane blokowych obiektów BLOB są replikowane asynchronicznie, konto źródłowe i docelowe nie są natychmiast synchronizowane. Obecnie nie ma umowy SLA dotyczącej tego, jak długo trwa replikowanie danych do konta docelowego.

### <a name="replication-policies"></a>Zasady replikacji

Podczas konfigurowania replikacji obiektów zasady replikacji są tworzone zarówno na koncie źródłowym, jak i na koncie docelowym za pośrednictwem dostawcy zasobów usługi Azure Storage. Zasady replikacji są identyfikowane przez identyfikator zasad. Zasady na kontach źródłowych i docelowych muszą mieć ten sam identyfikator zasad, aby replikacja była przeprowadzana.

Konto magazynu może stanowić konto źródłowe dla maksymalnie dwóch kont docelowych. Konta źródłowe i docelowe mogą znajdować się w tym samym regionie lub w różnych regionach. Mogą również znajdować się w różnych subskrypcjach i w różnych dzierżawach Azure Active Directory (Azure AD). Dla każdej pary kont źródłowych/docelowych można utworzyć tylko jedną zasadę replikacji.

### <a name="replication-rules"></a>Reguły replikacji

Reguły replikacji określają, w jaki sposób usługa Azure Storage będzie replikować obiekty blob z kontenera źródłowego do kontenera docelowego. Dla każdej zasady replikacji można określić maksymalnie 10 reguł replikacji. Każda reguła replikacji definiuje pojedynczy kontener źródłowy i docelowy, a każdy kontener źródłowy i docelowy może być używany tylko w jednej regule.

Podczas tworzenia reguły replikacji domyślnie kopiowane są tylko nowe blokowe obiekty blob, które są następnie dodawane do kontenera źródłowego. Można określić, że kopiowane są zarówno nowe, jak i istniejące blokowe obiekty blob, albo można zdefiniować niestandardowy zakres kopiowania, który kopiuje blokowe obiekty blob utworzone od określonego momentu.

Można również określić jeden lub więcej filtrów jako część reguły replikacji, aby odfiltrować blokowe obiekty blob według prefiksu. Po określeniu prefiksu tylko obiekty blob pasujące do tego prefiksu w kontenerze źródłowym zostaną skopiowane do kontenera docelowego.

Oba kontenery źródłowe i docelowe muszą istnieć przed określeniem ich w regule. Po utworzeniu zasad replikacji kontener docelowy jest tylko do odczytu. Wszystkie próby zapisu w kontenerze docelowym kończą się niepowodzeniem z kodem błędu 409 (konflikt). Można jednak wywołać operację [ustawiania warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier) na obiekcie BLOB w kontenerze docelowym, aby przenieść ją do warstwy archiwum. Aby uzyskać więcej informacji o warstwie archiwum, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Rozliczenia

Replikacja obiektów wiąże się z dodatkowymi kosztami dotyczącymi transakcji odczytu i zapisu na kontach źródłowych i docelowych, a także naliczanie opłat za replikację danych z konta źródłowego na konto docelowe oraz naliczane są opłaty za odczytanie do źródła zmian procesu.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie replikacji obiektów](object-replication-configure.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
