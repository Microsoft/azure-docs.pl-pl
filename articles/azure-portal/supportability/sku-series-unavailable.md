---
title: Seria regionów lub jednostek SKU jest niedostępna — Azure
description: Niektóre serie jednostek SKU są niedostępne dla wybranej subskrypcji tego regionu, co może wymagać żądania obsługi zarządzania subskrypcją.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: fccde344a06d39bd4bb8e3178444ec20677e2f50
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078514"
---
# <a name="region-or-sku-unavailable"></a>Region lub jednostka SKU jest niedostępna

W tym artykule opisano sposób rozwiązywania problemu z subskrypcją platformy Azure, która nie ma dostępu do regionu lub jednostki SKU maszyny wirtualnej.

## <a name="symptoms"></a>Objawy

Podczas wdrażania maszyny wirtualnej jest wyświetlany jeden z następujących komunikatów o błędach:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Podczas zakupu wystąpień zarezerwowanych maszyn wirtualnych otrzymujesz jeden z następujących komunikatów o błędach:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

W przypadku tworzenia żądania pomocy technicznej w celu zwiększenia przydziału rdzeni obliczeniowych nie można wybrać regionu lub rodziny jednostek SKU.

## <a name="solution"></a>Rozwiązanie

Najpierw Zalecamy rozważenie alternatywnego regionu lub jednostki SKU, która spełnia Twoje wymagania biznesowe.

Jeśli nie możesz znaleźć odpowiedniego regionu lub jednostki SKU, Utwórz [żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **zarządzania subskrypcją** , wykonując następujące czynności:

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**. Następnie wybierz pozycję **nowe żądanie obsługi**.

1. W obszarze **podstawowe**, w obszarze **typ problemu**wybierz pozycję **Zarządzanie subskrypcją**.

1. Wybierz **subskrypcję** i wprowadź krótki opis **podsumowujący**.

   ![Karta podstawy nowego żądania obsługi](./media/SKU-series-unavailable/support-request-basics.png)

1. W obszarze **typ problemu**wybierz **pozycję Wybierz typ problemu**.

1. W obszarze **typ problemu wybierz**opcję, na przykład **nie można uzyskać dostępu do mojej subskrypcji lub zasobu**  >  **mój problem nie jest wymieniony powyżej**. Wybierz pozycję **Zapisz**.

   ![Określ problem dotyczący żądania](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Wybierz pozycję **Dalej: rozwiązania** , aby poznać możliwe rozwiązania. W razie potrzeby wybierz pozycję **Dalej: szczegóły** , aby kontynuować.

1. Wprowadź wszelkie dodatkowe informacje, które można podać wraz z informacjami kontaktowymi.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zweryfikowaniu informacji wybierz pozycję **Utwórz** , aby utworzyć żądanie.

## <a name="send-us-your-suggestions"></a>Wyślij nam swoje sugestie

Zawsze jesteśmy w trakcie korzystania z opinii i sugestii. Wyślij nam swoje [sugestie](https://feedback.azure.com/forums/266794-support-feedback). Ponadto możesz zaangażować się z nami w serwisie [Twitter](https://twitter.com/azuresupport) lub stronie pytań i odpowiedzi na [pytania firmy Microsoft&](/answers/products/azure).

## <a name="learn-more"></a>Więcej informacji

[Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq)