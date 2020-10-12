---
title: Często zadawane pytania dotyczące dostępności aplikacji i usług
titleSuffix: Azure Cloud Services
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących dostępności aplikacji i usług dla Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d7a2596d4f6a8aa9fb4c95dfb5a3ccab7d7206f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461310"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z dostępnością aplikacji i usług dla platformy Azure Cloud Services: często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące problemów z dostępnością aplikacji i usług w programie [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informacje o rozmiarze można także znaleźć na [stronie Cloud Services rozmiaru maszyny wirtualnej](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moja rola została odtworzona. Czy była dostępna aktualizacja dla mojej usługi w chmurze?
Około miesiąca firma Microsoft udostępnia nową wersję systemu operacyjnego gościa dla maszyn wirtualnych PaaS platformy Microsoft Azure.System operacyjny gościa to tylko jedna taka aktualizacja w potoku. Na wydanie może mieć wpływ wiele innych czynników. Ponadto platforma Azure działa na setkach tysięcy maszyn. W związku z tym nie można przewidzieć dokładnej daty i godziny ponownego uruchomienia ról. Aktualizujemy kanał informacyjny RSS systemu operacyjnego gościa z najnowszymi informacjami, które mamy, ale należy wziąć pod uwagę, że raportowany czas musi być przybliżoną wartością. Mamy świadomość, że jest to problematyczne dla klientów i pracujemy nad planem w celu ograniczenia lub precyzyjnego ponownego uruchomienia.

Aby uzyskać szczegółowe informacje na temat ostatnich aktualizacji systemu operacyjnego gościa, zobacz [wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawu SDK](cloud-services-guestos-update-matrix.md).

Aby uzyskać przydatne informacje dotyczące ponownego uruchamiania i wskaźników do szczegółowych informacji technicznych dotyczących aktualizacji systemu operacyjnego gościa i hosta, zobacz [Ponowne uruchamianie wystąpienia roli w blogu MSDN z powodu uaktualnień systemu operacyjnego](https://docs.microsoft.com/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Dlaczego pierwsze żądanie do mojej usługi w chmurze, gdy usługa jest bezczynna, trwa dłużej niż zwykle?
Gdy serwer sieci Web otrzymuje pierwsze żądanie, najpierw ponownie skompiluje kod, a następnie przetworzy żądanie. Dlatego pierwsze żądanie trwa dłużej niż inne. Domyślnie Pula aplikacji jest zamykana w przypadku nieaktywności użytkownika. Pula aplikacji również będzie odtwarzana domyślnie co 1 740 minut (29 godzin).

Pule aplikacji Internet Information Services (IIS) mogą być okresowo odtwarzane w celu uniknięcia niestabilnych Stanów, które mogą prowadzić do awarii aplikacji, zawieszenia lub przecieków pamięci.

Następujące dokumenty ułatwią zrozumienie i rozwiązanie tego problemu:
* [Naprawianie wolnego początkowej ładowania dla usług IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Pierwsze żądanie aplikacji sieci Web usług IIS 7,5 po powolnej odtworzeniu puli aplikacji](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Jeśli chcesz zmienić domyślne zachowanie usług IIS, musisz użyć zadań uruchamiania, ponieważ w przypadku ręcznego zastosowania zmian w wystąpieniach roli sieci Web zmiany zostaną ostatecznie utracone.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).
