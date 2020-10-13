---
title: Monitorowanie urządzenia Azure FXT Edge Filer
description: Jak monitorować stan sprzętu dla hybrydowej pamięci podręcznej magazynu usługi Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 7027fe9988c0c559db72c3c388c7a579d533c57e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85509442"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorowanie stanu sprzętu usługi Azure FXT Edge

Hybrydowy system pamięci podręcznej magazynu usługi Azure FXT Edge ma wiele sygnalizatorów stanu wbudowanych do obudowy, aby ułatwić administratorom zrozumienie, jak działa sprzęt.

## <a name="system-health-status"></a>Stan kondycji systemu

Aby monitorować operacje pamięci podręcznej na wyższym poziomie, użyj strony **pulpitu nawigacyjnego** panelu sterowania oprogramowania, zgodnie z opisem w [przewodniku po pulpicie nawigacyjnym panelu sterowania](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Diody LED stanu sprzętu

W tej sekcji opisano różne sygnalizatory stanu wbudowane w sprzęt usługi Azure FXT Edge.

### <a name="hard-drive-status-leds"></a>Diody LED stanu dysku twardego

![obraz przedstawiający przód, poziomy, z etykietami objaśnień 2 (lewy górny róg), 1 (lewy dolny róg) i 3 (prawa strona)](media/fxt-monitor/fxt-drive-callouts.png)

Każdy przewoźnik stacji ma dwa diody LED stanu: wskaźnik działania (1) i wskaźnik stanu (2). 

* Dioda LED działania (1) świecy, gdy dysk jest w użyciu.  
* DIODa stanu (2) wskazuje na warunek dysku przy użyciu kodów z poniższej tabeli.

| Stan diody LED stanu dysku              | Znaczenie  |
|-------------------------------------|----------------------------------------------------------|
| Błyski zielone dwa razy na sekundę      | Identyfikowanie dysku *lub* <br> Przygotowywanie dysku do usunięcia  |
| Wyłączone (bez oświetlenia)                         | System nie zakończył uruchamiania *lub* <br>Dysk jest gotowy do usunięcia |
| Błyski zielone, bursztynowe i wyłączone       | Awaria dysku jest przewidywana   |
| Błyski bursztynowe cztery razy na sekundę | Awaria dysku   |
| Solid zielony                         | Dysk jest w trybie online |

Prawa strona dysku (3) ma etykietę pojemności dysku i inne informacje.

Numery dysków są drukowane w przestrzeni między dyskami. W systemie plików usługi Azure FXT Edge dysk 0 jest lewym górnym dyskiem, a dysk 1 jest bezpośrednio poniżej. Numerowanie jest kontynuowane w tym wzorcu. 

![Fotografia jednej kieszeni dysku twardego w obudowie FXT, pokazująca numery dysków i etykiety pojemności](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Lewy panel sterowania

Lewy panel sterowania ma różne wskaźniki przepełnienia stanu (1) i duży, jasny wskaźnik kondycji systemu (2). 

![lewy panel stanu z 1 etykietami wskaźników stanu po lewej stronie i 2 oznaczający duże światło kondycji systemu po prawej stronie](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Wskaźniki stanu panelu sterowania 

W przypadku wystąpienia błędu w tym systemie wskaźniki stanu po lewej stronie pokazują pełny bursztynowy sygnalizator. W poniższej tabeli opisano możliwe przyczyny i rozwiązania błędów. 

Jeśli po wykonaniu tych rozwiązań nadal wystąpi błąd, [skontaktuj się z pomocą techniczną](fxt-support-ticket.md) , aby uzyskać pomoc. 

| Ikona | Opis | Błąd | Możliwe rozwiązania |
|----------------|---------------|--------------------|----------------------|
| ![Ikona dysku](media/fxt-monitor/fxt-hd-icon.jpg) | Stan dysku | Błąd dysku | Sprawdź dziennik zdarzeń systemu, aby ustalić, czy dysk ma błąd, lub <br>Uruchom odpowiedni test diagnostyczny online; Uruchom ponownie system i uruchom osadzoną diagnostykę (ePSA) lub <br>Jeśli stacje są skonfigurowane w macierzy RAID, należy ponownie uruchomić system i wprowadzić program narzędziowy konfiguracji karty hosta. |
|![ikona temperatury](media/fxt-monitor/fxt-temp-icon.jpg) | Stan temperatury | Błąd termiczny — na przykład wentylator zakończył się niepowodzeniem lub temperatura otoczenia jest poza zakresem | Sprawdź, czy są spełnione następujące warunki: <br>Brak dmuchawy chłodzącej lub zakończyła się niepowodzeniem <br>Spacja systemowa, Shroud powietrzna, moduł pamięci lub nawiasy wypełnienia są usuwane <br>Temperatura otoczenia jest zbyt wysoka <br>Zewnętrzny przepływ powietrza jest zakłócony |
|![ikona energii elektrycznej](media/fxt-monitor/fxt-electric-icon.jpg) | Stan elektryczny | Błąd elektryczny — na przykład napięcie poza zasięgiem, Niepowodzenie PSU lub regulator napięcia z błędem |  Sprawdź dziennik zdarzeń systemu lub komunikaty systemowe dla określonego problemu. Jeśli wystąpił problem z PSU, sprawdź stan PSU i w razie konieczności ponownie podłącz PSU. | 
|![ikona pamięci](media/fxt-monitor/fxt-memory-icon.jpg) | Stan pamięci | Błąd pamięci | Sprawdź dziennik zdarzeń systemu lub komunikaty systemowe dla lokalizacji uszkodzonej pamięci; Odłączanie modułu pamięci. |
|![Ikona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stan PCIe | Błąd karty PCIe | Uruchom ponownie system; Aktualizuj sterowniki kart PCIe; Zainstaluj ponownie kartę |


### <a name="system-health-status-indicator"></a>Wskaźnik stanu kondycji systemu

Duże podświetlony przycisk z prawej strony panelu sterowania wskazuje ogólny stan systemu, a także jest używany jako sygnalizator lokalizatora jednostki w trybie identyfikatora systemu.

Naciśnij przycisk kondycja systemu i identyfikator, aby przełączyć się między trybem identyfikatora systemowego a trybem kondycji systemu.

|Stan kondycji systemu | Warunek |
|-------------------------------------------|-----------------------------------------------|
| Solid Blue | Normalna operacja: system jest włączony, działa normalnie, a tryb identyfikatora systemu nie jest aktywny. <br/>Naciśnij przycisk kondycja systemu i identyfikator, jeśli chcesz przełączyć się do trybu identyfikatora systemu. |
| Miganie niebieski | Tryb identyfikatora systemu jest aktywny. Naciśnij przycisk kondycji systemu i identyfikator systemu, aby przełączyć się do trybu kondycji systemu. |
| Solid bursztynowa | System działa w trybie awaryjnym. Jeśli problem będzie się powtarzać, [skontaktuj się z działem obsługi klienta i pomocy technicznej firmy Microsoft](fxt-support-ticket.md). |
| Miganie żółtej | Błąd systemu. Sprawdź dziennik zdarzeń systemu pod kątem określonych komunikatów o błędach. Informacje o zdarzeniach i komunikatach o błędach wygenerowanych przez oprogramowanie układowe i agentów systemu, które monitorują składniki systemu, można znaleźć na stronie wyszukiwania kodu błędu pod adresem qrl.dell.com. |


