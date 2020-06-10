---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629530"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Jeśli magazyn danych jest skonfigurowany w jeden z następujących sposobów, należy skonfigurować [własne środowisko Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , aby nawiązać połączenie z magazynem danych:

- Magazyn danych znajduje się w sieci lokalnej, wewnątrz sieci wirtualnej platformy Azure lub w ramach wirtualnej chmury prywatnej Amazon.
- Magazyn danych to zarządzana usługa danych w chmurze, do której dostęp jest ograniczony do adresów IP, które są listy dozwolonych w regułach zapory.
