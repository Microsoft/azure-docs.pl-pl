---
title: Konfigurowanie Azure Analysis Services dla źródeł danych sieci wirtualnej | Microsoft Docs
description: Dowiedz się, jak skonfigurować serwer Azure Analysis Services, aby używał bramy dla źródeł danych w usłudze Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73572274"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Używanie bramy dla źródeł danych w usłudze Azure Virtual Network (Sieć wirtualna)

W tym artykule opisano Azure Analysis Services właściwość serwera **AlwaysUseGateway** , która ma być używana, gdy źródła danych znajdują się w usłudze [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Dostęp serwera do źródeł danych sieci wirtualnej

Jeśli dostęp do źródeł danych odbywa się za pomocą sieci wirtualnej, serwer Azure Analysis Services musi połączyć się z tymi źródłami danych, tak jakby znajdowały się one lokalnie, w Twoim środowisku. Można skonfigurować właściwość serwera **AlwaysUseGateway** , aby określić serwer, który ma uzyskać dostęp do wszystkich źródeł danych za pomocą [bramy lokalnej](analysis-services-gateway.md). 

Azure SQL Database źródła danych wystąpienia zarządzanego są uruchamiane w sieci wirtualnej platformy Azure z prywatnym adresem IP. Jeśli w wystąpieniu jest włączony publiczny punkt końcowy, Brama nie jest wymagana. Jeśli publiczny punkt końcowy nie jest włączony, wymagana jest lokalna Brama danych i Właściwość AlwaysUseGateway musi mieć wartość true.

> [!NOTE]
> Ta właściwość obowiązuje tylko wtedy, gdy [lokalna Brama danych](analysis-services-gateway.md) została zainstalowana i skonfigurowana. Brama może znajdować się w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Skonfiguruj Właściwość AlwaysUseGateway

1. W obszarze SSMS > Server > **Właściwości** > **Ogólne**wybierz pozycję **Pokaż zaawansowane (wszystkie) właściwości**.
2. W **ASPaaS\AlwaysUseGateway**wybierz **wartość true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz także
[Łączenie z lokalnymi źródłami danych](analysis-services-gateway.md)   
[Instalowanie i Konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md)   
[Virtual Network platformy Azure (Sieć wirtualna)](../virtual-network/virtual-networks-overview.md)   

