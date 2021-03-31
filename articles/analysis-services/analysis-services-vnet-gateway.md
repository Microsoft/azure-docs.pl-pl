---
title: Konfigurowanie Azure Analysis Services dla źródeł danych sieci wirtualnej | Microsoft Docs
description: Dowiedz się, jak skonfigurować serwer Azure Analysis Services, aby używał bramy dla źródeł danych w usłudze Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84197194"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Używanie bramy dla źródeł danych w usłudze Azure Virtual Network (VNet)

W tym artykule opisano Azure Analysis Services właściwość serwera **AlwaysUseGateway** , która ma być używana, gdy źródła danych znajdują się w usłudze [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Dostęp serwera do źródeł danych sieci wirtualnej

Jeśli dostęp do źródeł danych odbywa się za pomocą sieci wirtualnej, serwer Azure Analysis Services musi połączyć się z tymi źródłami danych, tak jakby znajdowały się one lokalnie, w Twoim środowisku. Można skonfigurować właściwość serwera **AlwaysUseGateway** , aby określić serwer, który ma uzyskać dostęp do wszystkich źródeł danych za pomocą [bramy lokalnej](analysis-services-gateway.md). 

Źródła danych wystąpienia zarządzanego usługi Azure SQL działają w sieci wirtualnej platformy Azure z prywatnym adresem IP. Jeśli w wystąpieniu jest włączony publiczny punkt końcowy, Brama nie jest wymagana. Jeśli publiczny punkt końcowy nie jest włączony, wymagana jest lokalna Brama danych i Właściwość AlwaysUseGateway musi mieć wartość true.

> [!NOTE]
> Ta właściwość obowiązuje tylko wtedy, gdy [lokalna Brama danych](analysis-services-gateway.md) została zainstalowana i skonfigurowana. Brama może znajdować się w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Skonfiguruj Właściwość AlwaysUseGateway

1. W obszarze SSMS > Server > **Właściwości**  >  **Ogólne** wybierz pozycję **Pokaż zaawansowane (wszystkie) właściwości**.
2. W **ASPaaS\AlwaysUseGateway** wybierz **wartość true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz też
[Łączenie z lokalnymi źródłami danych](analysis-services-gateway.md)   
[Instalowanie i Konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md)   
[Virtual Network platformy Azure (Sieć wirtualna)](../virtual-network/virtual-networks-overview.md)   

