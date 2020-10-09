---
title: Często zadawane pytania dotyczące monitorowania Azure Site Recovery
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące monitorowania Azure Site Recovery przy użyciu wbudowanego monitorowania i Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131162"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Często zadawane pytania dotyczące monitorowania Site Recovery

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące monitorowania [Site Recovery](site-recovery-overview.md)platformy Azure przy użyciu wbudowanego monitorowania Site Recovery i Azure Monitor (log Analytics).

## <a name="general"></a>Ogólne

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Jak jest rejestrowane wartość RPO różni się od najnowszego dostępnego punktu odzyskiwania?

Site Recovery używa wieloetapowego procesu asynchronicznego do replikowania maszyn na platformę Azure.

- W przedostatniej części replikacji ostatnie zmiany na komputerze wraz z metadanymi są kopiowane do konta magazynu dziennika/pamięci podręcznej.
- Te zmiany, wraz ze znacznikiem identyfikującym punkt możliwy do odzyskania, są zapisywane na koncie magazynu/dysku zarządzanym w regionie docelowym.
- Site Recovery może teraz wygenerować odwracalny punkt dla maszyny.
- W tym momencie cel punktu odzyskiwania został osiągnięty dla zmian przekazanych do konta magazynu. Innymi słowy, cel punktu odzyskiwania maszyny w tym punkcie jest równy czasowi, który upłynął od sygnatury czasowej odpowiadającej punktowi możliwemu do odzyskania.
- Teraz Site Recovery wybiera przekazane dane z konta magazynu i stosuje je do dysków repliki utworzonych dla maszyny.
- Site Recovery następnie generuje punkt odzyskiwania i udostępni ten punkt do odzyskiwania w trybie failover.
- W ten sposób najnowszy dostępny punkt odzyskiwania wskazuje sygnaturę czasową odpowiadającą najnowszym punktowi odzyskiwania, który został już przetworzony i ma zastosowanie do dysków repliki.


Niewłaściwy czas systemowy na maszynie źródłowej replikacji lub na serwerach infrastruktury lokalnej spowoduje pochylenie obliczonej wartości RPO. W celu uzyskania dokładnego raportowania punktu odzyskiwania upewnij się, że zegar systemowy jest dokładny na wszystkich serwerach i komputerach.



## <a name="inbuilt-site-recovery-logging"></a>Wbudowane rejestrowanie Site Recovery


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Dlaczego liczba maszyn wirtualnych w widoku infrastruktury magazynu różni się od łącznej liczby pokazanej w replikowanych elementach?

Widok infrastruktury magazynu jest objęty przez scenariusze replikacji. Tylko maszyny w aktualnie wybranego scenariusza replikacji są uwzględniane w liczbie dla widoku. Ponadto Zliczamy tylko maszyny wirtualne skonfigurowane do replikacji na platformę Azure. Maszyny wirtualne przełączone w tryb failover lub maszyny replikowane z powrotem do lokacji lokalnej nie są zliczane w widoku.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Dlaczego liczba replikowanych elementów w Essentials różni się od łącznej liczby zreplikowanych elementów na pulpicie nawigacyjnym?

Tylko maszyny, dla których zakończono replikację początkową, są uwzględniane w liczbie pokazanej w Essentials. Łącznie zreplikowane elementy obejmują wszystkie maszyny w magazynie, w tym te, dla których replikacja początkowa jest obecnie w toku.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Jak często Site Recovery wysyłać dzienniki zasobów do dziennika Azure Monitor? 

- AzureSiteRecoveryReplicationStats i AzureSiteRecoveryRecoveryPoints są wysyłane co 15 minut.  
- AzureSiteRecoveryReplicationDataUploadRate i AzureSiteRecoveryProtectedDiskDataChurn są wysyłane co pięć minut. 
- AzureSiteRecoveryJobs jest wysyłana w wyzwalaczu i zakończeniu zadania.
- AzureSiteRecoveryEvents jest wysyłana za każdym razem, gdy zdarzenie zostanie wygenerowane. 
- AzureSiteRecoveryReplicatedItems jest wysyłana za każdym razem, gdy istnieje jakakolwiek zmiana środowiska. Zazwyczaj czas odświeżania danych to 15 minut od zmiany. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Jak długo dane są przechowywane w dziennikach Azure Monitor? 

Domyślnie przechowywanie odbywa się przez 31 dni. Możesz zwiększyć okres w sekcji **użycie i szacowany koszt** w obszarze roboczym log Analytics. Kliknij pozycję **przechowywanie danych**i wybierz zakres.

### <a name="whats-the-size-of-the-resource-logs"></a>Jakie są rozmiary dzienników zasobów? 

Zwykle rozmiar dziennika to 15-20 KB. 


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak monitorować przy użyciu [wbudowanego monitorowania Site Recovery](site-recovery-monitor-and-troubleshoot.md)lub [Azure monitor](monitor-log-analytics.md).


