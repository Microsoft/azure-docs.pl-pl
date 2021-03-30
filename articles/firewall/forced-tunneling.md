---
title: Wymuszone tunelowanie zapory platformy Azure
description: Można skonfigurować Wymuszone tunelowanie w celu kierowania ruchu związanego z Internetem do dodatkowej zapory lub sieciowego urządzenia wirtualnego w celu dalszej obróbki.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612603"
---
# <a name="azure-firewall-forced-tunneling"></a>Wymuszone tunelowanie zapory platformy Azure

Podczas konfigurowania nowej zapory Azure Firewall można skierować cały ruch internetowy do wyznaczonego następnego przeskoku, zamiast bezpośrednio do Internetu. Na przykład lokalna Zapora brzegowa lub inne wirtualne urządzenie sieciowe (urządzenie WUS) mogą przetwarzać ruch sieciowy przed przekazaniem go do Internetu. Nie można jednak skonfigurować istniejącej zapory do wymuszonego tunelowania.

Domyślnie Wymuszone tunelowanie nie jest dozwolone w zaporze platformy Azure, aby upewnić się, że wszystkie jej wychodzące zależności platformy Azure są spełnione. Konfiguracje trasy zdefiniowane przez użytkownika (UDR) w *AzureFirewallSubnet* , które mają trasę domyślną nie przechodzą bezpośrednio do Internetu, są wyłączone.

## <a name="forced-tunneling-configuration"></a>Konfiguracja wymuszonego tunelowania

Aby zapewnić obsługę tunelowania wymuszonego, ruch związany z zarządzaniem usługami jest oddzielony od ruchu klienta. Dodatkowa podsieć dedykowana o nazwie *AzureFirewallManagementSubnet* (minimalny rozmiar podsieci/26) jest wymagana ze skojarzonym publicznym adresem IP. Jedyną trasą dozwoloną w tej podsieci jest trasa domyślna do Internetu, a Propagacja trasy BGP musi być wyłączona.

Jeśli trasa domyślna jest anonsowana za pośrednictwem protokołu BGP, aby wymusić ruch w środowisku lokalnym, należy utworzyć *AzureFirewallSubnet* i *AzureFirewallManagementSubnet* przed wdrożeniem zapory i mieć UDR z domyślną trasą do Internetu i **propagować trasy bramy** wyłączone.

W ramach tej konfiguracji *AzureFirewallSubnet* może teraz zawierać trasy do dowolnej zapory lokalnej lub urządzenie WUS, aby przetwarzać ruch przed przekazaniem go do Internetu. Możesz również opublikować te trasy za pośrednictwem protokołu BGP do *AzureFirewallSubnet* , jeśli w tej podsieci jest włączona **Propagacja tras bramy** .

Na przykład możesz utworzyć trasę domyślną na *AzureFirewallSubnet* z bramą sieci VPN w ramach następnego skoku, aby przejść do urządzenia lokalnego. Można też włączyć **propagację tras bramy** w celu uzyskania odpowiednich tras do sieci lokalnej.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Propagacja trasy bramy sieci wirtualnej":::

W przypadku włączenia tunelowania wymuszonego ruch związany z Internetem jest podłączony do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet, ukrywając źródło z lokalnej zapory.

Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure SNATs ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Można jednak skonfigurować zaporę platformy Azure w taki sposób, aby **nie** przywiązać do tego zakresu publicznych adresów IP. Aby uzyskać więcej informacji, zobacz [zakresy prywatnych adresów IP zapory systemu Azure](snat-private-range.md).

Po skonfigurowaniu zapory platformy Azure do obsługi wymuszonego tunelowania nie można cofnąć konfiguracji. Jeśli usuniesz wszystkie inne konfiguracje protokołu IP w zaporze, Konfiguracja protokołu IP zarządzania zostanie również usunięta, a Zapora zostanie cofnięta. Nie można usunąć publicznego adresu IP przypisanego do konfiguracji adresu IP zarządzania, ale można przypisać inny publiczny adres IP.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md)
