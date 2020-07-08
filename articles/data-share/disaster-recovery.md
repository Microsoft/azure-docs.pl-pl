---
title: Odzyskiwanie po awarii dla udziału danych platformy Azure
description: Odzyskiwanie po awarii dla udziału danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483182"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Odzyskiwanie po awarii dla udziału danych platformy Azure

W tym artykule omówiono sposób konfigurowania środowiska odzyskiwania po awarii dla udziału danych platformy Azure. Awaria centrum danych platformy Azure jest rzadka, ale może być w dowolnym miejscu od kilku minut do godzin. Awaria centrum danych może spowodować zakłócenia w środowiskach, które są zależne od danych udostępnianych przez dostawcę danych. Postępując zgodnie z krokami opisanymi w tym artykule, dostawcy danych mogą nadal udostępniać dane swoim odbiorcom danych w przypadku awarii centrum danych w regionie podstawowym hostującym udział danych. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Osiąganie ciągłości biznesowej dla udziału danych platformy Azure

Aby przygotować się do awarii centrum danych, dostawca danych może mieć udostępnione środowisko udostępniania danych w regionie pomocniczym. Istnieją miary, które można podjąć, aby zapewnić bezproblemową pracę w trybie failover w przypadku wystąpienia awarii centrum danych. 

Dostawcy danych mogą inicjować obsługę administracyjną zasobów udziałów danych platformy Azure w dodatkowym regionie. Te zasoby udostępniające dane można skonfigurować tak, aby obejmowały zestawy danych, które istnieją w podstawowym środowisku udostępniania. Odbiorcy danych mogą być dodawani do udziału danych podczas konfigurowania środowiska odzyskiwania po awarii lub dodanych w późniejszym czasie (tj. w ramach ręcznego kroku trybu failover).

Jeśli odbiorcy danych mają aktywną subskrypcję udziałów w środowisku pomocniczym zainicjowanym w celu odzyskiwania po awarii, mogą włączyć harmonogram migawek w ramach trybu failover. Jeśli odbiorcy danych nie chcą subskrybować regionu pomocniczego na potrzeby odzyskiwania po awarii, mogą zostać zaproszeni do pomocniczego udziału danych w późniejszym czasie. 

Konsumenci danych mogą mieć aktywną subskrypcję udziałów, która jest bezczynna dla celów odzyskiwania po awarii, lub dostawców danych może dodawać je w późniejszym momencie w czasie w ramach ręcznych procedur trybu failover. 

## <a name="related-information"></a>Informacje pokrewne

- [Ciągłość działania i odzyskiwanie po awarii](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Wbudowywanie wysokiej dostępności w strategię zapewniania ciągłości biznesowej i odzyskiwania po awarii](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .




