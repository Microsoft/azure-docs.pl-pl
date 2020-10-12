---
title: Używanie aparatu reguł do wymuszania protokołu HTTPS w standardowej Azure CDN | Microsoft Docs
description: Użyj aparatu reguł dla programu Microsoft Standard Azure Content Delivery Network (Azure CDN), aby dostosować sposób, w jaki Azure CDN obsługuje żądania HTTP, w tym blokowanie dostarczania określonych typów zawartości, definiowanie zasad buforowania i modyfikowanie nagłówków HTTP. W tym artykule dowiesz się, jak utworzyć regułę przekierowania użytkowników do protokołu HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 1c596bf10d8afbce504b5abf04eacb877989521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887530"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Skonfiguruj aparat reguł standardowych dla Azure CDN

W tym artykule opisano sposób konfigurowania standardowego aparatu reguł dla platformy Azure Content Delivery Network (Azure CDN) i korzystania z niego.

## <a name="standard-rules-engine"></a>Aparat reguł standardowych

Aby dostosowywać sposób obsługi żądań HTTP, można użyć aparatu reguł standardowych dla Azure CDN. Można na przykład użyć aparatu reguł, aby wymusić dostarczanie zawartości przy użyciu określonych protokołów, zdefiniować zasady buforowania lub zmodyfikować nagłówek HTTP. W tym artykule pokazano, jak utworzyć regułę, która automatycznie przekierowuje użytkowników do protokołu HTTPS. 

> [!NOTE]
> Aparat reguł opisany w tym artykule jest dostępny tylko dla standardowej Azure CDN firmy Microsoft. 

## <a name="redirect-users-to-https"></a>Przekierowywanie użytkowników do protokołu HTTPS

1. W swoich profilach firmy Microsoft przejdź do pozycji Azure Content Delivery Network.

1. Na stronie **profil usługi CDN** wybierz punkt końcowy, dla którego chcesz utworzyć reguły.
  
1. Wybierz kartę **Aparaty reguł** .
   
    Zostanie otwarte okienko **reguły aparatu** i zostanie wyświetlona lista dostępnych reguł globalnych. 
   
    [![Strona Azure CDN nowych reguł](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Kolejność, w której są wyświetlane wiele reguł, ma wpływ na sposób obsługi reguł. Akcje określone w regule mogą zostać zastąpione przez kolejną regułę.
   >

1. Wybierz pozycję **Dodaj regułę** i wprowadź nazwę reguły. Nazwy reguł muszą zaczynać się literą i mogą zawierać tylko cyfry i litery.

1. Aby zidentyfikować typ żądań, których dotyczy reguła, Utwórz warunek dopasowania:
    1. Wybierz pozycję **Dodaj warunek**, a następnie wybierz warunek dopasowania **protokołu żądania** .
    1. W polu **Operator**wybierz pozycję **Równa się**.
    1. W obszarze **wartość**wybierz pozycję **http**.
   
   [![Warunek dopasowania reguły Azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Z listy rozwijanej **Dodaj warunek** można wybrać spośród wielu warunków dopasowywania. Aby uzyskać szczegółową listę warunków dopasowania, zobacz [warunki dopasowania w aparacie standardowych reguł](cdn-standard-rules-engine-match-conditions.md).
   
1. Wybierz akcję, która ma zostać zastosowana do żądań, które spełniają warunek dopasowania:
   1. Wybierz pozycję **Dodaj akcję**, a następnie wybierz pozycję **Przekierowywanie adresu URL**.
   1. W obszarze **Typ**wybierz pozycję **znaleziono (302)**.
   1. W polu **Protocol** (Protokół) wybierz pozycję **HTTPS**.
   1. Pozostaw puste pole, aby użyć wartości przychodzących.
   
   [![Akcja reguły Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Z listy rozwijanej **Dodaj akcję** można wybrać jedną z wielu akcji. Aby uzyskać szczegółową listę akcji, zobacz [Akcje w aparacie standardowych reguł](cdn-standard-rules-engine-actions.md).

6. Wybierz pozycję **Zapisz** , aby zapisać nową regułę. Ta reguła jest teraz dostępna do użycia.
   
   > [!IMPORTANT]
   > Propagowanie zmian zasad może potrwać do 15 minut w Azure CDN.
   >
   

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowania w aparacie standardowych reguł](cdn-standard-rules-engine-match-conditions.md)
- [Akcje w aparacie reguł standardowych](cdn-standard-rules-engine-actions.md)
