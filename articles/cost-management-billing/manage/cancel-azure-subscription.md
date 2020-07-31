---
title: Anulowanie subskrypcji platformy Azure
description: Opis sposobu anulowania subskrypcji platformy Azure, na przykład subskrypcji bezpłatnej wersji próbnej
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: banders
ms.openlocfilehash: 8b0ba7b597921d82883f2c7ba505ebb10eee1876
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039754"
---
# <a name="cancel-your-azure-subscription"></a>Anulowanie subskrypcji platformy Azure

Jeśli subskrypcja platformy Azure nie jest już potrzebna, możesz ją anulować w witrynie Azure Portal.

Chociaż nie jest to wymagane, firma Microsoft *zaleca* wykonanie następujących czynności przed anulowaniem subskrypcji:

* Utwórz kopię zapasową danych. Na przykład w przypadku przechowywania danych w usłudze Azure Storage lub SQL pobierz ich kopię. Jeśli masz maszynę wirtualną, zapisz jej obraz lokalnie.
* Zamknij usługi, z których korzystasz. Przejdź do [strony zasobów w portalu zarządzania](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) i **zatrzymaj** wszystkie uruchomione maszyny wirtualne, aplikacje lub inne usługi.
* Rozważ wykonanie migracji danych. Zobacz artykuł [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Usuń wszystkie zasoby i wszystkie grupy zasobów.
* Jeśli masz jakieś role niestandardowe odwołujące się do tej subskrypcji w parametrze `AssignableScopes`, należy zaktualizować te role niestandardowe, aby usunąć subskrypcję. Jeśli spróbujesz zaktualizować rolę niestandardową po anulowaniu subskrypcji, może wystąpić błąd. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rolami niestandardowymi](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) i [Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md).

Jeśli anulujesz płatny plan pomocy technicznej platformy Azure, opłaty będą naliczane za resztę okresu subskrypcji. Aby uzyskać więcej informacji, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Anulowanie subskrypcji w witrynie Azure Portal

1. Wybierz subskrypcję na [stronie Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, którą chcesz anulować.
3. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Anuluj subskrypcję**.
    ![Zrzut ekranu z przyciskiem Anuluj](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Postępuj zgodnie z instrukcjami i zakończ anulowanie.

## <a name="who-can-cancel-a-subscription"></a>Kto może anulować subskrypcję?

W poniższej tabeli opisano uprawnienia wymagane do anulowania subskrypcji.

|Typ subskrypcji     |Kto może anulować  |
|---------|---------|
|Subskrypcje utworzone podczas tworzenia konta na platformie Azure za pomocą witryny internetowej Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrator konta i właściciele subskrypcji  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) i [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Właściciel konta i właściciele subskrypcji       |
|[Plan platformy Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) i [Plan platformy Azure na potrzeby tworzenia i testowania](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Właściciele subskrypcji      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co stanie się, gdy anuluję moją subskrypcję?

Po anulowaniu rozliczanie zostanie natychmiast zatrzymane. Jednak wyświetlenie informacji o anulowaniu w portalu może potrwać do 10 minut. Jeśli wykonasz anulowanie w trakcie okresu rozliczeniowego, wyślemy końcową fakturę z typową datą faktury po zakończeniu okresu. 

Po anulowaniu usługi zostaną wyłączone. Oznacza to, że przydziały dla maszyn wirtualnych zostaną cofnięte, tymczasowe adresy IP zostaną zwolnione, a magazyn będzie dostępny tylko do odczytu.

Firma Microsoft czeka 30–90 dni, zanim trwale usunie dane — stosujemy te zwłokę na wypadek potrzeby uzyskania dostępu do danych lub zmiany decyzji użytkownika. Nie naliczamy opłat za zachowywanie danych. Aby dowiedzieć się więcej, zobacz [Centrum zaufania firmy Microsoft — jak zarządzamy Twoimi danymi](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli przypadkowo anulujesz subskrypcję z opcją płatności zgodnie z rzeczywistym użyciem, możesz ją [ponownie aktywować w Centrum konta](subscription-disabled.md).

Jeśli subskrypcja nie jest subskrypcją z opcją płatności zgodnie z rzeczywistym użyciem, skontaktuj się z pomocą techniczną w ciągu 90 dni od anulowania, aby ponownie aktywować subskrypcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- W razie potrzeby możesz ponownie uaktywnić subskrypcję rozliczaną zgodnie z rzeczywistym użyciem w [Centrum konta](subscription-disabled.md).