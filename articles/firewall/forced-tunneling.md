---
title: Wymuszone tunelowanie zapory platformy Azure
description: Można skonfigurować Wymuszone tunelowanie w celu kierowania ruchu związanego z Internetem do dodatkowej zapory lub sieciowego urządzenia wirtualnego w celu dalszej obróbki.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597284"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Wymuszone tunelowanie zapory platformy Azure (wersja zapoznawcza)

Zaporę platformy Azure można skonfigurować tak, aby rozsyłać cały ruch związany z Internetem do określonego następnego przeskoku zamiast bezpośrednio do Internetu. Na przykład lokalna Zapora brzegowa lub inne wirtualne urządzenie sieciowe (urządzenie WUS) mogą przetwarzać ruch sieciowy przed przekazaniem go do Internetu.

> [!IMPORTANT]
> Wymuszone tunelowanie w zaporze platformy Azure jest obecnie dostępne w publicznej wersji zapoznawczej.
>
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domyślnie Wymuszone tunelowanie nie jest dozwolone w zaporze platformy Azure, aby upewnić się, że wszystkie jej wychodzące zależności platformy Azure są spełnione. Konfiguracje trasy zdefiniowane przez użytkownika (UDR) w *AzureFirewallSubnet* , które mają trasę domyślną nie przechodzą bezpośrednio do Internetu, są wyłączone.

## <a name="forced-tunneling-configuration"></a>Konfiguracja wymuszonego tunelowania

Aby zapewnić obsługę tunelowania wymuszonego, ruch związany z zarządzaniem usługami jest oddzielony od ruchu klienta. Dodatkowa podsieć dedykowana o nazwie *AzureFirewallManagementSubnet* (minimalny rozmiar podsieci/26) jest wymagana ze skojarzonym publicznym adresem IP. Jedyną trasą dozwoloną w tej podsieci jest trasa domyślna do Internetu, a Propagacja trasy BGP musi być wyłączona.

Jeśli trasa domyślna jest anonsowana za pośrednictwem protokołu BGP, aby wymusić ruch w środowisku lokalnym, należy utworzyć *AzureFirewallSubnet* i *AzureFirewallManagementSubnet* przed wdrożeniem zapory i mieć UDR z domyślną trasą do Internetu, a **Propagacja trasy bramy sieci wirtualnej** jest wyłączona.

W ramach tej konfiguracji *AzureFirewallSubnet* może teraz zawierać trasy do wszelkich lokalnych ZAPÓR lub urządzenie WUS, aby przetwarzać ruch przed przekazaniem go do Internetu. Możesz również opublikować te trasy za pośrednictwem protokołu BGP do *AzureFirewallSubnet* , jeśli w tej podsieci jest włączona **Propagacja trasy bramy sieci wirtualnej** .

Na przykład możesz utworzyć trasę domyślną na *AzureFirewallSubnet* z bramą sieci VPN w ramach następnego przeskoku, aby przejść do urządzenia lokalnego. Można też włączyć **propagację tras bramy sieci wirtualnej** w celu uzyskania odpowiednich tras do sieci lokalnej.

![Propagacja trasy bramy sieci wirtualnej](media/forced-tunneling/route-propagation.png)

Po skonfigurowaniu zapory platformy Azure do obsługi wymuszonego tunelowania nie można cofnąć konfiguracji. Jeśli usuniesz wszystkie inne konfiguracje protokołu IP w zaporze, Konfiguracja protokołu IP zarządzania zostanie również usunięta, a Zapora zostanie cofnięta. Nie można usunąć publicznego adresu IP przypisanego do konfiguracji adresu IP zarządzania, ale można przypisać inny publiczny adres IP.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md)