---
title: Raporty w Azure Monitor dla kontenerów
description: Opisuje raporty dostępne do analizowania danych zbieranych przez Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 94709cf4a8b579447c11f91f9bc6863ab4b4fe08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582307"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Raporty w Azure Monitor dla kontenerów
Raporty w Azure Monitor for Containers są zalecane dla wbudowanych [skoroszytów platformy Azure](../visualize/workbooks-overview.md). W tym artykule opisano różne raporty, które są dostępne, oraz sposób uzyskiwania do nich dostępu.

## <a name="viewing-reports"></a>Wyświetlanie raportów
Z menu **Azure monitor** w Azure Portal wybierz pozycję **Containers (kontenery**). Wybierz pozycję szczegółowe **informacje** w sekcji **monitorowanie** , wybierz konkretny klaster, a następnie wybierz stronę **raporty (wersja zapoznawcza)** . 

[![Strona raporty](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Tworzenie skoroszytu niestandardowego
Aby utworzyć niestandardowy skoroszyt oparty na dowolnym z tych skoroszytów, wybierz listę rozwijaną **Wyświetl skoroszyty** , a następnie **Przejdź do galerii AKS** w dolnej części listy rozwijanej. Aby uzyskać więcej informacji na temat skoroszytów i szablonów skoroszytów, zobacz [Azure monitor skoroszytów](../visualize/workbooks-overview.md) .

[![Galeria AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Skoroszyty węzłów

- **Pojemność dysku**: wykresy interaktywne użycie dysków dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Procent użycia dysku dla wszystkich dysków.
    - Wolne miejsce na dysku dla wszystkich dysków.
    - Siatka pokazująca dysk każdego węzła, jego procent zajętego miejsca, Trend procentu zajętego miejsca, wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy wiersz jest zaznaczony w tabeli, procent zajętego miejsca i wolnego miejsca na dysku (GiB) jest pokazywany poniżej wiersza.

- **We/wy dysku**: wykresy interaktywnego wykorzystania dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Dyskowe operacje we/wy są sumowane na wszystkich dyskach przez odczyt bajtów/s, liczba bajtów/s oraz trendy odczytu i zapisu bajtów/s.
    - Osiem wykresów wydajnościowych przedstawia kluczowe wskaźniki wydajności, które pomagają mierzyć i identyfikować wąskie gardła dyskowych operacji we/wy.

- **Procesor GPU**: interaktywne wykresy użycia procesora GPU dla każdego węzła klastra KUBERNETES z procesorem GPU.

## <a name="resource-monitoring-workbooks"></a>Skoroszyty monitorowania zasobów

- **Wdrożenia**: stan wdrożeń & skalowanie w poziomie poniżej (HPA), w tym niestandardowy hPa. 
  
- **Szczegóły obciążenia**: wykresy interaktywne pokazujące statystyki wydajności obciążeń dla przestrzeni nazw. Zawiera wiele kart:

  - Omówienie użycia procesora i pamięci według.
  - Stan "POD/kontener" pokazujący trend ponownego uruchomienia, Trend ponownego uruchomienia kontenera i stan kontenera dla zasobników.
  - Zdarzenia Kubernetes pokazujące podsumowanie zdarzeń dla kontrolera.

- **Kubelet**: obejmuje dwie siatki pokazujące statystyki operacyjne węzła klucza:

    - Przegląd według siatki węzła podsumowuje całkowitą operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu dla każdego węzła.
    - Przegląd według typu operacji podsumowuje dla każdej operacji łączną operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu.
## <a name="billing-workbooks"></a>Skoroszyty rozliczeń

- **Użycie danych**: pomaga wizualizować źródło danych bez konieczności kompilowania własnej biblioteki zapytań z tego, co udostępniamy w naszej dokumentacji. W tym skoroszycie znajdują się wykresy, za pomocą których można wyświetlić dane podlegające rozliczaniu z takich perspektyw, jak:

  - Łączne dane do rozliczenia pobrane w GB według rozwiązania
  - Dane do rozliczenia pobrane przez dzienniki kontenerów (Dzienniki aplikacji)
  - Rozliczane dane dzienników kontenerów pozyskiwane według przestrzeni nazw Kubernetes
  - Rozdzielone dane dzienników kontenera do rozliczenia według nazwy klastra
  - Rozliczane dane dziennika kontenerów pobrane przez wpis logsource
  - Rozliczane dane diagnostyczne pobrane przez dzienniki diagnostyki węzłów głównych

## <a name="networking-workbooks"></a>Skoroszyty sieciowe

- **Konfiguracja npm**: monitorowanie konfiguracji sieci skonfigurowanych za poorednictwem programu Network Policy Manager (npm).

  - Podsumowanie informacji o ogólnej złożoności konfiguracji.
  - Zasady, reguły i zestawy są liczone z upływem czasu, umożliwiając wgląd w relacje między trzema i Dodawanie wymiaru czasu na potrzeby debugowania konfiguracji.
  - Liczba wpisów we wszystkich IPSets i każdy IPSet.
  - Najgorszy i średnią wydajność wielkości liter na węzeł w celu dodania składników do konfiguracji sieci.

- **Sieć**: wykresy interaktywnego wykorzystania sieci dla każdej karty sieciowej każdego węzła i siatka przedstawiają kluczowe wskaźniki wydajności, które ułatwiają mierzenie wydajności kart sieciowych.



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje o skoroszytach w Azure Monitor, zobacz [Azure monitor skoroszytów](../visualize/workbooks-overview.md) .
