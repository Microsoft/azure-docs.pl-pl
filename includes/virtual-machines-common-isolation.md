---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: 3d65b574fc1479d000d347020823166a4d4b968b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410588"
---
Usługa Azure COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. Izolowane rozmiary na żywo i działają na określonej generacji sprzętu i zostaną wycofane, gdy generacja sprzętu zostanie wycofana.

Rozmiary maszyn wirtualnych wyizolowane najlepiej nadają się do obciążeń, które wymagają wysokiego stopnia odizolowania obciążeń innych klientów z przyczyn, które obejmują zgodność i wymagania prawne.  Wykorzystanie wyizolowanego rozmiaru gwarantuje, że Twoja maszyna wirtualna będzie jedyną uruchomioną w tym konkretnym wystąpieniu serwera. 


Ponadto, ponieważ maszyny wirtualne o rozmiarze izolowanym są duże, klienci mogą wybrać opcję podziału zasobów tych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Obecnie odizolowane oferty maszyn wirtualnych obejmują:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standardowa_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> Izolowane rozmiary maszyn wirtualnych mają ograniczoną cykl życia sprzętową. Aby uzyskać szczegółowe informacje, zobacz poniżej.

## <a name="deprecation-of-isolated-vm-sizes"></a>Wycofanie wyizolowanych rozmiarów maszyn wirtualnych

Ponieważ rozmiary maszyn wirtualnych są zależne od rozmiarów sprzętowych, platforma Azure dostarczy monity 12 miesięcy przed oficjalnym wycofaniem rozmiarów.  Platforma Azure oferuje także zaktualizowany wyizolowany rozmiar w następnej wersji sprzętowej, z którą klient może rozważyć przeniesienie obciążeń do programu.

| Rozmiar | Data wycofania izolacji | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15 maja 2020 |
| Standard_D15_v2<sup>1</sup>  | 15 maja 2020 |

<sup>1</sup>  Aby uzyskać szczegółowe informacje na temat Standard_DS15_v2 i Standard_D15_v2 programu wycofywania izolacji, zobacz często zadawane pytania


## <a name="faq"></a>Często zadawane pytania
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>P: czy rozmiar, który ma zostać wycofany, czy tylko "izolacja" jest?
Odp **.: Jeśli** rozmiar maszyny wirtualnej nie ma indeksu dolnego "i", tylko funkcja "izolacja" zostanie wycofana. Jeśli izolacja nie jest konieczna, nie ma żadnej akcji, a maszyna wirtualna będzie nadal działać zgodnie z oczekiwaniami. Przykładami mogą być Standard_DS15_v2, Standard_D15_v2, Standard_M128ms itd. Jeśli rozmiar maszyny wirtualnej obejmuje indeks dolny, rozmiar zostanie wycofany.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: czy w przypadku maszyn wirtualnych znajdujących się na innym sprzęcie nie jest wyizolowany?
Odp **.: Jeśli** nie ma potrzeby izolacji, nie jest wymagana żadna akcja i nie będzie przestoje.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: czy istnieją jakieś różnice kosztów w przypadku przechodzenia do nieizolowanej maszyny wirtualnej?
Odp **.:** nie

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Kiedy inne izolowane rozmiary są wycofywane?
Odp **.: będziemy** podawać przypomnienia 12 miesięcy przed oficjalnym wyizolowanym rozmiarem.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Pytanie: jestem klientem Service Fabricm na platformie Azure, opierając się na warstwach trwałości Silver lub Gold. Czy ta zmiana wpłynie na mnie?
Odp **.: nie**. Gwarancje zapewniane przez Service Fabric [warstwy trwałości](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) będą nadal działać nawet po tej zmianie. Jeśli wymagana jest izolacja sprzętowa fizycznego z innych powodów, może być konieczne wykonanie jednej z opisanych powyżej czynności. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: Jakie są punkty kontrolne na potrzeby wycofania D15_v2 lub odDS15_v2nia izolacji? 
Odp **.:** 
 
| Data | Akcja |
|---|---| 
| 18 listopada 2019 r. | Dostępność D/DS15i_v2 (PAYG, 1 – rok RI) | 
| 14 maja 2020 | Ostatni dzień zakupu D/DS15i_v2 1 roku RI | 
| 15 maja 2020 | Gwarancja izolacji D/DS15_v2 usunięta | 
| 15 maja 2021 | Wycofaj D/DS15i_v2 (wszyscy klienci, z wyjątkiem sytuacji, w których zakupił 3-letni RI) D/DS15_v2 przed 18 listopada, 2019)| 
| 17 listopada 2022 | Wycofaj D/DS15i_v2 po 3-letnich usługach RIs (w przypadku klientów, którzy kupili 3-letnie RI) D/DS15_v2 przed 18 listopada, 2019) |

## <a name="next-steps"></a>Następne kroki

Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
