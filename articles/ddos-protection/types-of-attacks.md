---
title: Typy ataków Azure DDoS Protection standardowe ograniczenia
description: Dowiedz się, jakie typy ataków Azure DDoS Protection są chronione przez Standard.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 50dd5cf9a51ebcc6b2df188838ef2910afcd224d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905507"
---
# <a name="types-of-ddos-attacks-overview"></a>Typy ataków DDoS — Omówienie

Standard DDoS Protection może ograniczyć ataki następujących typów:

- **Ataki pomiarowe** : te ataki zalewają warstwę sieciową o znaczną ilość pozornie wiarygodnego ruchu. Obejmują one zalewania protokołu UDP, zalewania Wzmiany oraz inne zalewania pakietów. DDoS Protection Standard ogranicza te potencjalne ataki z obsługą wielodostępności przez przeabsorbowanie i ich kontrolę przy użyciu globalnej skali sieci platformy Azure.
- **Ataki protokołów** : te ataki sprawiają, że element docelowy jest niedostępny, wykorzystując słabą lukę w stosie protokołu warstwy 3 i 4. Obejmują one ataki na zalanie, ataki na odbicie i inne ataki protokołów. DDoS Protection Standard ogranicza te ataki, różnią się od złośliwego i wiarygodnego ruchu, współpracując z klientem i blokując złośliwy ruch. 
- **Ataki warstwowe zasobów (aplikacji)** : te ataki są docelowymi pakietami aplikacji sieci Web, aby zakłócać przekazywanie danych między hostami. Obejmują one naruszenia protokołu HTTP, wstrzykiwanie kodu SQL, skrypty między lokacjami oraz inne ataki warstwy 7. Użyj zapory aplikacji sieci Web, takiej jak [Zapora aplikacji sieci Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)platformy Azure Application Gateway, a także DDoS Protection Standard, aby zapewnić ochronę przed atakami. Istnieją także oferty zapory aplikacji sieci Web innych firm dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Usługa Azure DDoS Protection w warstwie Standardowa

DDoS Protection Standard chroni zasoby w sieci wirtualnej łącznie z publicznymi adresami IP skojarzonymi z maszynami wirtualnymi, modułami równoważenia obciążenia i bramami aplikacji. W połączeniu z zaporą aplikacji sieci Web Application Gateway lub zaporą aplikacji sieci Web innej firmy wdrożoną w sieci wirtualnej z publicznym adresem IP, DDoS Protection Standard może zapewnić pełną możliwość ograniczenia warstwy 3 do warstwy 7.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).