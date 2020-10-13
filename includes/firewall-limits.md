---
title: plik dołączany
description: plik dołączany
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507336"
---
| Zasób | Limit |
| --- | --- |
| Przepływność danych |30 GB/s<sup>1</sup> |
|Reguły|10 000. Wszystkie typy reguł połączone.|
|Maksymalna liczba reguł DNAT|298 dla jednego publicznego adresu IP.<br>Wszystkie dodatkowe publiczne adresy IP przyczyniają się do dostępnych portów, ale obniżają liczbę dostępnych reguł DNAT. Na przykład dwa publiczne adresy IP zezwalają na reguły DNAT 297. Jeśli protokół reguły jest skonfigurowany dla protokołów TCP i UDP, liczy się jako dwie reguły.|
|Minimalny rozmiar AzureFirewallSubnet |/26|
|Zakres portów w regułach sieci i aplikacji|1 - 65535|
|Publiczne adresy IP|250 maksymalnie. Wszystkie publiczne adresy IP mogą być używane w regułach DNAT i wszystkie współtworzą dostęp do dostępnych portów.|
|Adresy IP w grupach IP|Maksymalnie 100 grup adresów IP na zaporę.<br>Maksymalna 5000 pojedynczych adresów IP lub prefiksów IP dla każdej grupy adresów IP.
|Tabela tras|Domyślnie AzureFirewallSubnet ma trasę 0.0.0.0/0 z wartością NextHopType ustawioną na wartość **Internet**.<br><br>Usługa Azure Firewall musi mieć bezpośrednie połączenie z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić wartość przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** , aby zachować bezpośrednią łączność z Internetem. Domyślnie Zapora platformy Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.<br><br>Jeśli jednak konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, firma Microsoft będzie obsługiwać ją w przypadku poszczególnych przypadków. Skontaktuj się z pomocą techniczną, aby umożliwić nam zapoznanie się z Twoim przypadkiem. Jeśli zostanie zaakceptowana, zezwolimy na subskrypcję i upewnimy się, że jest utrzymywana wymagana łączność internetowa zapory.|

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.
