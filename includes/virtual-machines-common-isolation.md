---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: fc61c111291f9862f71f9a81828fa0fa828ab2ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511099"
---
Usługa Azure COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. Izolowane rozmiary na żywo i działają na określonej generacji sprzętu i zostaną wycofane, gdy generacja sprzętu zostanie wycofana.

Rozmiary maszyn wirtualnych wyizolowane najlepiej nadają się do obciążeń, które wymagają wysokiego stopnia odizolowania obciążeń innych klientów z przyczyn, które obejmują zgodność i wymagania prawne.  Wykorzystanie wyizolowanego rozmiaru gwarantuje, że Twoja maszyna wirtualna będzie jedyną uruchomioną w tym konkretnym wystąpieniu serwera. 


Ponadto, ponieważ maszyny wirtualne o rozmiarze izolowanym są duże, klienci mogą wybrać opcję podziału zasobów tych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Obecnie odizolowane oferty maszyn wirtualnych obejmują:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standardowa_GS5
* Standard_G5


> [!NOTE]
> Izolowane rozmiary maszyn wirtualnych mają ograniczoną cykl życia sprzętową. Aby uzyskać szczegółowe informacje, zobacz poniżej.

## <a name="deprecation-of-isolated-vm-sizes"></a>Wycofanie wyizolowanych rozmiarów maszyn wirtualnych

Izolowane rozmiary maszyn wirtualnych mają ograniczoną cykl życia sprzętową. Na platformie Azure będą wystawiane przypomnienia 12 miesięcy przed oficjalną datą zakończenia rozmiarów i zostaną zaktualizowane wyizolowane oferty.

| Rozmiar | Data wycofania izolacji | 
| --- | --- |
| Standard_DS15_v2 | 15 maja 2021 |
| Standard_D15_v2  | 15 maja 2021 |
| Standard_G5  | 15 lutego 2022 |
| Standardowa_GS5  | 15 lutego 2022 |
| Standard_E64i_v3  | 15 lutego 2022 |
| Standard_E64is_v3  | 15 lutego 2022 |


## <a name="faq"></a>Często zadawane pytania
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>P: czy rozmiar, który ma zostać wycofany, czy tylko jego funkcję "izolacja"?
Odp **.: obecnie** trwa wycofywanie tylko funkcji izolowania rozmiarów maszyn wirtualnych. Przestarzałe rozmiary izolowane będą nadal istnieć w stanie nieizolowanym. Jeśli izolacja nie jest konieczna, nie ma żadnej akcji, a maszyna wirtualna będzie nadal działać zgodnie z oczekiwaniami.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: czy w przypadku maszyn wirtualnych znajdujących się na innym sprzęcie nie jest wyizolowany?
Odp **.: Jeśli** nie ma potrzeby izolacji, nie jest wymagana żadna akcja i nie będzie przestoje. W przeciwieństwie do tego, czy izolacja jest wymagana, nasz anons będzie zawierać zalecany rozmiar zastępczy. Wybranie rozmiaru wymiany będzie wymagało od naszych klientów zmiany rozmiaru maszyn wirtualnych.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: czy istnieją jakieś różnice kosztów w przypadku przechodzenia do nieizolowanej maszyny wirtualnej?
Odp **.:** nie

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Kiedy inne izolowane rozmiary są wycofywane?
Odp **.: będziemy** podawać przypomnienia 12 miesięcy przed oficjalnym wyizolowanym rozmiarem. Nasz najnowszy anons obejmuje wycofanie funkcji izolacji Standard_G5, Standard_GS5, Standard_E64i_v3 i Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Pytanie: jestem klientem Service Fabricm na platformie Azure, opierając się na warstwach trwałości Silver lub Gold. Czy ta zmiana wpłynie na mnie?
Odp **.: nie**. Gwarancje zapewniane przez Service Fabric [warstwy trwałości](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) będą nadal działać nawet po tej zmianie. Jeśli wymagana jest izolacja sprzętowa fizycznego z innych powodów, może być konieczne wykonanie jednej z opisanych powyżej czynności. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: Jakie są punkty kontrolne na potrzeby wycofania D15_v2 lub odDS15_v2nia izolacji? 
Odp **.:** 
 
| Date (Data) | Akcja |
|---|---| 
| 15 maja, 2020<sup>1</sup> | Anons dotyczący oddalenia izolacji D/DS15_v2| 
| 15 maja 2021 | Gwarancja izolacji D/DS15_v2 usunięta| 

<sup>1</sup> istniejący klient korzystający z tych rozmiarów otrzyma wiadomość e-mail z powiadomieniem ze szczegółowymi instrukcjami dotyczącymi następnych kroków.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>P: Jakie są punkty kontrolne dla wycofania, Gs5, E64i_v3 i E64is_v3 odtrwania izolacji? 
Odp **.:** 
 
| Date (Data) | Akcja |
|---|---|
| 15 lutego, 2021<sup>1</sup> | Anons dotyczący wycofania/GS5/E64i_v3/E64is_v3 |
| 15 lutego 2022 | Usunięto gwarancję izolacji/GS5/E64i_v3/E64is_v3 |

<sup>1</sup> istniejący klient korzystający z tych rozmiarów otrzyma wiadomość e-mail z powiadomieniem ze szczegółowymi instrukcjami dotyczącymi następnych kroków.  

## <a name="next-steps"></a>Następne kroki

Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
