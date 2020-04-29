---
title: Usuń oferty z witryny Marketplace | Portal Azure Marketplace
description: Usuń oferty na platformie Azure i AppSource Marketplace przy użyciu portal Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80286459"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Usuwanie ofert Azure Marketplace i AppSource oraz jednostek SKU

Z różnych powodów możesz zdecydować się na wycofanie oferty z witryny Microsoft Marketplace, która może przyjmować dwie formy:

- *Usunięcie oferty* gwarantuje, że nowi klienci nie będą już mogli kupować ani wdrażać oferty, ale nie mają wpływu na istniejących klientów, którzy muszą być obsługiwani zgodnie z umową licencyjną i odpowiednimi przepisami. 
- *Zakończenie oferty* to proces zakończenia usługi i/lub umowy licencyjnej między Twoją firmą a istniejącymi klientami. 

Wskazówki i zasady związane z usuwaniem i wygaśnięciem oferty podlegają postanowieniom [umowy wydawcy Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) i [zasadom uczestnictwa](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sekcja [oferująca zawieszenie i usunięcie](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

W tym artykule przedstawiono różne obsługiwane scenariusze usuwania oraz kroki wymagane do wykonania każdego z nich.  

> [!NOTE]
> Możesz usunąć ofertę, która nie została opublikowana, po prostu wybierając przycisk **Usuń** na pasku narzędzi na karcie **Edytor** .


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Usuwanie opublikowanej jednostki SKU z portalu Azure Marketplace

Opublikowaną jednostkę SKU można usunąć z witryny Azure Marketplace, wykonując następujące czynności:

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2.  Na stronie **wszystkie oferty** wybierz swoją ofertę.  Twoja oferta powinna zostać wyświetlona na karcie **Edytor** .
3.  Na pasku narzędzi po lewej stronie wybierz kartę **jednostki SKU** . 
4.  Wybierz jednostkę SKU, którą chcesz usunąć, a następnie kliknij przycisk **Usuń** .
5.  [Opublikuj ponownie](./cpp-publish-offer.md) ofertę w witrynie Azure Marketplace.

Po opublikowaniu zmodyfikowanej oferty w portalu Azure Marketplace wybrana jednostka SKU nie zostanie już wymieniona w witrynie Azure Marketplace i Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Przywróć poprzednią wersję SKU

Bieżącą wersję opublikowanej jednostki SKU można usunąć z witryny Azure Marketplace, wykonując kroki opisane tutaj. Po zakończeniu procesu jednostka SKU zostanie wycofana do poprzedniej wersji.

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. Na stronie **wszystkie oferty** wybierz swoją ofertę.  Twoja oferta powinna zostać wyświetlona na karcie **Edytor** .
3. Na pasku narzędzi po lewej stronie wybierz kartę **jednostki SKU** . 
4. Usuń najnowszą wersję skojarzonego elementu zawartości rozwiązania z listy wersji dysków.  W zależności od typu oferty to pole może być **wersją dysku**, **wersjami pakietu**lub podobnym zasobem. 
5. [Opublikuj ponownie](./cpp-publish-offer.md) ofertę w witrynie Azure Marketplace.

Po opublikowaniu zmodyfikowanej oferty w witrynie theAzure Marketplace bieżąca wersja wymienionej jednostki SKU nie będzie już wyświetlana. w witrynie Azure Marketplace i Azure Portal.  Jednostka SKU jest wycofywana do poprzedniej wersji.


## <a name="delete-a-live-offer"></a>Usuń ofertę na żywo

Istnieją różne aspekty proceduralne, biznesowe i prawne dotyczące usuwania oferty na żywo. Wykonaj następujące kroki, aby uzyskać wskazówki od zespołu pomocy technicznej w celu usunięcia oferty na żywo z witryny Azure Marketplace:

1.  Zgłoś bilet pomocy technicznej przy użyciu strony [Tworzenie zdarzenia](https://go.microsoft.com/fwlink/?linkid=844975) lub klikając pozycję **Obsługa** w prawym górnym rogu [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  Wybierz konkretny typ oferty na liście **typ problemu** i wybierz pozycję **Usuń opublikowaną ofertę** z listy **Kategoria** .

3.  Prześlij żądanie.

Zespół pomocy technicznej przeprowadzi Cię przez proces usuwania oferty.

> [!NOTE]
> Usunięcie oferty (lub jednostki SKU) nie będzie miało wpływu na bieżące zakupy tej oferty (lub jednostki SKU). Te zakupy będą nadal działały tak jak wcześniej. Usunięte oferty lub jednostki SKU nie będą jednak dostępne dla żadnych przyszłych zakupów.


## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z podstawowymi operacjami, które są używane do zarządzania ofertami, możesz utworzyć wystąpienie [oferty portalu Microsoft Marketplace](../cpp-marketplace-offers.md).
