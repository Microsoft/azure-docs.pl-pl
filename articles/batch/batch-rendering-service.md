---
title: Przegląd renderowania
description: Wprowadzenie do renderowania przy użyciu platformy Azure i przegląd możliwości renderowania Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232118"
---
# <a name="rendering-using-azure"></a>Renderowanie przy użyciu platformy Azure

Renderowanie jest procesem tworzenia modeli 3D i konwertowania ich na obrazy 2D. Pliki sceny 3W są tworzone w aplikacjach, takich jak Autodesk 3ds Max, Autodesk Maya i Blender.  Renderowanie aplikacji, takich jak Autodesk Maya, Autodesk Arnold, chaos Group V-ray oraz cykle programu Blender, tworzy obrazy 2D.  Czasami pojedyncze obrazy są tworzone na podstawie plików sceny. Jednak często można modelować i renderować wiele obrazów, a następnie połączyć je w animacji.

Obciążenie renderowania jest intensywnie używane w przypadku efektów specjalnych (VFX) w branży multimedialnej i rozrywkowej. Renderowanie jest również używane w wielu innych sektorach, takich jak reklama, produkcja, handel detaliczny oraz przemysł naftowy.

Proces renderowania ma duże obciążenie; może być wiele ramek/obrazów do wyprodukowania, a każdy obraz może trwać wiele godzin.  Renderowanie jest dlatego idealnym obciążeniem przetwarzania wsadowego, które może wykorzystać platformę Azure do uruchamiania wielu renderowanych równolegle i korzystać z szerokiego zakresu sprzętu, w tym procesorów GPU.

## <a name="why-use-azure-for-rendering"></a>Dlaczego warto używać platformy Azure do renderowania?

Z wielu powodów renderowanie jest doskonale dopasowane do platformy Azure:

* Zadania renderowania można podzielić na wiele kawałków, które mogą być uruchamiane równolegle przy użyciu wielu maszyn wirtualnych:
  * Animacje składają się z wielu ramek, a każda ramka może być renderowana równolegle.  Im więcej maszyn wirtualnych jest dostępnych do przetwarzania każdej ramki, tym szybsze są wszystkie ramki i animacje.
  * W przypadku niektórych programów do renderowania można podzielić pojedyncze klatki na wiele elementów, takich jak kafelki lub wycinki.  Każdy element może być renderowany oddzielnie, a następnie połączony z końcowym obrazem po zakończeniu wszystkich fragmentów.  Im więcej dostępnych maszyn wirtualnych, tym szybciej można renderować klatkę.
* Renderowanie projektów może wymagać dużej skali:
  * Pojedyncze ramki mogą być złożone i wymagać wielu godzin do renderowania, nawet w przypadku sprzętu wysokiej klasy. animacje mogą składać się z setek tysięcy ramek.  Duża ilość obliczeń jest wymagana do renderowania wysokiej jakości animacji w rozsądnym czasie.  W niektórych przypadkach ponad 100 000 rdzeni zostało użytych do równoczesnego renderowania tysięcy klatek.
* Renderowanie projektów odbywa się na podstawie projektu i wymaga różnych ilości obliczeń:
  * Przydzielaj pojemności obliczeniowej i magazynu w razie potrzeby, Skaluj je w górę lub w dół zgodnie z obciążeniem w trakcie projektu i usuń je po zakończeniu projektu.
  * Płatność za pojemność po przydzieleniu, ale nie płatność za nią, gdy nie ma żadnych obciążeń, takich jak między projektami.
  * Skorzystaj z serii ze względu na nieoczekiwane zmiany; Skalowanie wyższe, jeśli w projekcie istnieją nieoczekiwane zmiany, a te zmiany muszą zostać przetworzone zgodnie z ścisłym harmonogramem.
* Wybieraj spośród szerokiego wyboru sprzętu w zależności od aplikacji, obciążenia i przedziału czasu:
  * Istnieje szeroki wybór sprzętu dostępnego na platformie Azure, który można przydzielić i zarządzać nimi za pomocą usługi Batch.
  * W zależności od projektu wymagania mogą dotyczyć najlepszej ceny/wydajności lub najlepszej ogólnej wydajności.  Inne sceny i/lub renderowanie aplikacji będą mieć różne wymagania dotyczące pamięci.  Niektóre aplikacje renderowania mogą korzystać z procesorów GPU w celu uzyskania najlepszej wydajności lub niektórych funkcji. 
* [Maszyny wirtualne](https://azure.microsoft.com/pricing/spot/) o niskim priorytecie lub na miejscu obniżają koszty:
  * Maszyny wirtualne o niskim priorytecie i miejscu są dostępne dla dużego rabatu w porównaniu do standardowych maszyn wirtualnych i są odpowiednie dla niektórych typów zadań.
  
## <a name="existing-on-premises-rendering-environment"></a>Istniejące lokalne środowisko renderowania

Najpopularniejszym przypadkiem jest istnienie istniejącej lokalnej farmy renderowania, która jest zarządzana przez aplikację do zarządzania renderowaniem, taką jak PipelineFX Qube, do renderowania, Thinkbox, ostateczny termin lub aplikacja niestandardowa.  Wymaganie ma na celu zwiększenie pojemności lokalnej farmy renderowania przy użyciu maszyn wirtualnych platformy Azure.

Infrastruktura i usługi platformy Azure są używane do tworzenia środowiska hybrydowego, w którym platforma Azure jest używana do uzupełniania pojemności lokalnej. Na przykład:

* Użyj [Virtual Network](../virtual-network/virtual-networks-overview.md) , aby umieścić zasoby platformy Azure w tej samej sieci co lokalna Farma renderowania.
* Użyj [avere vFXT dla platformy Azure](../avere-vfxt/avere-vfxt-overview.md) lub [pamięci podręcznej platformy Azure HPC](../hpc-cache/hpc-cache-overview.md) do buforowania plików źródłowych na platformie Azure, aby zmniejszyć wykorzystanie przepustowości i opóźnienia, maksymalizując wydajność.
* Upewnij się, że istniejący serwer licencji znajduje się w sieci wirtualnej i Kup dodatkowe licencje wymagane do zapewnienia dodatkowej pojemności opartej na platformie Azure.

## <a name="no-existing-render-farm"></a>Brak istniejącej farmy renderowania

Na stacjach roboczych klienta mogą być wykonywane operacje renderowania, ale obciążenie renderowania jest zwiększane i trwa zbyt długo, aby korzystać wyłącznie z pojemności stacji roboczej.

Dostępne są dwie główne opcje:

* Wdrożenie lokalnego Menedżera renderowania, takiego jak renderowanie sprawności i skonfigurowanie środowiska hybrydowego do korzystania z platformy Azure, gdy jest wymagana dodatkowa pojemność lub wydajność. Menedżer renderowania jest specjalnie dostosowany do renderowania obciążeń i obejmuje wtyczki dla popularnych aplikacji klienckich, co umożliwia łatwe przesyłanie zadań renderowania.

* Niestandardowe rozwiązanie przy użyciu Azure Batch do przydzielania pojemności obliczeniowej i zarządzania nią, a także do wykonywania zadań renderowania przez planowanie zadań.

## <a name="next-steps"></a>Następne kroki

 Dowiedz się [, jak korzystać z infrastruktury i usług platformy Azure w celu zwiększenia istniejącej lokalnej farmy renderowania](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Dowiedz się więcej o [możliwościach renderowania Azure Batch](batch-rendering-functionality.md).
