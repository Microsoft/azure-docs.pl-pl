---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544546"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Jeśli magazyn danych znajduje się w sieci lokalnej, w sieci wirtualnej platformy Azure lub wirtualnej chmurze prywatnej firmy Amazon, musisz skonfigurować [własne środowisko Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , aby nawiązać z nim połączenie.

Jeśli magazyn danych jest zarządzaną usługą danych w chmurze, w której dostęp jest ograniczony do adresów IP, które są listy dozwolonych w regułach zapory, możesz użyć środowiska Azure Integration Runtime i dodać [jego adresy IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) do listy dozwolonych. 

Zobacz [strategie dostępu do danych](../articles/data-factory/data-access-strategies.md) , aby uzyskać informacje o mechanizmach zabezpieczeń sieci obsługiwanych przez Data Factory w celu ogólnego dostępu do magazynów danych.
