---
title: Model kosztów dla Azure NetApp Files | Microsoft Docs
description: Opisuje model kosztów Azure NetApp Files do zarządzania wydatkami z usługi.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325610"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model kosztów usługi Azure NetApp Files 

Zrozumienie modelu kosztów Azure NetApp Files ułatwia zarządzanie wydatkami z usługi. 

Aby uzyskać model kosztów specyficzny dla replikacji między regionami, zobacz [model kosztów dla replikacji między regionami](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Obliczanie zużycia pojemności

Opłaty za Azure NetApp Files są naliczane zgodnie z zaalokowanej pojemności magazynu.  Przydzielone pojemności są alokowane przez utworzenie pul pojemności.  Pule pojemności są rozliczane na podstawie wartości $/provisioned-GiB/month w przyrostach godzinowych. Minimalny rozmiar puli o pojedynczej pojemności to 4 TiB, a pule pojemności mogą zostać następnie rozwinięte w przyrostach 1-TiB. Woluminy są tworzone w ramach pul pojemności.  Każdy wolumin ma przypisany przydział, który zmniejsza się od pojemności alokowanej do puli. Przydział, który może być przypisany do woluminów z zakresu od minimum do 100 GiB, do maksymalnie 100 TiB.  

W przypadku aktywnego woluminu użycie wydajności w stosunku do przydziału jest oparte na logicznej (efektywnej) pojemności.

Jeśli rzeczywiste zużycie pojemności woluminu przekracza limit przydziału magazynu, wolumin może nadal rosnąć. Operacje zapisu będą nadal dozwolone, o ile rzeczywisty rozmiar woluminu będzie mniejszy niż limit systemu (100 TiB).  

Łączna ilość użytej pojemności w puli pojemności do jej alokowanej kwoty to suma większej liczby przypisanych przydziałów lub rzeczywistego zużycia wszystkich woluminów w puli: 

   ![Obliczanie łącznej wydajności](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Na poniższym diagramie przedstawiono te koncepcje.  
* Mamy pulę pojemności z 4 TiB pojemnością administracyjną.  Pula zawiera trzy woluminy.  
    * Do woluminu 1 przypisano limit przydziału równy 2 TiB i ma 800 GiB zużycia.  
    * Wolumin 2 ma przypisany limit 1 TiB i ma 100 GiB zużycia.  
    * Do woluminu 3 przypisano limit przydziału 500 GiB, ale ma 800 GiB zużycia (nadwyżki).  
* Pula pojemności jest naliczana za 4 TiB pojemności (zainicjowana ilość).  
    3,8 TiB pojemności (2 TiB i 1 TiB przydziału z woluminów 1 i 2 oraz 800 GiB rzeczywiste zużycie dla woluminu 3). I 200 GiB pojemności.

   ![Pula pojemności z trzema woluminami](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Nadwyżkowe użycie pojemności  

Jeśli całkowita używana pojemność puli przekroczy swoją zainicjowaną pojemność, operacje zapisu danych są nadal dozwolone.  Po upływie okresu prolongaty (1 godzina), jeśli używana pojemność puli nadal przekracza swoją zainicjowaną pojemność, rozmiar puli zostanie automatycznie zwiększony w przyrostach 1 TiB, dopóki pojemność nie zostanie osiągnięta.  Na przykład na powyższej ilustracji, jeśli wolumin 3 zostanie powiększony, a rzeczywiste zużycie osiągnie 1,2 TiB, wówczas po okresie prolongaty rozmiar puli zostanie automatycznie zmieniony na 5 TiB.  W efekcie pojemność puli zainicjowanej (5 TiB) przekracza użytą pojemność (4,2 TiB).  

Mimo że rozmiar puli pojemności zostanie automatycznie powiększony, aby spełnić zapotrzebowanie na wolumin, nie jest automatycznie zmniejszany w przypadku zmniejszenia rozmiaru woluminu. Jeśli chcesz zmniejszyć rozmiar puli pojemności po zmniejszeniu rozmiaru woluminu (na przykład po oczyszczeniu danych woluminu), należy _ręcznie_ zmniejszyć rozmiar puli pojemności.

## <a name="manual-changes-of-the-pool-size"></a>Ręczne zmiany rozmiaru puli  

Rozmiar puli można ręcznie zwiększyć lub zmniejszyć. Jednak obowiązują następujące ograniczenia:
* Minimalne i maksymalne limity usługi  
    Zobacz artykuł dotyczący [limitów zasobów](azure-netapp-files-resource-limits.md).
* Przyrost 1 TiB po początkowym minimalnym zakupie 4-TiB
* Minimalny przyrost rozliczania dla jednej godziny
* Rozmiar puli aprowizacji nie może zostać zmniejszony do wartości mniejszej niż całkowita użyta w puli.
* W przypadku pul pojemności z ręczną usługą QoS rozmiar puli można zmniejszyć tylko wtedy, gdy rozmiar i poziom usług zapewniają większą przepływność niż rzeczywista przypisana przepływność dla wszystkich woluminów.

## <a name="behavior-of-maximum-size-pool-overage"></a>Zachowanie nadwyżkowe w puli o maksymalnym rozmiarze   

Maksymalny rozmiar puli pojemności, którą można utworzyć lub zmienić jej rozmiar to 500 TiB.  Gdy całkowita użyta pojemność w puli pojemności przekracza 500 TiB, zostaną wykonane następujące sytuacje:
* Zapisy danych będą nadal dozwolone (jeśli wolumin jest niższy niż maksymalny 100 TiB).
* Po upływie 1-godzinnego okresu prolongaty rozmiar puli zostanie automatycznie zmieniony w przyrostach TiB, dopóki alokacja puli nie przekracza całkowitej używanej pojemności.
* Do przypisywania przydziału woluminu nie można używać dodatkowej i rozliczanej pojemności puli, która przekracza 500 TiB. Nie można go również użyć do rozwinięcia limitów QoS wydajności.  
    Zobacz [poziomy usługi](azure-netapp-files-service-levels.md) dotyczące limitów wydajności i ustalania wielkości QoS.

Poniższy diagram ilustruje następujące koncepcje:
* Mamy pulę pojemności w warstwie Premium Storage i 500-TiB pojemność. Pula zawiera dziewięć woluminów.
    * Woluminy od 1 do 8 są przypisane do 60 TiB każdy.  Całkowita użyta pojemność to 480 TiB.  
        Każdy wolumin ma limit QoS wynoszący 3,75 GiB/s przepływności (60 TiB * 64 MiB/s).  
    * Do woluminu 9 przypisano limit 20 TiB.  
        Wolumin 9 ma limit QoS 1,25 GiB/s przepływności (20 TiB * 64 MiB/s).
* Wolumin 9 jest scenariuszem nadwyżkowym. Ma 25 TiB rzeczywistego zużycia.  
    * Po upływie okresu prolongaty o jednej godzinie rozmiar puli pojemności zostanie zmieniony na 505 TiB.  
        Oznacza to, że łączna zużyta pojemność = 8 * 60-TiB przydziału dla woluminów od 1 do 8 i 25 TiB rzeczywistego zużycia dla woluminu 9.
    * Rozliczane zdolności produkcyjne to 505 TiB.
    * Nie można zwiększyć przydziału woluminu dla woluminu 9 (ponieważ łączny przydział puli nie może przekraczać 500 TiB).
    * Nie można przypisać dodatkowej przepływności QoS (ponieważ całkowita jakość usług QoS dla puli nadal jest oparta na 500 TiB).

   ![Pula pojemności z dziewięcioma woluminami](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Użycie pojemności migawek 

Użycie pojemności migawek w Azure NetApp Files jest obciążane opłatami za przydział woluminu nadrzędnego.  W związku z tym ma taką samą stawkę rozliczania co Pula pojemności, do której należy wolumin.  Jednak, w przeciwieństwie do aktywnego woluminu, użycie migawek jest mierzone na podstawie zużywanej pojemności.  Migawki Azure NetApp Files są zróżnicowane. W zależności od współczynnika zmian danych migawki często zużywają znacznie mniejszą pojemność niż pojemność logiczna woluminu aktywnego. Załóżmy na przykład, że masz migawkę woluminu 500-GiB, która zawiera tylko 10 GiB danych różnicowych. Pojemność, która jest naliczana względem limitu przydziału woluminu dla tej migawki, wynosi 10 GiB, nie 500 GiB. 

## <a name="next-steps"></a>Następne kroki

* [Strona cennika Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Model kosztów dla replikacji między regionami](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
