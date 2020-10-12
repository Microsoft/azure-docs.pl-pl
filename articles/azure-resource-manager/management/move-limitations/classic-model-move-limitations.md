---
title: Przenoszenie klasycznych zasobów wdrażania platformy Azure
description: Użyj Azure Resource Manager, aby przenieść klasyczne zasoby wdrożenia do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75485288"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Wskazówki dotyczące przenoszenia dla zasobów klasycznego modelu wdrażania

Kroki dotyczące przenoszenia zasobów wdrożonych za pośrednictwem modelu klasycznego różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy na nową subskrypcję.

## <a name="move-in-the-same-subscription"></a>Przenieś w tej samej subskrypcji

Gdy przenosisz zasoby z jednej grupy zasobów do innej grupy zasobów w ramach tej samej subskrypcji, mają zastosowanie następujące ograniczenia:

* Nie można przenieść sieci wirtualnych (klasycznych).
* Maszyny wirtualne (klasyczne) należy przenieść przy użyciu usługi w chmurze.
* Usługę w chmurze można przenieść tylko wtedy, gdy przeniesienie obejmuje wszystkie jej maszyny wirtualne.
* Jednocześnie można przenieść tylko jedną usługę w chmurze.
* Jednocześnie można przenieść tylko jedno konto magazynu (klasyczne).
* Nie można przenieść konta magazynu (klasycznego) do tej samej operacji z maszyną wirtualną lub usługą w chmurze.

Aby przenieść zasoby klasyczne do nowej grupy zasobów w ramach tej samej subskrypcji, użyj [standardowych operacji przenoszenia](../move-resource-group-and-subscription.md) w portalu, Azure PowerShell, INTERFEJSIE wiersza polecenia platformy Azure lub interfejsu API REST. Te same operacje są używane do przeniesienia Menedżer zasobów zasobów.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

W przypadku przeniesienia zasobów do nowej subskrypcji obowiązują następujące ograniczenia:

* Wszystkie klasyczne zasoby w subskrypcji muszą być przenoszone w ramach tej samej operacji.
* Subskrypcja docelowa nie może mieć żadnych innych zasobów klasycznych.
* Do przenoszenia można żądać tylko za pomocą oddzielnego interfejsu API REST dla ruchów klasycznych. Standardowe Menedżer zasobów przenoszenia poleceń nie działają podczas przenoszenia zasobów klasycznych do nowej subskrypcji.

Aby przenieść zasoby klasyczne do nowej subskrypcji, użyj operacji REST, które są specyficzne dla zasobów klasycznych. Aby użyć REST, wykonaj następujące czynności:

1. Sprawdź, czy subskrypcja źródłowa może uczestniczyć w przenoszeniu między subskrypcjami. Użyj następującej operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania należy uwzględnić:

   ```json
   {
    "role": "source"
   }
   ```

     Odpowiedź na operację walidacji ma następujący format:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Sprawdź, czy subskrypcja docelowa może uczestniczyć w przenoszeniu między subskrypcjami. Użyj następującej operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     W treści żądania należy uwzględnić:

   ```json
   {
    "role": "target"
   }
   ```

     Odpowiedź jest w tym samym formacie co Walidacja subskrypcji źródłowej.
1. Jeśli subskrypcje przechodzą weryfikację, Przenieś wszystkie klasyczne zasoby z jednej subskrypcji do innej subskrypcji przy użyciu następującej operacji:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    W treści żądania należy uwzględnić:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Operacja może być wykonywana przez kilka minut.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problem z przeniesieniem zasobów klasycznych, skontaktuj się z [pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
