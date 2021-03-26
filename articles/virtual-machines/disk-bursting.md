---
title: Zarządzanie dyskami zarządzanymi
description: Dowiedz się więcej na temat obciążeń dysków dla dysków platformy Azure i maszyn wirtualnych platformy Azure.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568719"
---
# <a name="managed-disk-bursting"></a>Zarządzanie dyskami zarządzanymi
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Usługa Azure [Premium dysków SSD](disks-types.md#premium-ssd) oferuje dwa modele rozruchowe:

- Model serii na żądanie (wersja zapoznawcza), w którym dyski są w miarę potrzeb przekraczają bieżącą pojemność. Ten model wiąże się z dodatkowymi opłatami za każde miejsce na dysku. Na dyskach o rozmiarze większym niż 512 GiB jest dostępna wyłącznie seria niekredytowa.
- Model oparty na kredytach, w którym dysk będzie przerastał się tylko wtedy, gdy ma on środki na korzystanie z serii w swoim zasobniku kredytowym. Ten model nie wiąże się z dodatkowymi opłatami w przypadku obciążeń dysków. Rozliczanie oparte na kredycie jest dostępne tylko na dyskach 512 GiB i mniejszych.

Dodatkowo [można zmienić warstwę wydajności dysków zarządzanych](disks-change-performance.md), co może być idealnym rozwiązaniem, jeśli obciążenie w przeciwnym razie będzie działać na rozerwanie.

|  |Korzystanie z rozliczeń opartych na kredycie  |Szeregowanie na żądanie  |Zmiana warstwy wydajności  |
|---------|---------|---------|---------|
| **Scenariusze**|Idealne rozwiązanie do krótkoterminowego skalowania (30 minut lub mniej).|Idealny do krótkoterminowego skalowania (bez ograniczeń czasowych).|Idealne rozwiązanie w przypadku ciągłego uruchamiania obciążenia w ramach serii.|
|**Koszty**     |Bezpłatna         |Koszt jest zmienny, zobacz sekcję [rozliczenia](#billing) , aby uzyskać szczegółowe informacje.        |Koszt każdej warstwy wydajności jest ustalony, zobacz [Managed disks cennika](https://azure.microsoft.com/pricing/details/managed-disks/) , aby uzyskać szczegółowe informacje.         |
|**Dostępność**     |Dostępne tylko dla warstwy Premium dysków SSD 512 GiB i mniejszej.         |Dostępne tylko dla dysków SSD Premium o rozmiarze większym niż 512 GiB.         |Dostępne dla wszystkich rozmiarów dysków SSD w warstwie Premium.         |
|**Włączanie**     |Domyślnie włączone na kwalifikujących się dyskach.         |Musi być włączona przez użytkownika.         |Użytkownik musi ręcznie zmienić warstwę.         |

## <a name="common-scenarios"></a>Typowe scenariusze
Poniższe scenariusze mogą znacznie wzczerpać korzyści z rozszeregowania:
- **Skrócenie czasu uruchamiania**  — dzięki rozbiciem wystąpienie będzie uruchamiane znacznie szybciej. Na przykład domyślnym dyskiem systemu operacyjnego dla maszyn wirtualnych z obsługą Premium jest dysk P4, który zapewnia wydajność o pojemności do 120 operacji we/wy i 25 MB/s. Dzięki rozruchom P4 można przystąpić do 3500 operacji we/wy na sekundę i 170 MB/s, co pozwala na szybkie przyspieszenie w 6X.
- **Obsługa zadań wsadowych** — niektóre obciążenia aplikacji mają charakter cykliczny. Wymagają one wydajności linii bazowej w większości czasu i wyższej wydajności przez krótki okres czasu. Przykładem jest program księgowy, który przetwarza dzienne transakcje, które wymagają niewielkiego natężenia ruchu na dysku. Na koniec miesiąca ten program wykona uzgadnianie raportów, które potrzebują znacznie większej ilości ruchu na dysku.
- **Skoki ruchu** — serwery sieci Web i ich aplikacje mogą powodować wzrosty ruchu w dowolnym momencie. Jeśli serwer sieci Web jest obsługiwany przez maszyny wirtualne lub dyski używające tworzenia serii, serwery byłyby lepiej wyposażone w program obsługujący skoki ruchu. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Następne kroki

Aby włączyć korzystanie z serii na żądanie, zobacz [Włączanie obsługi serii na żądanie](disks-enable-bursting.md).
Aby dowiedzieć się, jak uzyskać wgląd w zasoby dotyczące zasobów, zobacz [metryki](disks-metrics.md)dotyczące tworzenia dysków.
