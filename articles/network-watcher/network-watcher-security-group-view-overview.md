---
title: Wprowadzenie do widoku obowiązujących reguł zabezpieczeń w usłudze Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie możliwości wyświetlania Network Watcher — Obowiązujące reguły zabezpieczeń
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
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778610"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Wprowadzenie do widoku obowiązujących reguł zabezpieczeń w usłudze Azure Network Watcher

Sieciowe grupy zabezpieczeń są skojarzone na poziomie podsieci lub na poziomie karty sieciowej. Skojarzona na poziomie podsieci ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. Widok obowiązujących reguł zabezpieczeń zwraca wszystkie skonfigurowane sieciowe grup zabezpieczeń i reguły, które są skojarzone na poziomie karty sieciowej i podsieci dla maszyny wirtualnej, zapewniając wgląd w konfigurację. Ponadto dla każdej karty sieciowej na maszynie wirtualnej są zwracane obowiązujące reguły zabezpieczeń. Za pomocą widoku Obowiązujące reguły zabezpieczeń można ocenić maszynę wirtualną pod względem luk w zabezpieczeniach sieci, takich jak otwarte porty. Można również sprawdzić, czy sieciowa grupa zabezpieczeń działa zgodnie z oczekiwaniami, na podstawie porównania skonfigurowanych i zatwierdzonych [reguł zabezpieczeń.](network-watcher-nsg-auditing-powershell.md)

Bardziej rozszerzony przypadek użycia dotyczy zgodności zabezpieczeń i inspekcji. Jako model nadzoru nad zabezpieczeniami w organizacji można zdefiniować nakazowy zestaw reguł zabezpieczeń. Okresową inspekcję zgodności można zaimplementować w sposób programowy, porównując normatywne reguły z obowiązującymi regułami dla każdej maszyny wirtualnej w sieci.

W portalu reguły są wyświetlane dla każdego interfejsu sieciowego i pogrupowane według ruchu przychodzącego i wychodzącego. Zapewnia to prosty widok reguł stosowanych do maszyny wirtualnej. Dostępny jest przycisk pobierania, który umożliwia łatwe pobranie wszystkich reguł zabezpieczeń niezależnie od karty do pliku CSV.

![widok grupy zabezpieczeń][1]

Można wybrać reguły, a zostanie otwarty nowy blok z prefiksami źródłowymi i docelowymi sieciowej grupy zabezpieczeń. W tym bloku możesz przejść bezpośrednio do zasobu sieciowej grupy zabezpieczeń.

![Drążenia][2]

### <a name="next-steps"></a>Następne kroki

Można również użyć funkcji *Efektywne grupy zabezpieczeń* za pomocą innych metod wymienionych poniżej:
* [Interfejs API REST](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [Program PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

Dowiedz się, jak inspekcji ustawień sieciowej grupy zabezpieczeń, odwiedzając stronę [Audit Network Security Group settings with PowerShell (Inspekcja ustawień sieciowej grupy zabezpieczeń przy użyciu programu PowerShell)](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png