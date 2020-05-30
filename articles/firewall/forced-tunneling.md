---
title: Wymuszone tunelowanie zapory platformy Azure
description: Można skonfigurować Wymuszone tunelowanie w celu kierowania ruchu związanego z Internetem do dodatkowej zapory lub sieciowego urządzenia wirtualnego w celu dalszej obróbki.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: d9afb93611712109d5e8fcc8a686f4f9196f3396
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204042"
---
# <a name="azure-firewall-forced-tunneling"></a>Wymuszone tunelowanie zapory platformy Azure

Podczas konfigurowania nowej zapory platformy Azure można skierować cały ruch związany z Internetem do określonego następnego przeskoku zamiast bezpośredniego przejścia do Internetu. Na przykład lokalna Zapora brzegowa lub inne wirtualne urządzenie sieciowe (urządzenie WUS) mogą przetwarzać ruch sieciowy przed przekazaniem go do Internetu. Nie można jednak skonfigurować istniejącej zapory do wymuszonego tunelowania.

Domyślnie Wymuszone tunelowanie nie jest dozwolone w zaporze platformy Azure, aby upewnić się, że wszystkie jej wychodzące zależności platformy Azure są spełnione. Konfiguracje trasy zdefiniowane przez użytkownika (UDR) w *AzureFirewallSubnet* , które mają trasę domyślną nie przechodzą bezpośrednio do Internetu, są wyłączone.

## <a name="forced-tunneling-configuration"></a>Konfiguracja wymuszonego tunelowania

Aby zapewnić obsługę tunelowania wymuszonego, ruch związany z zarządzaniem usługami jest oddzielony od ruchu klienta. Dodatkowa podsieć dedykowana o nazwie *AzureFirewallManagementSubnet* (minimalny rozmiar podsieci/26) jest wymagana ze skojarzonym publicznym adresem IP. Jedyną trasą dozwoloną w tej podsieci jest trasa domyślna do Internetu, a Propagacja trasy BGP musi być wyłączona.

Jeśli trasa domyślna jest anonsowana za pośrednictwem protokołu BGP, aby wymusić ruch w środowisku lokalnym, należy utworzyć *AzureFirewallSubnet* i *AzureFirewallManagementSubnet* przed wdrożeniem zapory i mieć UDR z domyślną trasą do Internetu, a **Propagacja trasy bramy sieci wirtualnej** jest wyłączona.

W ramach tej konfiguracji *AzureFirewallSubnet* może teraz zawierać trasy do dowolnej zapory lokalnej lub urządzenie WUS, aby przetwarzać ruch przed przekazaniem go do Internetu. Możesz również opublikować te trasy za pośrednictwem protokołu BGP do *AzureFirewallSubnet* , jeśli w tej podsieci jest włączona **Propagacja trasy bramy sieci wirtualnej** .

Na przykład możesz utworzyć trasę domyślną na *AzureFirewallSubnet* z bramą sieci VPN w ramach następnego skoku, aby przejść do urządzenia lokalnego. Można też włączyć **propagację tras bramy sieci wirtualnej** w celu uzyskania odpowiednich tras do sieci lokalnej.

![Propagacja trasy bramy sieci wirtualnej](media/forced-tunneling/route-propagation.png)

Po skonfigurowaniu zapory platformy Azure do obsługi wymuszonego tunelowania nie można cofnąć konfiguracji. Jeśli usuniesz wszystkie inne konfiguracje protokołu IP w zaporze, Konfiguracja protokołu IP zarządzania zostanie również usunięta, a Zapora zostanie cofnięta. Nie można usunąć publicznego adresu IP przypisanego do konfiguracji adresu IP zarządzania, ale można przypisać inny publiczny adres IP.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md)
