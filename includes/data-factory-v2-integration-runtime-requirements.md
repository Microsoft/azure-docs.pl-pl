---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529396"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Jeśli magazyn danych znajduje się w sieci lokalnej, w sieci wirtualnej platformy Azure lub wirtualnej chmurze prywatnej firmy Amazon, musisz skonfigurować [własne środowisko Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , aby nawiązać z nim połączenie.

Jeśli magazyn danych jest zarządzaną usługą danych w chmurze, możesz użyć platformy Azure Integration Runtime. Jeśli dostęp jest ograniczony do adresów IP, które są listy dozwolonych w regułach zapory, możesz dodać [Azure Integration Runtime adresy IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) do listy dozwolonych. 

Aby uzyskać więcej informacji na temat mechanizmów i opcji zabezpieczeń sieciowych obsługiwanych przez Data Factory, zobacz [strategie dostępu do danych](../articles/data-factory/data-access-strategies.md).
