---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023198"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Jeśli magazyn danych znajduje się w sieci lokalnej, w sieci wirtualnej platformy Azure lub wirtualnej chmurze prywatnej firmy Amazon, musisz skonfigurować [własne środowisko Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , aby nawiązać z nim połączenie.

Alternatywnie, jeśli magazyn danych jest zarządzaną usługą danych w chmurze, możesz użyć środowiska Azure Integration Runtime. Jeśli dostęp jest ograniczony do adresów IP, które są zatwierdzone w regułach zapory, można dodać [Azure Integration Runtime adresy IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) do listy dozwolonych. 

Aby uzyskać więcej informacji na temat mechanizmów i opcji zabezpieczeń sieciowych obsługiwanych przez Data Factory, zobacz [strategie dostępu do danych](../articles/data-factory/data-access-strategies.md).
