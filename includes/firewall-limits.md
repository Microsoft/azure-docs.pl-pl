---
title: dołączanie pliku
description: dołączanie pliku
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fe223030b98f95036f01cf69babdeb8a9a84dc2d
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242108"
---
| Zasób | Limit |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000. Wszystkie typy reguł połączone.|
|Maksymalna liczba reguł DNAT|298 dla jednego publicznego adresu IP.<br>Wszystkie dodatkowe publiczne adresy IP przyczyniają się do dostępnych portów, ale obniżają liczbę dostępnych reguł DNAT. Na przykład dwa publiczne adresy IP zezwalają na reguły DNAT 297. Jeśli protokół reguły jest skonfigurowany dla protokołów TCP i UDP, liczy się jako dwie reguły.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|1 - 65535|
|Publiczne adresy IP|250 maksymalnie. Wszystkie publiczne adresy IP mogą być używane w regułach DNAT i wszystkie współtworzą dostęp do dostępnych portów.|
|Adresy IP w grupach IP|50 grup adresów IP lub mniej: Maksymalna 5000 pojedynczych adresów IP dla każdego wystąpienia zapory.<br>51 – 100 grup adresów IP: 500 poszczególne adresy IP dla każdego wystąpienia zapory.<br><br>Aby uzyskać więcej informacji, zobacz [grupy IP (wersja zapoznawcza) w zaporze platformy Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na wartość **Internet**.<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić wartość przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** , aby zachować bezpośrednią łączność z Internetem. Domyślnie Zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją w przypadku poszczególnych przypadków. Skontaktuj się z pomocą techniczną, aby umożliwić nam zapoznanie się z Twoim przypadkiem. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i upewnimy się, że jest utrzymywana wymagana łączność internetowa zapory.|

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
