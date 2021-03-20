---
title: Używanie Azure DNS z innymi usługami platformy Azure
description: W tej ścieżce szkoleniowej Zacznij korzystać z usługi Azure DNS do rozwiązywania nazw dla innych usług platformy Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76937255"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak Azure DNS współpracuje z innymi usługami platformy Azure

Azure DNS to Hostowana usługa zarządzania DNS i rozpoznawania nazw. Można jej użyć do tworzenia publicznych nazw DNS dla innych aplikacji i usług wdrażanych na platformie Azure. Tworzenie nazwy dla usługi platformy Azure w domenie niestandardowej jest proste. Wystarczy dodać rekord poprawnego typu dla usługi.

* W przypadku dynamicznie przyznanych adresów IP można utworzyć rekord CNAME DNS, który jest mapowany na nazwę DNS utworzoną przez platformę Azure dla usługi. Standardy DNS uniemożliwiają użycie rekordu CNAME dla wierzchołka strefy. Zamiast tego można użyć rekordu aliasu. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md).
* W przypadku statycznie przydzielonej adresów IP można utworzyć rekord DNS A za pomocą dowolnej nazwy, która zawiera nieprawidłową nazwę *domeny* w wierzchołku strefy.

W poniższej tabeli przedstawiono obsługiwane typy rekordów, których można użyć w różnych usługach platformy Azure. Jak przedstawiono w tabeli, Azure DNS obsługuje tylko rekordy DNS dla zasobów sieciowych dostępnych z Internetu. Nie można używać Azure DNS do rozpoznawania nazw adresów wewnętrznych, prywatnych.

| Usługa platformy Azure | Interfejs sieciowy | Opis |
| --- | --- | --- |
| Azure Application Gateway |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. |
| Azure Load Balancer |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. Load Balancer może mieć publiczny adres IP IPv6, który jest dynamicznie przypisywany. Utwórz rekord CNAME dla adresu IPv6. |
| Azure Traffic Manager |Nazwa publiczna |Można utworzyć rekord aliasu, który jest mapowany na nazwę trafficmanager.net przypisaną do Twojego profilu Traffic Manager. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu do obsługi nazw domen wierzchołków z Traffic Manager](tutorial-alias-tm.md). |
| usług Azure Cloud Services |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |W przypadku statycznie przyznanych adresów IP można utworzyć rekord DNS A. W przypadku dynamicznie przyznanych adresów IP należy utworzyć rekord CNAME, który jest mapowany na nazwę *cloudapp.NET* .|
| Azure App Service | [Zewnętrzny adres IP](dns-custom-domain.md#app-service-web-apps) |W przypadku zewnętrznych adresów IP można utworzyć rekord DNS A. W przeciwnym razie należy utworzyć rekord CNAME, który jest mapowany na nazwę azurewebsites.net. Aby uzyskać więcej informacji, zobacz [Mapowanie niestandardowej nazwy domeny na aplikację platformy Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Menedżer zasobów maszyny wirtualne mogą mieć publiczne adresy IP. Maszyna wirtualna z publicznym adresem IP może być również za modułem równoważenia obciążenia. Można utworzyć rekord DNS A, CNAME lub alias dla adresu publicznego. Tej nazwy niestandardowej można użyć do obejścia adresu VIP w module równoważenia obciążenia. |
| Klasyczne maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Klasyczne maszyny wirtualne utworzone przy użyciu programu PowerShell lub interfejsu wiersza polecenia można skonfigurować przy użyciu dynamicznego lub statycznego (zastrzeżonego) adresu wirtualnego. Można odpowiednio utworzyć rekord CNAME lub rekordu DNS. |
