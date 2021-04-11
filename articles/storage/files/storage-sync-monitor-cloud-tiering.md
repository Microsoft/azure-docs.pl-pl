---
title: Monitorowanie Azure File Sync warstw chmury | Microsoft Docs
description: Szczegóły dotyczące metryk używanych do monitorowania zasad obsługi warstw w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593139"
---
# <a name="monitor-cloud-tiering"></a>Monitorowanie warstw chmury
Istnieją dwa sposoby monitorowania zasad obsługi warstw w chmurze: blok właściwości punktu końcowego serwera i Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Monitorowanie za pośrednictwem punktu końcowego serwera

Zaloguj się do [Azure Portal](https://portal.azure.com/), a następnie przejdź do bloku **właściwości punktu końcowego serwera** dla punktu końcowego serwera, który chcesz monitorować, a następnie przewiń w dół do sekcji Obsługa warstw chmury. 

![Zrzut ekranu przedstawiający sekcję Obsługa warstw w chmurze we właściwościach punktu końcowego serwera.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Oszczędność miejsca** to ilość miejsca zapisywanych przez włączenie obsługi warstw w chmurze. Jeśli rozmiar lokalnego woluminu jest wystarczająco duży, aby pomieścić wszystkie dane, masz 0% oszczędności miejsca. Jeśli masz 0% lub mniej procent oszczędności miejsca, może to wskazywać, że obsługa warstw w chmurze nie jest korzystna dla bieżącego lokalnego rozmiaru pamięci podręcznej. 

**Współczynnik trafień pamięci podręcznej** to procent plików, które są otwierane w lokalnej pamięci podręcznej. Współczynnik trafień pamięci podręcznej jest obliczany przez pliki otwierane bezpośrednio z pamięci podręcznej/łącznej liczby otwartych plików. Współczynnik trafień pamięci podręcznej wynoszący 100% oznacza, że wszystkie pliki otwarte w ciągu ostatniej godziny znajdowały się już w lokalnej pamięci podręcznej. Jeśli chcesz zmniejszyć ruch wychodzący, oznacza to, że bieżące zasady działają prawidłowo.

> [!NOTE]
> Obciążenia ze wzorcami dostępu losowego zwykle mają niższą szybkość trafień pamięci podręcznej. 

**Łączny rozmiar (w chmurze)** to rozmiar plików, które zostały zsynchronizowane z chmurą. 

**Rozmiar pamięci podręcznej (serwer)** to łączny rozmiar plików na serwerze, pobieranych i warstwowych. Czasami rozmiar pamięci podręcznej może być większy niż łączny rozmiar plików w chmurze. Mogą to być zmienne, takie jak rozmiar klastra woluminu na serwerze. Jeśli rozmiar pamięci podręcznej jest większy niż chcesz, rozważ zwiększenie zasad wolnego miejsca na woluminie. 

**Efektywne zasady woluminu** są używane przez Azure File Sync, aby określić ilość wolnego miejsca na woluminie, na którym znajduje się punkt końcowy serwera. W przypadku wielu punktów końcowych serwera na tym samym woluminie, najwyższe ilości wolnego miejsca między punktami końcowymi serwera staną się obowiązującymi zasadami woluminu dla wszystkich punktów końcowych serwera w tym woluminie. Na przykład, jeśli na tym samym woluminie znajdują się dwa punkty końcowe serwera, jeden z 30% wolnego miejsca na woluminie z ilością wolnego miejsca na 50%, obowiązujące zasady wolnego miejsca na woluminie dla obu punktów końcowych serwera będą 50%.

**Bieżące wolne miejsce na woluminie** to wolne miejsce na woluminie dostępne na serwerze lokalnym. Jeśli masz duże ilości danych wyjściowych, ale więcej wolnego miejsca na woluminie dostępne przed rozpoczęciem uruchamiania zasad wolnego miejsca na woluminie, rozważ wyłączenie zasad dotyczących daty. Innym problemem może być brak plików warstwowych, a ostatnio używany plik jest większy niż ilość wolnego miejsca na woluminie pozostałej przed rozpoczęciem działania zasad. W takim przypadku należy rozważyć zwiększenie rozmiaru woluminu lokalnego. 

## <a name="monitoring-via-azure-monitor"></a>Monitorowanie za pośrednictwem Azure Monitor

Przejdź do **usługi synchronizacji magazynu** i wybierz pozycję **metryki** po nawigacji po stronie. 

Istnieją trzy różne metryki, których można użyć do monitorowania ruchu wychodzącego w ramach obsługi warstw w chmurze:

- Rozmiar odwołania do warstw w chmurze
    - Jest to łączny rozmiar danych, które zostały odwołane w bajtach i mogą służyć do identyfikowania ilości danych, które są wywoływane.
- Rozmiar odwołania do warstw w chmurze według aplikacji
    - Jest to całkowity rozmiar danych, które zostały odbyte w bajtach przez aplikację i można go użyć do zidentyfikowania, co spowoduje oddzwonienie danych.
- Przepływność odwołań do warstw w chmurze
    - To mierzy, jak szybko dane są wywoływane w bajtach i powinny być używane, jeśli masz problemy z wydajnością. 

Aby uzyskać bardziej szczegółowe informacje na temat tego, co mają być wyświetlane na wykresach, należy rozważyć użycie opcji **Dodaj filtr** i **Zastosuj podział**.
 
Aby uzyskać szczegółowe informacje na temat różnych typów metryk dla Azure File Sync i sposobu ich używania, zobacz [Azure File Sync monitorowania](storage-sync-files-monitoring.md).

Aby uzyskać szczegółowe informacje na temat korzystania z metryk, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk.](../../azure-monitor/essentials/metrics-getting-started.md).

Jeśli chcesz zmienić zasady dotyczące warstw w chmurze, zobacz [Wybieranie zasad obsługi warstw w chmurze](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)