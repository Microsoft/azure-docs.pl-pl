---
title: Odzyskiwanie po awarii dla udziału danych platformy Azure
description: Odzyskiwanie po awarii dla udziału danych platformy Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 61804aaf65a97485e2b2b5bb4869c335a14ce812
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513562"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Odzyskiwanie po awarii dla udziału danych platformy Azure

W tym artykule wyjaśniono, jak skonfigurować środowisko odzyskiwania po awarii dla udziału danych platformy Azure. Awaria centrum danych platformy Azure jest rzadka, ale może być w dowolnym miejscu od kilku minut do godzin. Awaria centrum danych może spowodować zakłócenia w środowiskach, które są zależne od danych udostępnianych przez dostawcę danych. Postępując zgodnie z krokami opisanymi w tym artykule, dostawcy danych mogą nadal udostępniać dane swoim odbiorcom danych w przypadku awarii centrum danych w regionie podstawowym hostującym udział danych. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Osiąganie ciągłości biznesowej dla udziału danych platformy Azure

Aby przygotować się do awarii centrum danych, dostawca danych może mieć udostępnione środowisko udostępniania danych w regionie pomocniczym. Miary mogą być podejmowane w celu zapewnienia płynnej pracy w trybie failover w przypadku wystąpienia awarii centrum danych. 

Dostawcy danych mogą inicjować obsługę administracyjną zasobów udziałów danych platformy Azure w dodatkowym regionie. Te zasoby udziału danych można skonfigurować w taki sposób, aby obejmowały udziały i zestawy danych, które istnieją w podstawowym zasobie udziału danych platformy Azure. Mogą zapraszać klientów danych do udziałów pomocniczych podczas konfigurowania środowiska odzyskiwania po awarii lub w późniejszym czasie (tj. w ramach ręcznego kroku trybu failover).

Jeśli odbiorcy danych mają aktywne subskrypcje udziałów w środowisku pomocniczym, które są udostępniane na potrzeby odzyskiwania po awarii, mogą włączyć harmonogram migawek w ramach trybu failover. Jeśli odbiorcy danych nie chcą subskrybować regionu pomocniczego na potrzeby odzyskiwania po awarii, mogą zostać zaproszeni do udziału pomocniczego w późniejszym czasie. 

Konsumenci danych mogą mieć aktywną subskrypcję udziałów, która jest bezczynna w celach odzyskiwania po awarii, lub dostawcy danych mogą zapraszać je w późniejszym czasie w ramach ręcznych procedur trybu failover. 

## <a name="related-information"></a>Informacje pokrewne

- [Ciągłość działania i odzyskiwanie po awarii](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Wbudowywanie wysokiej dostępności w strategię zapewniania ciągłości biznesowej i odzyskiwania po awarii](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .




