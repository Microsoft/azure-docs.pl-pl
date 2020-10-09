---
title: Przegląd ciągłości biznesowej
titleSuffix: Microsoft Genomics
description: Ten przegląd zawiera opis możliwości, które Microsoft Genomics zapewnia ciągłość działania i odzyskiwanie po awarii.
keywords: ciągłość działalności biznesowej, odzyskiwanie po awarii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72249175"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Przegląd ciągłości działania z Microsoft Genomics
Ten przegląd zawiera opis możliwości, które Microsoft Genomics zapewnia ciągłość działania i odzyskiwanie po awarii. Informacje o opcjach odzyskiwania zdarzeń powodujących zakłócenia, takich jak awaria regionu platformy Azure, które mogą spowodować utratę danych. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkcje, które obsługują ciągłość działania 
Chociaż rzadko centrum danych platformy Azure może mieć awarie, co może spowodować zakłócenie działania firmy, które może trwać kilka minut. Gdy wystąpi awaria, wszystkie zadania aktualnie uruchomione w centrum danych zakończą się niepowodzeniem, a usługa Microsoft Genomics nie będzie automatycznie ponownie przesyłać zadań do regionu pomocniczego. 

* Jedną z opcji jest poczekanie, aż centrum danych powróci do trybu online, gdy wystąpiła awaria centrum danych. Jeśli awaria jest krótka, usługa Microsoft Genomics automatycznie wykryje zadania zakończone niepowodzeniem, a przepływ pracy zostanie automatycznie uruchomiony ponownie.

* Kolejną opcją jest zaaktywnie przesyłanie przepływu pracy w innym regionie danych. Microsoft Genomics wdraża wystąpienia w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/regions/services/), a każde wystąpienie specyficzne dla regionu jest niezależne. Jeśli jedno z wystąpień Microsoft Genomics wystąpi awaria lokalnego regionu, inne regiony, w których uruchomione są wystąpienia Microsoft Genomics, będą kontynuowały przetwarzanie zadań. Przeniesienie do alternatywnego regionu jest pod kontrolą użytkownika i dostępne w dowolnym momencie.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ręczne przełączenie w tryb failover Microsoft Genomics przepływów pracy do innego regionu
Jeśli wystąpi awaria regionalnego centrum danych, możesz przesłać Microsoft Genomics przepływy pracy w regionie pomocniczym, na podstawie indywidualnych wymagań związanych z jurysdykcją i ciągłością biznesową. Aby ręcznie Microsoft Genomics przepływów pracy w trybie failover, należy użyć innego regionu. Genomika konta i przesłanie zadania z odpowiednimi poświadczeniami specyficznymi dla regionu i kontami magazynu.

W odniesieniu do tego należy:
* Utwórz konto genomiki w regionie pomocniczym przy użyciu Azure Portal. 
* Przeprowadź migrację danych wejściowych do konta magazynu w regionie pomocniczym i skonfiguruj folder wyjściowy w regionie pomocniczym.
* Prześlij przepływ pracy w regionie pomocniczym.

Po przywróceniu oryginalnego regionu usługa Microsoft Genomics nie migruje danych z regionu pomocniczego z powrotem do oryginalnego regionu. Możesz wybrać przenoszenie plików wejściowych i wyjściowych z regionu pomocniczego z powrotem do regionu oryginalnego.  Jeśli zdecydujesz się przenieść swoje dane, będzie to miało miejsce poza usługą genomiki, a wszystkie opłaty związane z przenoszeniem danych będą Twoje obowiązki. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Przygotowanie do potencjalnej awarii specyficznej dla regionu
Jeśli chodzi o szybsze odzyskiwanie w przypadku awarii centrum danych, istnieje kilka kroków, które można wykonać, aby skrócić czas, w którym trzeba ręcznie ponownie przesłać przepływy pracy Microsoft Genomics do regionu pomocniczego:

* Identyfikowanie odpowiedniego regionu pomocniczego i zaawansowanego tworzenia konta genomiki w tym regionie
* Duplikuj dane w regionie podstawowym i pomocniczym, aby dane były natychmiast dostępne w regionie pomocniczym. Można to zrobić ręcznie lub za pomocą funkcji [magazynu geograficznie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dostępnej w usłudze Azure Storage. 

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera informacje o opcjach zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii podczas korzystania z usługi Microsoft Genomics. Aby uzyskać więcej informacji na temat ciągłości działania i odzyskiwania po awarii na platformie Azure, zobacz [Wskazówki techniczne dotyczące odporności na platformie Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 