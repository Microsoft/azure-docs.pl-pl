---
title: Pobieranie informacji o usłudze równoważenia obciążenia przy użyciu usługi Azure Instance Metadata Service
titleSuffix: Azure Load Balancer
description: Rozpocznij uczenie się dotyczące korzystania z usługi Azure Instance Metadata Service w celu pobrania informacji o usłudze równoważenia obciążenia.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519086"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Pobieranie informacji o usłudze równoważenia obciążenia przy użyciu usługi Azure Instance Metadata Service

IMDS (platforma Azure Instance Metadata Service) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych. Usługa jest interfejsem API REST, który jest dostępny z dobrze znanym adresem IP bez obsługi routingu (169.254.169.254). 

Po umieszczeniu wystąpień maszyny wirtualnej lub zestawu maszyn wirtualnych za usługa Load Balancer w warstwie Standardowaem platformy Azure, użyj IMDS, aby pobrać metadane związane z modułem równoważenia obciążenia i wystąpieniami.

Metadane obejmują następujące informacje dotyczące maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych:

* Publiczny adres IP jednostki SKU w warstwie Standardowa.
* Konfiguracje reguł ruchu przychodzącego modułu równoważenia obciążenia każdego prywatnego adresu IP interfejsu sieciowego.
* Konfiguracje reguły ruchu wychodzącego modułu równoważenia obciążenia każdego prywatnego adresu IP interfejsu sieciowego.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Dostęp do metadanych usługi równoważenia obciążenia przy użyciu IMDS

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metadanych modułu równoważenia obciążenia, zobacz temat [Korzystanie z usługi Azure instance Metadata Service w celu uzyskania dostępu do informacji modułu równoważenia obciążenia](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Rozwiązywanie typowych kodów błędów

Aby uzyskać więcej informacji na temat typowych kodów błędów i ich metod zaradczych, zobacz [Rozwiązywanie typowych kodów błędów podczas korzystania z IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Pomoc techniczna

Jeśli nie możesz pobrać odpowiedzi metadanych po wielu próbach, Utwórz problem z pomocą techniczną w Azure Portal.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [usłudze Azure instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Wdrażanie usługi równoważenia obciążenia w warstwie Standardowa](quickstart-load-balancer-standard-public-portal.md)

