---
title: Anulowanie subskrypcji platformy Azure
description: Opis sposobu anulowania subskrypcji platformy Azure, na przykład subskrypcji bezpłatnej wersji próbnej
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: b20134cbfb85c2dcaef1227e6390c8c6047ad5be
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109394"
---
# <a name="cancel-your-azure-subscription"></a>Anulowanie subskrypcji platformy Azure

Jeśli subskrypcja platformy Azure nie jest już potrzebna, możesz ją anulować w witrynie Azure Portal.

Chociaż nie jest to wymagane, firma Microsoft *zaleca* wykonanie następujących czynności przed anulowaniem subskrypcji:

* Utwórz kopię zapasową danych. Na przykład w przypadku przechowywania danych w usłudze Azure Storage lub SQL pobierz ich kopię. Jeśli masz maszynę wirtualną, zapisz jej obraz lokalnie.
* Zamknij usługi, z których korzystasz. Przejdź do [strony zasobów w portalu zarządzania](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) i **zatrzymaj** wszystkie uruchomione maszyny wirtualne, aplikacje lub inne usługi.
* Rozważ wykonanie migracji danych. Zobacz artykuł [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Usuń wszystkie zasoby i wszystkie grupy zasobów.
* Jeśli masz jakieś role niestandardowe odwołujące się do tej subskrypcji w parametrze `AssignableScopes`, należy zaktualizować te role niestandardowe, aby usunąć subskrypcję. Jeśli spróbujesz zaktualizować rolę niestandardową po anulowaniu subskrypcji, może wystąpić błąd. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rolami niestandardowymi](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) i [Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md).

Jeśli anulujesz płatny plan pomocy technicznej platformy Azure, opłaty za resztę okresu subskrypcji zostaną naliczone. Aby uzyskać więcej informacji, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Kto może anulować subskrypcję?

W poniższej tabeli opisano uprawnienia wymagane do anulowania subskrypcji.

|Typ subskrypcji     |Kto może anulować  |
|---------|---------|
|Subskrypcje utworzone podczas tworzenia konta na platformie Azure za pomocą witryny internetowej Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrator konta i właściciele subskrypcji  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) i [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Właściciel konta i właściciele subskrypcji       |
|[Plan platformy Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) i [Plan platformy Azure na potrzeby tworzenia i testowania](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Właściciele subskrypcji      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Anulowanie subskrypcji w witrynie Azure Portal

1. Wybierz subskrypcję na [stronie Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz anulować.
1. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Anuluj subskrypcję**.
    ![Zrzut ekranu z przyciskiem Anuluj](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Postępuj zgodnie z instrukcjami i zakończ anulowanie.

> [!NOTE]
> Na prośbę klienta lub w przypadkach braku płatności bądź oszustwa partnerzy mogą wstrzymywać lub anulować subskrypcję. Aby uzyskać więcej informacji, zobacz [Wstrzymywanie lub anulowanie subskrypcji](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Anulowanie planu pomocy technicznej

Jeśli plan pomocy technicznej został zakupiony za pośrednictwem witryny internetowej platformy Azure, witryny Azure Portal lub w ramach umowy klienta firmy Microsoft, możesz go anulować. Jeśli plan pomocy technicznej został zakupiony za pośrednictwem przedstawiciela lub partnera firmy Microsoft, skontaktuj się z nimi w celu uzyskania pomocy. 

1. W witrynie Azure Portal przejdź do obszaru **Zarządzanie kosztami i rozliczenia**.
1. W obszarze **Rozliczenia** wybierz pozycję **Opłaty cykliczne**.
1. Po prawej stronie elementu wiersza planu pomocy wybierz wielokropek ( **...** ), a następnie wybierz pozycję **Wyłącz automatyczne odnawianie**.

## <a name="what-happens-after-subscription-cancellation"></a>Co się dzieje po anulowaniu subskrypcji?

Po anulowaniu rozliczanie zostanie natychmiast zatrzymane. Jednak wyświetlenie informacji o anulowaniu w portalu może potrwać do 10 minut. Jeśli wykonasz anulowanie w trakcie okresu rozliczeniowego, wyślemy końcową fakturę z typową datą faktury po zakończeniu okresu.

Po anulowaniu usługi zostaną wyłączone. Oznacza to, że przydziały dla maszyn wirtualnych zostaną cofnięte, tymczasowe adresy IP zostaną zwolnione, a magazyn będzie dostępny tylko do odczytu.

Po anulowaniu subskrypcji firma Microsoft czeka 30–90 dni, zanim trwale usunie dane — stosujemy tę zwłokę na wypadek potrzeby uzyskania dostępu do danych lub zmiany decyzji użytkownika. Nie naliczamy opłat za przechowywanie danych. Aby dowiedzieć się więcej, zobacz [Centrum zaufania firmy Microsoft — jak zarządzamy Twoimi danymi](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Usuwanie subskrypcji bezpłatnej wersji próbnej

Jeśli masz subskrypcję bezpłatnej wersji próbnej, nie musisz czekać 30 dni, aż subskrypcja zostanie automatycznie usunięta. Możesz usunąć swoją subskrypcję *trzy dni* po jej anulowaniu. Opcja **Usuń subskrypcję** jest dostępna dopiero po upływie trzech dni od anulowania subskrypcji.

1. Odczekaj trzy dni od daty anulowania subskrypcji.
1. Wybierz subskrypcję na stronie [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal.
1. Wybierz subskrypcję, którą chcesz usunąć.
1. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Usuń subskrypcję**.

## <a name="delete-other-subscriptions"></a>Usuń inne subskrypcje

Jedynym typem subskrypcji, który można usunąć ręcznie, jest bezpłatna subskrypcja wersji próbnej. Wszystkie inne typy subskrypcji, w tym subskrypcje z płatność zgodnie z rzeczywistym użyciem, są usuwane tylko przez proces [anulowania subskrypcji](#cancel-subscription-in-the-azure-portal) . Innymi słowy, nie można usunąć subskrypcji bezpośrednio, chyba że jest to bezpłatna subskrypcja wersji próbnej. Jeśli jednak anulujesz subskrypcję, możesz utworzyć [żądanie pomocy technicznej platformy Azure](https://go.microsoft.com/fwlink/?linkid=2083458) , aby zażądać natychmiastowego usunięcia subskrypcji.

## <a name="reactivate-a-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli anulujesz subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem, możesz [ją ponownie aktywować w Azure Portal](subscription-disabled.md).

Jeśli subskrypcja nie jest subskrypcją z płatnością zgodnie z rzeczywistym użyciem, skontaktuj się z pomocą techniczną w ciągu 90 dni od anulowania, aby ponownie aktywować subskrypcję.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Dlaczego nie widzę opcji Anuluj subskrypcję w witrynie Azure Portal? 

Możesz nie mieć uprawnień wymaganych do anulowania subskrypcji. Zobacz [Kto może anulować subskrypcję?](#who-can-cancel-a-subscription), aby dowiedzieć się, kto może anulować różne typy subskrypcji.

## <a name="how-do-i-delete-my-azure-account"></a>Jak mogę usunąć moje konto platformy Azure?

*Chcę usunąć moje konto, w tym wszystkie moje dane osobowe. Anulowano już aktywną subskrypcję (bezpłatny okres próbny). Nie mam aktywnych subskrypcji i chcę całkowicie usunąć moje konto*.

* Jeśli korzystasz z konta Azure Active Directory za pośrednictwem organizacji, administrator usługi Azure AD może usunąć konto. Następnie usługi zostaną wyłączone. Oznacza to, że przydziały dla maszyn wirtualnych zostaną cofnięte, tymczasowe adresy IP zostaną zwolnione, a magazyn będzie dostępny tylko do odczytu. Podsumowując, po anulowaniu rozliczanie zostanie natychmiast zatrzymane.

* Jeśli nie korzystasz z konta Azure Active Directory za pośrednictwem organizacji, możesz anulować i usunąć subskrypcje platformy Azure, a następnie usunąć kartę kredytową z konta. Chociaż ta akcja nie powoduje usunięcia konta, po jej wykonaniu nie będzie można z niego korzystać. Możesz wykonać dodatkowy krok i usunąć także skojarzone konto Microsoft, jeśli nie jest ono używane w żadnym innym celu.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Jak mogę anulować konto programu Visual Studio Professional?

Zapoznaj się z artykułem [Odnawianie i anulowanie](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Jeśli masz jakiekolwiek subskrypcje programu Visual Studio na platformie Azure, je również należy anulować i usunąć.

## <a name="next-steps"></a>Następne kroki

- W razie konieczności można ponownie aktywować subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem w [Azure Portal](subscription-disabled.md).