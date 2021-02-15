---
title: Adresy IP usługi Azure Integration Runtime
description: Dowiedz się, które adresy IP należy zezwolić na ruch przychodzący z programu, w celu poprawnego skonfigurowania zapór do zabezpieczania dostępu sieciowego do magazynów danych.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371400"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresy IP usługi Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adresy IP używane przez Azure Integration Runtime są zależne od regionu, w którym znajduje się środowisko Azure Integration Runtime. *Wszystkie* Środowiska Azure Integration Runtime, które znajdują się w tym samym regionie, używają tych samych zakresów adresów IP.

> [!IMPORTANT]  
> Przepływy danych i Azure Integration Runtime, które włączają zarządzane Virtual Network nie obsługują używania stałych zakresów adresów IP.
>
> Możesz użyć tych zakresów adresów IP na potrzeby przenoszenia danych, wykonywania potoku i działań zewnętrznych. Te zakresy adresów IP mogą służyć do filtrowania w magazynach danych/Network Security Group (sieciowej grupy zabezpieczeń)/zaporach na potrzeby dostępu przychodzącego z platformy Azure Integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime adresy IP: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla środowiska Azure Integration Runtime w określonym regionie świadczenia usługi Azure, w którym znajdują się zasoby. Listę zakresów adresów IP można uzyskać z [linku pobierania zakresu adresów IP znaczników usług](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Na przykład jeśli region platformy Azure to **AustraliaEast**, można uzyskać listę zakresów adresów IP z **DataFactory. AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Znany problem z usługą Azure Storage

* W przypadku nawiązywania połączenia z kontem usługi Azure Storage reguły sieci IP nie mają wpływu na żądania pochodzące z platformy Azure Integration Runtime w tym samym regionie, w którym znajduje się konto magazynu. Aby uzyskać więcej informacji, [zapoznaj się z tym artykułem](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Zamiast tego sugerujemy używanie [zaufanych usług podczas nawiązywania połączenia z usługą Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w Azure Data Factory](data-movement-security-considerations.md)