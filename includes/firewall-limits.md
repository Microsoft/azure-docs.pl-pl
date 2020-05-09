---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876007"
---
| Zasób | Limit |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000. Wszystkie typy reguł połączone.|
|Maksymalna liczba reguł DNAT|298<br>Jeśli protokół reguły jest skonfigurowany dla protokołów TCP i UDP, liczy się jako dwie reguły.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|1 - 65535|
|Publiczne adresy IP|250 maksymalnie dla portów DNAT i.|
|Adresy IP grup IP|50 grup adresów IP lub mniej: Maksymalna 5000 pojedynczych adresów IP dla każdego wystąpienia zapory.<br>51 – 100 grup adresów IP: 500 poszczególne adresy IP dla każdego wystąpienia zapory.<br><br>Aby uzyskać więcej informacji, zobacz [grupy IP (wersja zapoznawcza) w zaporze platformy Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na wartość **Internet**.<br><br>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić wartość przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** , aby zachować bezpośrednią łączność z Internetem. Domyślnie Zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją w przypadku poszczególnych przypadków. Skontaktuj się z pomocą techniczną, aby umożliwić nam zapoznanie się z Twoim przypadkiem. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i upewnimy się, że jest utrzymywana wymagana łączność internetowa zapory.|

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
