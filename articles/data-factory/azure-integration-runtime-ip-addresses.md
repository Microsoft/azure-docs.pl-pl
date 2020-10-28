---
title: Adresy IP usługi Azure Integration Runtime
description: Dowiedz się, które adresy IP należy zezwolić na ruch przychodzący z programu, w celu poprawnego skonfigurowania zapór do zabezpieczania dostępu sieciowego do magazynów danych.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: fb83be59a3ccb11ea8bbd88307596937caa2b354
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638299"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresy IP usługi Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adresy IP używane przez Azure Integration Runtime są zależne od regionu, w którym znajduje się środowisko Azure Integration Runtime. *Wszystkie* Środowiska Azure Integration Runtime, które znajdują się w tym samym regionie, używają tych samych zakresów adresów IP.

> [!IMPORTANT]  
> Przepływy danych i Azure Integration Runtime, które włączają zarządzane Virtual Network nie obsługują używania stałych zakresów adresów IP.
>
> Możesz użyć tych zakresów adresów IP na potrzeby przenoszenia danych, wykonywania potoku i działań zewnętrznych. Te zakresy adresów IP mogą służyć do filtrowania w magazynach danych/Network Security Group (sieciowej grupy zabezpieczeń)/zaporach na potrzeby dostępu przychodzącego z platformy Azure Integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime adresy IP: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla środowiska Azure Integration Runtime w określonym regionie świadczenia usługi Azure, w którym znajdują się zasoby. Listę zakresów adresów IP można uzyskać z [linku pobierania zakresu adresów IP znaczników usług](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Na przykład jeśli region platformy Azure to **AustraliaEast** , można uzyskać listę zakresów adresów IP z **DataFactory. AustraliaEast** .


## <a name="known-issue-with-azure-storage"></a>Znany problem z usługą Azure Storage

* W przypadku nawiązywania połączenia z kontem usługi Azure Storage reguły sieci IP nie mają wpływu na żądania pochodzące z platformy Azure Integration Runtime w tym samym regionie, w którym znajduje się konto magazynu. Aby uzyskać więcej informacji, [zapoznaj się z tym artykułem](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Zamiast tego sugerujemy używanie [zaufanych usług podczas nawiązywania połączenia z usługą Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w Azure Data Factory](data-movement-security-considerations.md)