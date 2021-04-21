---
title: Monitorowanie Azure File Sync warstw w chmurze | Microsoft Docs
description: Szczegółowe informacje na temat metryk do monitorowania zasad warstw w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796863"
---
# <a name="monitor-cloud-tiering"></a>Monitorowanie warstw w chmurze
Istnieją dwa sposoby monitorowania zasad warstw w chmurze: blok właściwości punktu końcowego serwera i Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Monitorowanie za pośrednictwem punktu końcowego serwera

Zaloguj się do Azure Portal [,](https://portal.azure.com/)a następnie  przejdź do bloku właściwości punktu końcowego serwera dla punktu końcowego serwera, który chcesz monitorować, i przewiń w dół do sekcji dotyczącej warstw w chmurze. 

![Zrzut ekranu przedstawiający sekcję warstw w chmurze we właściwościach punktu końcowego serwera.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Oszczędność miejsca to** oszczędność miejsca dzięki włączeniu warstw w chmurze. Jeśli rozmiar woluminu lokalnego jest wystarczająco duży, aby pomieścić wszystkie dane, można zaoszczędzić 0%. Jeśli masz 0% lub niski procent oszczędności miejsca, może to wskazywać, że warstwy w chmurze nie są korzystne w przypadku bieżącego rozmiaru lokalnej pamięci podręcznej. 

**Szybkość trafień w pamięci** podręcznej to procent plików otwieranych w lokalnej pamięci podręcznej. Szybkość trafień w pamięci podręcznej jest obliczana przez pliki otwierane bezpośrednio z pamięci podręcznej/całkowitą liczbę otwartych plików. Wskaźnik trafień pamięci podręcznej wynosi 100%, co oznacza, że wszystkie pliki otwarte w ciągu ostatniej godziny były już w lokalnej pamięci podręcznej. Jeśli Twoim celem jest zmniejszenie ilości danych wychodzących, oznacza to, że bieżące zasady działają dobrze.

> [!NOTE]
> Obciążenia z wzorcami dostępu losowego zwykle mają niższy wskaźnik trafień w pamięci podręcznej. 

**Łączny rozmiar (w chmurze)** to rozmiar plików, które zostały zsynchronizowane z chmurą. 

**Rozmiar buforowany (serwer)** to łączny rozmiar plików na serwerze, zarówno pobranych, jak i warstwowych. Czasami rozmiar pamięci podręcznej może być większy niż całkowity rozmiar plików w chmurze. Mogą to powodować zmienne, takie jak rozmiar klastra woluminu na serwerze. Jeśli rozmiar pamięci podręcznej jest większy niż chcesz, rozważ zwiększenie zasad wolnego miejsca na woluminie. 

**Zasady efektywnego woluminu** są używane przez Azure File Sync, aby określić ilość wolnego miejsca do pozostawienie na woluminie, na którym znajduje się punkt końcowy serwera. Jeśli na tym samym woluminie znajduje się wiele punktów końcowych serwera, zasady dotyczące największej ilości wolnego miejsca wśród punktów końcowych serwera stają się efektywnymi zasadami woluminu dla wszystkich punktów końcowych serwera na tym woluminie. Jeśli na przykład na tym samym woluminie znajdują się dwa punkty końcowe serwera, jeden z 30% wolnego miejsca na woluminie i drugi z 50% wolnego miejsca na woluminie, efektywne zasady wolnego miejsca dla obu punktów końcowych serwera będą mieć 50%.

**Ilość wolnego miejsca w bieżącym** woluminie to ilość wolnego miejsca dostępna obecnie na serwerze lokalnym. Jeśli przed rozpoczęciem zasad wolnego miejsca na woluminie jest dostępnych dużo wolnego miejsca, ale jest dostępnych dużo wolnego miejsca, rozważ wyłączenie zasad daty. Innym problemem może być to, że z obecnie plików warstwowych ostatnio używanych plików jest większy niż ilość wolnego miejsca na woluminie, która pozostała przed rozpoczęciem zasad. W takim przypadku rozważ zwiększenie rozmiaru woluminu lokalnego. 

## <a name="monitoring-via-azure-monitor"></a>Monitorowanie za pośrednictwem Azure Monitor

Przejdź do usługi **synchronizacji magazynu i** wybierz pozycję **Metryki** na bocznym pasku nawigacyjnym. 

Istnieją trzy różne metryki, których można użyć do monitorowania danych wychodzących w szczególności z poziomu warstw w chmurze:

- Rozmiar odwołań warstw w chmurze
    - Jest to łączny rozmiar danych przywołynych w bajtach i może służyć do identyfikowania, ile danych jest przywoływynych.
- Rozmiar odwołań warstw w chmurze według aplikacji
    - Jest to łączny rozmiar danych przywołynych w bajtach przez aplikację i może służyć do identyfikowania, co przywołuje dane.
- Przepływność odwoływania do warstwy w chmurze
    - Mierzy to, jak szybko dane są przywoływyne w bajtach i powinny być używane, jeśli masz obawy dotyczące wydajności. 

Aby dokładniej o tym, co mają być wyświetlane wykresy, rozważ użycie funkcji **Dodaj filtr** i **Zastosuj podział.**
 
Aby uzyskać szczegółowe informacje na temat różnych typów metryk dla Azure File Sync i sposobu ich używania, zobacz [Monitorowanie Azure File Sync](file-sync-monitoring.md).

Aby uzyskać szczegółowe informacje na temat używania metryk, zobacz [Wprowadzenie do usługi Azure Eksplorator metryk.](../../azure-monitor/essentials/metrics-getting-started.md).

Jeśli chcesz zmienić zasady warstw w chmurze, zobacz [Choose cloud tiering policies (Wybieranie zasad warstw w chmurze).](file-sync-choose-cloud-tiering-policies.md)

## <a name="next-steps"></a>Następne kroki

* [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)