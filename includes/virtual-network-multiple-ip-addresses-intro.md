---
title: plik dołączany
description: plik dołączany
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: c63160d7514dccb0d2a9c2879db6d3fd614e1a96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646396"
---
> [!div class="op_single_selector"]
> * [Azure Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [Program PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Do maszyny wirtualnej platformy Azure jest dołączony co najmniej jeden interfejs sieciowy (karta sieciowa). Każda karta sieciowa może mieć przypisany jeden lub wiele statycznych lub dynamicznych publicznych i prywatnych adresów IP. Przypisywanie wielu adresów IP do maszyny wirtualnej włącza następujące możliwości:

* Hostowanie wielu witryn sieci Web lub usług z różnymi adresami IP i certyfikatami SSL na jednym serwerze.
* Może służyć jako sieciowe urządzenie wirtualne, takie jak zapora lub moduł równoważenia obciążenia.
* Możliwość dodania dowolnego z prywatnych adresów IP dla dowolnej z kart sieciowych do puli zaplecza usługi Azure Load Balancer. W przeszłości tylko podstawowy adres IP podstawowej karty sieciowej można było dodać do puli zaplecza. Aby dowiedzieć się więcej na temat sposobu równoważenia obciążenia dla wielu konfiguracji adresów IP, zapoznaj się z artykułem [Load balancing multiple IP configurations](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Równoważenie obciążenia dla wielu konfiguracji adresów IP).

Z każdą kartą sieciową dołączoną do maszyny wirtualnej jest skojarzona co najmniej jedna konfiguracja adresu IP. Każdej konfiguracji jest przypisany jeden statyczny lub dynamiczny prywatny adres IP. Każda konfiguracja może mieć również powiązany jeden zasób publicznego adresu IP. Zasób publicznego adresu IP ma przypisany dynamiczny lub statyczny publiczny adres IP. Aby dowiedzieć się więcej o adresach IP na platformie Azure, zapoznaj się z artykułem [Adresy IP na platformie Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Istnieje limit liczby prywatnych adresów IP, które można przypisać do karty sieciowej. Istnieje również limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł dotyczący [limitów platformy Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
