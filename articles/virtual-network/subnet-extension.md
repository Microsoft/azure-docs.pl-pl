---
title: Rozszerzenie podsieci na platformie Azure | Microsoft Docs
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
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539302"
---
# <a name="subnet-extension"></a>Rozszerzenie podsieci
Migracja obciążenia do chmury publicznej wymaga starannego planowania i koordynacji. Jedną z kluczowych kwestii może być możliwość zachowania adresów IP. Co może być szczególnie ważne, jeśli aplikacje mają zależność adresów IP lub wymagania dotyczące zgodności mają zastosowanie do określonych adresów IP. Usługa Azure Virtual Network rozwiązuje ten problem, umożliwiając tworzenie sieci wirtualnej i podsieci przy użyciu wybranego zakresu adresów IP.

Migracje mogą uzyskać nieco trudne, gdy powyższe wymaganie jest powiązane z dodatkowym wymaganiem do utrzymywania niektórych aplikacji w środowisku lokalnym. W takim przypadku należy podzielić aplikacje na platformę Azure i lokalnie, bez konieczności ponownego numerowania adresów IP po obu stronach. Ponadto musisz zezwolić aplikacjom na komunikację, tak jakby znajdowały się w tej samej sieci.

Jednym z rozwiązań powyższego problemu jest rozszerzenie podsieci. Rozszerzanie sieci umożliwia aplikacjom komunikowanie się w tej samej domenie emisji, gdy istnieją w różnych lokalizacjach fizycznych, eliminując konieczność przetworzenia architektury topologii sieci. 

Chociaż rozszerzanie sieci nie jest dobrym zwyczajem, poniżej przypadków użycia może to być konieczne.

- **Migracja etapowa**: najbardziej typowy scenariusz polega na tym, że chcesz stopniowo przefazach migracji. Chcesz najpierw przenieść kilka aplikacji i przeprowadzić migrację aplikacji na platformę Azure w czasie.
- **Opóźnienie**: wymagania dotyczące małych opóźnień mogą być kolejnymi przyczynami, w których niektóre aplikacje są przechowywane lokalnie, aby upewnić się, że są możliwie blisko centrum danych.
- **Zgodność**: inny przypadek użycia polega na tym, że wymagania dotyczące zgodności mogą być przechowywane w środowisku lokalnym.
 
> [!NOTE] 
> Nie należy zwiększać podsieci, chyba że jest to konieczne. W przypadkach, w których można zwiększyć podsieć, należy spróbować wykonać krok pośredni. Z czasem należy spróbować ponownie nanumerować aplikacje w sieci lokalnej i migrować je na platformę Azure.

W następnej sekcji omówiono sposób rozbudowania podsieci na platformie Azure.


## <a name="extend-your-subnet-to-azure"></a>Zwiększanie podsieci do platformy Azure
 Możesz rozłożyć lokalne podsieci na platformę Azure przy użyciu rozwiązania opartego na sieci nakładki warstwy 3. Większość rozwiązań używa technologii nakładki, takiej jak VXLAN, aby zwiększyć sieć warstwy 2 przy użyciu sieci nakładki warstwy 3. Na poniższym diagramie przedstawiono uogólnione rozwiązanie. W tym rozwiązaniu ta sama podsieć istnieje na obu stronach, na platformie Azure i w środowisku lokalnym. 

![Przykład rozszerzenia podsieci](./media/subnet-extension/subnet-extension.png)

Adresy IP z podsieci są przypisywane do maszyn wirtualnych na platformie Azure i lokalnie. Zarówno platforma Azure, jak i lokalnie, mają urządzenie WUS w swoich sieciach. Gdy maszyna wirtualna na platformie Azure próbuje komunikować się z maszyną wirtualną w sieci lokalnej, usługa Azure urządzenie WUS przechwytuje pakiet, hermetyzuje ją i wysyła ją za pośrednictwem trasy sieci VPN/Express do sieci lokalnej. Lokalny urządzenie WUS odbiera pakiet, decapsulates go i przekazuje go do zamierzonego odbiorcy w swojej sieci. Ruch powrotny używa podobnej ścieżki i logiki.

W powyższym przykładzie usługa Azure urządzenie WUS i lokalne urządzenie WUS komunikują się i poznają adresy IP w tle. Bardziej złożone sieci mogą również mieć usługę mapowania, która zachowuje mapowanie między urządzeń WUS i adresami IP znajdującymi się za nimi. Gdy urządzenie WUS odbiera pakiet, wysyła zapytanie do usługi mapowania, aby sprawdzić adres urządzenie WUS, który ma docelowy adres IP.

W następnej sekcji znajdziesz szczegółowe informacje o rozwiązaniach rozszerzenia podsieci, które zostały przetestowane na platformie Azure.

## <a name="next-steps"></a>Następne kroki 
[Zwiększ swoją podsieć na platformę Azure przy użyciu rozwiązań dostawcy.](https://github.com/microsoft/Azure-LISP)