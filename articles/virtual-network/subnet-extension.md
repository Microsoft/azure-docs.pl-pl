---
title: Rozszerzenie podsieci w usłudze Azure | Microsoft Docs
description: Dowiedz się więcej o rozszerzeniu podsieci na platformie Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: e39859e3cc4d28ac4b1456fcdaec65ecfb9c7f31
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728216"
---
# <a name="subnet-extension"></a>Rozszerzenie podsieci
Migracja obciążeń do chmury publicznej wymaga starannego planowania i koordynacji. Jedną z kluczowych kwestii może być możliwość zachowania adresów IP. Może to być ważne zwłaszcza wtedy, gdy aplikacje są zależne od adresów IP lub jeśli masz wymagania dotyczące zgodności dotyczące używania określonych adresów IP. Usługa Azure Virtual Network rozwiązuje ten problem, umożliwiając tworzenie sieci wirtualnych i podsieci przy użyciu wybranego zakresu adresów IP.

Migracje mogą być nieco trudne, gdy powyższe wymaganie jest powiązane z dodatkowym wymaganiem do utrzymania niektórych aplikacji w środowisku lokalnym. W takiej sytuacji należy podzielić aplikacje między platformę Azure i lokalnie bez konieczności ponownego numerowania adresów IP po obu stronach. Ponadto należy zezwolić aplikacjom na komunikację tak, jakby były w tej samej sieci.

Jednym z rozwiązań powyższego problemu jest rozszerzenie podsieci. Rozszerzenie sieci umożliwia aplikacjom mówienie w tej samej domenie emisji, gdy istnieją w różnych lokalizacjach fizycznych, eliminując konieczność ponownego projektowania topologii sieci. 

Chociaż rozszerzanie sieci nie jest dobrą praktyką, poniższe przypadki użycia mogą być niezbędne.

- **Migracja etapowa:** Najbardziej powszechnym scenariuszem jest etap migracji. Chcesz przenieść kilka aplikacji najpierw i z czasem migrować pozostałe aplikacje na platformę Azure.
- **Opóźnienie:** Wymagania dotyczące małych opóźnień mogą być kolejnym powodem utrzymania niektórych aplikacji w środowisku lokalnym, aby upewnić się, że są one jak najbardziej zbliżone do centrum danych.
- **Zgodność:** Innym przypadek użycia jest to, że mogą być spełnione wymagania dotyczące zgodności w celu zachowania niektórych aplikacji w środowisku lokalnym.
 
> [!NOTE] 
> Nie należy rozszerzać podsieci, chyba że jest to konieczne. W przypadkach, w których rozszerzasz podsieci, należy spróbować zrobić to jako krok pośredni. Z czasem należy spróbować ponownie numerować aplikacje w sieci lokalnej i przeprowadzić ich migrację na platformę Azure.

W następnej sekcji omówimy sposób rozszerzania podsieci na platformę Azure.


## <a name="extend-your-subnet-to-azure"></a>Rozszerzanie podsieci na platformę Azure
 Podsieci lokalne można rozszerzyć na platformę Azure przy użyciu rozwiązania opartego na sieci nakładki warstwy 3. Większość rozwiązań używa technologii nakładek, takiej jak VXLAN, aby rozszerzyć sieć warstwy 2 przy użyciu sieci nakładek warstwy 3. Na poniższym diagramie przedstawiono uogólnione rozwiązanie. W tym rozwiązaniu ta sama podsieć istnieje po obu stronach, czyli na platformie Azure i w środowisku lokalnym. 

![Przykład rozszerzenia podsieci](./media/subnet-extension/subnet-extension.png)

Adresy IP z podsieci są przypisywane do maszyn wirtualnych na platformie Azure i lokalnych. Zarówno platforma Azure, jak i lokalne urządzenie WUS są wstawiane do sieci. Gdy maszyna wirtualna na platformie Azure próbuje połączyć się z maszyną wirtualną w sieci lokalnej, urządzenie WUS platformy Azure przechwyci pakiet, hermetyzuje go i wysyła za pośrednictwem sieci VPN/Express Route do sieci lokalnej. Lokalne urządzenie WUS odbiera pakiet, dekapsuluje go i przekazuje do zamierzonego odbiorcy w swojej sieci. Ruch powrotny używa podobnej ścieżki i logiki.

W powyższym przykładzie urządzenie WUS platformy Azure i lokalne urządzenie WUS komunikują się ze sobą i poznają za sobą adresy IP. Bardziej złożone sieci mogą również mieć usługę mapowania, która zachowuje mapowanie między urządzeniami WUS i ich adresami IP. Gdy urządzenie WUS odbiera pakiet, wysyła zapytanie do usługi mapowania w celu znalezienia adresu urządzenia WUS, które ma za nim docelowy adres IP.

W następnej sekcji znajdziesz szczegółowe informacje na temat rozwiązań rozszerzeń podsieci, które przetestowaliśmy na platformie Azure.

## <a name="next-steps"></a>Następne kroki 
[Rozszerz podsieci lokalne na platformę Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-extended-network)przy użyciu usługi Azure Extended Network.