---
title: Wprowadzenie do widoku obowiązujących reguł zabezpieczeń w usłudze Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie możliwości widoku Network Watcher obowiązujących reguł zabezpieczeń
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 6e330d2595487366ff7b8979490cb8a897f89520
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503151"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Wprowadzenie do widoku obowiązujących reguł zabezpieczeń w usłudze Azure Network Watcher

Sieciowe grupy zabezpieczeń są skojarzone na poziomie podsieci lub na poziomie karty sieciowej. Gdy jest on skojarzony z poziomem podsieci, ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. Widok obowiązujących reguł zabezpieczeń zwraca wszystkie skonfigurowane sieciowych grup zabezpieczeń i reguły, które są skojarzone na karcie sieciowej i poziomie podsieci dla maszyny wirtualnej, która zapewnia wgląd w konfigurację. Ponadto obowiązujące reguły zabezpieczeń są zwracane dla każdej karty interfejsu sieciowego w maszynie wirtualnej. Za pomocą widoku efektywne reguły zabezpieczeń można ocenić maszynę wirtualną pod kątem luk w zabezpieczeniach, takich jak otwarte porty. Możesz również sprawdzić, czy sieciowa Grupa zabezpieczeń działa zgodnie z oczekiwaniami na podstawie [porównania między skonfigurowanymi i zatwierdzonymi regułami zabezpieczeń](network-watcher-nsg-auditing-powershell.md).

Bardziej rozszerzonym przypadkiem użycia jest zgodność i Inspekcja zabezpieczeń. Można zdefiniować zestaw reguł zabezpieczeń jako model dla ładu zabezpieczeń w organizacji. Okresowe inspekcje zgodności można zaimplementować w sposób programistyczny, porównując reguły dotyczące zasad i zasad obowiązujących dla każdej z maszyn wirtualnych w sieci.

W obszarze reguły portalu są wyświetlane dla każdego interfejsu sieciowego i pogrupowane według ruchu przychodzącego a wychodzącego. Zapewnia to prosty widok reguł stosowanych do maszyny wirtualnej. Dostępny jest przycisk Pobierz, aby łatwo pobrać wszystkie reguły zabezpieczeń niezależnie od karty do pliku CSV.

![Widok grupy zabezpieczeń][1]

Można wybrać reguły i otworzyć nowy blok, aby wyświetlić grupy zabezpieczeń sieci oraz prefiksy źródłowe i docelowe. Z tego bloku możesz przejść bezpośrednio do zasobu sieciowej grupy zabezpieczeń.

![wielopoziomowe][2]

### <a name="next-steps"></a>Następne kroki

Możesz również użyć funkcji *efektywne grupy zabezpieczeń* , korzystając z innych metod wymienionych poniżej:
* [Interfejs API REST](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [Program PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/nic#az-network-nic-list-effective-nsg)

Dowiedz się, jak przeprowadzać inspekcję ustawień sieciowej grupy zabezpieczeń, odwiedzając [ustawienia grup zabezpieczeń sieci inspekcji za pomocą programu PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png