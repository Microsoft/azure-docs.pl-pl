---
title: Replikacja między regionami Azure NetApp Files woluminów | Microsoft Docs
description: Opisuje, co Azure NetApp Files replikacja między regionami, obsługiwane pary regionów, cele poziomu usług, trwałość danych i model kosztów.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: ac0f9e6e5e1a1988386cc85c2d7576719acbd6e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590963"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replikacja między regionami Azure NetApp Files woluminów

Funkcja replikacji Azure NetApp Files zapewnia ochronę danych za pomocą replikacji woluminów między regionami. Dane można asynchronicznie zreplikować z woluminu Azure NetApp Files (Źródło) w jednym regionie do innego woluminu Azure NetApp Files (miejsce docelowe) w innym regionie.  Ta funkcja umożliwia przełączenie w tryb failover aplikacji o krytycznym znaczeniu w przypadku awarii lub awarii całego regionu.

> [!IMPORTANT]
> Funkcja replikacji między regionami jest obecnie w publicznej wersji zapoznawczej. Musisz przesłać żądanie waitlist, aby uzyskać dostęp do funkcji za Azure NetApp Files pomocą [strony przesyłania waitlist replikacji między regionami](https://aka.ms/anfcrrpreviewsignup). Przed rozpoczęciem korzystania z funkcji replikacji między regionami poczekaj oficjalne powiadomienie e-mail z zespołu Azure NetApp Files.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>Obsługiwane pary replikacji między regionami

Azure NetApp Files replikacja woluminu jest obsługiwana między różnymi [lokalnymi parami](../best-practices-availability-paired-regions.md#azure-regional-pairs) i Nieparami platformy Azure. Azure NetApp Files replikacja woluminu jest obecnie dostępna w następujących regionach:  

### <a name="azure-regional-pairs"></a>Pary regionalne platformy Azure

* Wschodnie stany USA i zachodnie stany USA
* Wschodnie stany USA 2 i środkowe stany USA
* Australia Wschodnia i Australia Południowo-Wschodnia
* Kanada Środkowa i Kanada Wschodnia
* Indie Południowe i Indie Środkowe 
* Niemcy Środkowo-Zachodnie i Niemcy Północne
* Japonia Wschodnia i Japonia Zachodnia
* Europa Północna i Europa Zachodnia
* Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo

### <a name="azure-regional-non-pairs"></a>Regionalne platformy Azure bez par

*   Zachodnie stany USA 2 i Wschodnie stany USA
*   Południowo-środkowe stany USA i środkowe stany USA
*   Południowo-środkowe stany USA i Wschodnie stany USA
*   Południowo-środkowe stany USA i Wschodnie stany USA 2
*   Wschodnie stany USA i Wschodnie stany USA 2
*   Wschodnie stany USA 2 i zachodnie stany USA 2
*   Australia Wschodnia i Azja Południowo-Wschodnia 
*   Niemcy Środkowo-Zachodnie i Południowe Zjednoczone Królestwo

## <a name="service-level-objectives"></a>Cele poziomu usługi

Cele punktu odzyskiwania (RPO) lub maksymalne wartości dopuszczalnej utraty danych są zdefiniowane jako dwa razy w harmonogramie replikacji.  Rzeczywisty cel punktu odzyskiwania może różnić się w zależności od czynników, takich jak całkowity rozmiar zestawu danych wraz z szybkością zmiany, procent zastępujący dane oraz przepustowość replikacji dostępna do transferu.   

* W przypadku harmonogramu replikacji 10 minut maksymalny cel punktu odzyskiwania wynosi 20 minut.  
* W przypadku harmonogramu replikacji co godzinę maksymalny cel punktu odzyskiwania wynosi 2 godziny.  
* W przypadku harmonogramu codziennej replikacji maksymalny cel punktu odzyskiwania wynosi dwa dni.  

Cel czasu odzyskiwania (RTO) lub maksymalny czas przestoju aplikacji biznesowej jest określany przez czynniki w sposobie tworzenia aplikacji i zapewniania dostępu do danych w drugiej lokacji. Część magazynu RTO do rozdzielenia relacji komunikacji równorzędnej w celu aktywowania woluminu docelowego i zapewnienia dostępu do odczytu i zapisu w drugiej lokacji oczekuje się na zakończenie w ciągu minuty.

## <a name="cost-model-for-cross-region-replication"></a>Model kosztów dla replikacji między regionami  

W przypadku Azure NetApp Files replikacji między regionami płacisz wyłącznie za ilość replikowanych danych. Nie ma opłat za konfigurację ani minimalnej opłaty za użycie. Cena replikacji zależy od częstotliwości replikacji i regionu woluminu *docelowego* wybranego podczas początkowej konfiguracji replikacji. Aby uzyskać więcej informacji, zobacz stronę z [cennikiem Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) .  

Zwykła Azure NetApp Files opłata za pojemność magazynu dotyczy woluminu docelowego replikacji (nazywanego również woluminem *ochrony danych* ). 

### <a name="pricing-examples"></a>Przykłady cen

Kwota replikacji między regionami rozliczana w miesiącu zależy od ilości danych replikowanych za pośrednictwem funkcji replikacji między regionami w tym miesiącu. Ilość replikowanych danych jest mierzona w GiB. Reprezentuje on sumę danych replikowanych w dwóch regionach podczas wszystkich regularnych replikacji z woluminów źródłowych do woluminów docelowych oraz podczas wszystkich ponownych synchronizacji replikacji z woluminów docelowych do woluminów źródłowych.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Przykład 1: miesiąc 1. replikacja podstawowa i replikacja przyrostowa

Założono następujące sytuacje:

* Wolumin *źródłowy* pochodzi z poziomu usługi Azure NetApp Files *Premium* . Ma rozmiar przydziałów 1000 GiB, a rozmiar woluminu wynoszący 500 GiB na początku pierwszego dnia miesiąca. Wolumin jest w regionie *Południowo-środkowe stany USA* .
* Wolumin *docelowy* jest z poziomu usługi Azure NetApp Files *Standard* . Znajduje się w regionie *Wschodnie stany USA 2* .
* Skonfigurowano *co godzinę* replikację międzyregionową między dwoma woluminami powyżej. W związku z tym cena replikacji wynosi $0,12 za GiB.
* Dla uproszczenia Załóżmy, że wolumin źródłowy ma stałą 0,5-GiB dane co godzinę, ale łączny rozmiar zużytego woluminu nie rośnie (pozostaje w 500 GiB). 

Po wstępnej instalacji replikacja punktu odniesienia odbywa się natychmiast.  

* Ilość danych replikowana podczas replikacji bazowej: `500 GiB`
* Opłaty za replikację linii bazowej: `500 GiB * $0.12 = $60`

Po replikacji bazowej replikowane są tylko zmienione bloki. W związku z tym tylko 0,5 GiB danych będzie replikowanych co godzinę w kolejnych replikacjach przyrostowych.

* Suma ilości danych zreplikowanych w ramach replikacji przyrostowej przez 30-dniowy miesiąc: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Opłaty za replikację przyrostową: `360 GiB * $0.12 = $43.2`

Na koniec miesiąca 1 łączna opłata za replikację między regionami jest następująca:  

*  Łączna opłata za replikację między regionami z miesiąca 1: `$60 + $43.2 = $103.2`

Zwykła Azure NetApp Files opłata za pojemność magazynu dotyczy woluminu docelowego. Jednak wolumin docelowy może korzystać z warstwy magazynowania innej niż (i tańsza niż) źródłowej warstwy woluminu.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Przykład 2: comiesięczne replikacje przyrostowe i ponowne synchronizowanie replikacji  

Załóżmy, że masz wolumin źródłowy, wolumin docelowy i relacja replikacji między dwoma skonfigurowanymi, jak opisano w przykładzie 1. Przez 29 dni drugiego miesiąca (30-dniowego miesiąca) replikacje godzinowe wystąpiły zgodnie z oczekiwaniami.

* Suma ilości danych replikowanych przez replikacje przyrostowe przez 29 dni: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Załóżmy, że w ostatnim dniu miesiąca nie zaplanowano przestoju w regionie źródłowym i przeprowadzono przełączenie w tryb failover do woluminu docelowego. Po 2 godzinach odzyskano region źródłowy i wykonano ponowną synchronizację z woluminu docelowego do woluminu źródłowego. W ciągu 2 godzin 0,8 GiB zmiany danych na woluminie docelowym i konieczne będzie ponowne zsynchronizowanie go ze źródłem.

* Suma ilości danych replikowanych między regularnymi replikacjami w ciągu ostatniego dnia: `0.5 GiB * 22 hours = 11 GiB`
* Ilość danych replikowana podczas jednej ponownej synchronizacji replikacji: `0.8 GiB`

W związku z tym do końca miesiąca 2 łączna opłata za replikację między regionami jest następująca:  

* Łączna opłata za replikację między regionami w miesiącu 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Zwykła Azure NetApp Files opłata za magazyn w miesiącu 2 dotyczy woluminu docelowego.

## <a name="next-steps"></a>Następne kroki
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Tworzenie replikacji woluminu](cross-region-replication-create-peering.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Zmiana rozmiaru woluminu docelowego replikacji między regionami](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Usuwanie replikacji woluminów lub woluminów](cross-region-replication-delete.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)