---
title: Przeniesienie własności rozliczeń subskrypcji platformy Azure
description: Opisuje sposób przeniesienia własności rozliczeń subskrypcji platformy Azure na inne konto. Zawiera odpowiedzi na często zadawane pytania dotyczące tego procesu
keywords: przeniesienie subskrypcji platformy Azure, przeniesienie subskrypcji platformy Azure na inne konto, zmiana właściciela subskrypcji platformy Azure, transfer subskrypcji platformy Azure na inne konto, transfer rozliczeń na platformie Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27ae816e3930ee0e5948c2daae40a0961a6cf474
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685038"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto

Możesz chcieć przenieść własność rozliczeń subskrypcji platformy Azure, jeśli opuszczasz organizację lub chcesz, aby Twoja subskrypcja była rozliczana w ramach innego konta. Przeniesienie własności rozliczeń do innego konta zapewnia administratorom nowego konta uprawnienia do zadań związanych z rozliczeniami. Mogą zmieniać formę płatności, wyświetlać opłaty i anulować subskrypcję.

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [Przełączanie subskrypcji platformy Azure na inną ofertę](switch-azure-offer.md). Aby kontrolować, kto może uzyskać dostęp do zasobów w ramach subskrypcji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli jesteś klientem z Umową Enterprise (EA), administratorzy w Twoim przedsiębiorstwie mogą przenosić własność rozliczeń subskrypcji między kontami. Więcej informacji — zobacz [Przenoszenie własności rozliczeń subskrypcji w ramach Umowy Enterprise (EA)](#EA).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta rozliczeniowego z subskrypcją, którą chcesz przenieść. Aby dowiedzieć się, czy jesteś administratorem, przeczytaj odpowiedzi na [często zadawane pytania](#faq).

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Wybierz pozycję **Subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeniowego, a następnie **subskrypcji** lub **subskrypcji platformy Azure**.

1. Wybierz pozycję **Przenieś własność rozliczeń subskrypcji** dla subskrypcji, którą chcesz przenieść.

   ![Wybierz subskrypcję do przeniesienia](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Wprowadź adres e-mail użytkownika, który jest administratorem rozliczeń konta i będzie nowym właścicielem subskrypcji.

1. Jeśli przenosisz subskrypcję do konta w innej dzierżawie usługi Azure AD, zaznacz, że chcesz przenieść subskrypcję do dzierżawy nowego konta. Więcej informacji — zobacz [Przenoszenie subskrypcji do konta w innej dzierżawie usługi Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).

    > [!IMPORTANT]
    >
    > Jeśli przeniesiesz subskrypcję do dzierżawy usługi Azure AD nowego konta, wszystkie [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) umożliwiające uzyskiwanie dostępu do zasobów w ramach subskrypcji zostaną trwale usunięte. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz następną sekcję [Przenoszenie subskrypcji do innego konta w dzierżawie usługi Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account)). Alternatywnie możesz usunąć zaznaczenie pola **Dzierżawa usługi Azure AD subskrypcji** w celu przeniesienia własności rozliczeń bez przeniesienia subskrypcji do dzierżawy nowego konta. Jeśli to zrobisz, istniejące przypisania ról platformy Azure umożliwiające dostęp do zasobów platformy Azure zostaną zachowane.

    ![Strona wysyłania przeniesienia](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Wybierz opcję **Wyślij żądanie przeniesienia**.

1. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

   ![Wiadomość e-mail dotycząca przeniesienia subskrypcji wysłana do adresata](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Aby zatwierdzić żądanie przeniesienia, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami. Użytkownik następnie wybiera formę płatności, która będzie używana do płacenia za subskrypcję. Jeśli użytkownik nie ma konta platformy Azure, musi utworzyć nowe konto.

   ![Pierwsza strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Druga strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Druga strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. To wszystko! Subskrypcja została przeniesiona.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Przenoszenie subskrypcji do innego konta w dzierżawie usługi Azure AD

Dzierżawa usługi Azure Active Directory (AD) jest tworzona podczas tworzenia konta na platformie Azure. Dzierżawa reprezentuje Twoje konto. Dzierżawa służy do zarządzania dostępem do Twoich subskrypcji i zasobów.

Nowo utworzona subskrypcja jest hostowana w dzierżawie usługi Azure AD Twojego konta. Jeśli chcesz zapewnić innym użytkownikom dostęp do swojej subskrypcji lub jej zasobów, musisz zaprosić ich do dołączenia do dzierżawy. Ułatwia to kontrolowanie dostępu do subskrypcji i zasobów.

Jeśli przenosisz własność rozliczeń subskrypcji do konta w innej dzierżawie usługi Azure AD, możesz przenieść subskrypcję do dzierżawy nowego konta. W takim przypadku wszyscy użytkownicy, grupy lub jednostki usługi mający [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) do zarządzania subskrypcjami i ich zasobami utracą ten dostęp. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami. Nowy właściciel musi ręcznie dodać tych użytkowników do subskrypcji, aby zapewnić dostęp użytkownikom, którzy go utracili. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD (wersja zapoznawcza)](../../role-based-access-control/transfer-subscription.md).


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Przenoszenie subskrypcji programu Visual Studio i Partner Network

Subskrypcje programu Visual Studio i Microsoft Partner Network zawierają comiesięczne, powiązane z nimi środki na korzystanie z platformy Azure. Po przeniesieniu tych subskrypcji środki nie są dostępne na docelowym koncie rozliczeniowym. Subskrypcja używa kredytu na docelowym koncie rozliczeniowym. Jeśli na przykład Robert przeniesie subskrypcję Visual Studio Enterprise na konto Janiny 9 września, a Janina zaakceptuje przeniesienie, po zakończeniu przeniesienia subskrypcja zacznie korzystać z kredytu na koncie Janiny. Środki będą resetowane każdego dziewiątego dnia miesiąca.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>Przenoszenie własności rozliczeń subskrypcji EA

Administrator przedsiębiorstwa może przenieść własność subskrypcji między kontami w ramach rejestracji. Aby uzyskać więcej informacji, zobacz sekcję [Zmienianie właściciela konta](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner) w witrynie EA Portal.

## <a name="next-steps-after-accepting-billing-ownership"></a>Kroki po zaakceptowaniu własności rozliczeń

Jeśli zaakceptujesz własność rozliczeń subskrypcji platformy Azure, zalecamy zapoznanie się z następującymi krokami:

1. Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
1. Zaktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Klucze dostępu dla usług, takich jak Storage. Więcej informacji — zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, rozważ zaktualizowanie identyfikatora partnera w ramach tej subskrypcji. Identyfikator partnera można zaktualizować w witrynie [Azure Portal](https://portal.azure.com). Więcej informacji — zobacz [Łączenie identyfikatora partnera z kontami platformy Azure](link-partner-id.md).

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Przenoszenie subskrypcji w witrynie Azure Portal jest dostępne dla typów subskrypcji wymienionych poniżej. Obecnie przenoszenie nie jest obsługiwane w przypadku [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p/) ani [subskrypcji platformy Azure w ramach programu licencjonowania Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Więcej informacji — zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Aby przenieść inne subskrypcje, takie jak plany pomocy technicznej, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Za pośrednictwem portalu EA](#EA).

\*\* Obsługiwane tylko w przypadku kont utworzonych podczas tworzenia konta w witrynie sieci Web platformy Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Często zadawane pytania dotyczące nadawców

Te pytania dotyczą użytkowników przenoszących własność rozliczeń subskrypcji platformy Azure na inne konto.

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> Kto jest administratorem rozliczeń konta?

Administrator rozliczeń to osoba, która ma uprawnienia do zarządzania rozliczeniami konta. Administratorzy mogą uzyskiwać dostęp do rozliczeń w witrynie [Azure Portal](https://portal.azure.com) i wykonywać różne zadania rozliczeniowe, takie jak tworzenie subskrypcji, wyświetlanie i płacenie faktur lub aktualizowanie form płatności.

Aby zidentyfikować konta, dla których jesteś administratorem rozliczeń, wykonaj następujące czynności:

1. Przejdź do strony [Zarządzanie kosztami i rozliczenia w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Zaznacz **Wszystkie zakresy rozliczeniowe** w okienku po lewej stronie.
1. Na stronie Subskrypcje są wyświetlane wszystkie subskrypcje, w których jesteś administratorem rozliczeń.

Jeśli nie wiesz, kto jest administratorem konta dla subskrypcji, wykonaj poniższe kroki, aby to sprawdzić.

1. Odwiedź [stronę Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie przejrzyj **Ustawienia**.
1. Wybierz pozycję **Właściwości**. Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Czy wszystko jest przenoszone? Również grupy zasobów, maszyny wirtualne, dyski i inne uruchomione usługi?

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryny internetowe, są przenoszone na nowe konto. Jeśli jednak przeniesiesz subskrypcję do konta w innej dzierżawie usługi Azure AD, żadne [role administratora](add-change-subscription-administrator.md) ani [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji [nie zostaną przeniesione](#transfer-a-subscription-to-another-azure-ad-tenant-account). Ponadto [rejestracje aplikacji](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) ani inne usługi specyficzne dla dzierżawy nie są przenoszone razem z subskrypcją.

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>Czy mogę przenieść własność na konto w innym kraju/regionie?
Niestety, w witrynie Azure Portal nie można wykonywać przeniesień między krajami/regionami. Aby przenieść swoją subskrypcję między krajami/regionami [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jestem administratorem na dwóch kontach. Czy mogę przenieść subskrypcję z jednego ze swoich kont na inne?
Tak, możesz przenieść subskrypcję między kontami. Twoje konta są koncepcyjnie uznawane za konta dwóch różnych użytkowników, możesz zatem wykonać powyższe kroki w celu przeniesienia subskrypcji między kontami.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Czy przeniesienie subskrypcji skutkuje przestojem usługi?

Przeniesienie subskrypcji do konta w tej samej dzierżawie usługi Azure Active Directory nie ma wpływu na zasoby działające w ramach subskrypcji. Jednak informacje kontekstowe zapisane w programie PowerShell nie są aktualizowane, więc może być konieczne ich wyczyszczenie lub zmiana ustawień. Jeśli subskrypcja zostanie przeniesiona do konta w innej dzierżawie, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [przypisaniami ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) umożliwiającymi uzyskanie dostępu do zasobów w ramach subskrypcji utracą dostęp. Może to spowodować przestój usługi.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>Czy użytkownicy w nowym koncie mają dostęp do historii użycia i rozliczeń?

Jedyne informacje dostępne dla użytkowników na nowym koncie to koszt subskrypcji z ostatniego miesiąca. Pozostała część historii użycia i rozliczeń nie jest przenoszona z subskrypcją

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak mogę przeprowadzić migrację danych i usług mojej subskrypcji platformy Azure do nowej subskrypcji?

Jeśli nie możesz przenieść własności subskrypcji, możesz ręcznie migrować zasoby. Zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Czy jeśli przeniosę subskrypcję programu Visual Studio lub Microsoft Partner Network, moje środki są przenoszone wraz z subskrypcją na nowe konto?

Nie, Twoje środki nie są dostępne na nowym koncie. Użytkownik akceptujący żądanie przeniesienia musi mieć licencję programu Visual Studio, aby zaakceptować żądanie przeniesienia. Subskrypcja korzysta z kredytu programu Visual Studio, który jest dostępny na koncie użytkownika. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji programu Visual Studio i Partner Network](#transfer-visual-studio-and-partner-network-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Często zadawane pytania dotyczące adresatów

Te pytania dotyczą użytkowników przejmujących własność rozliczeń subskrypcji platformy Azure z innego konta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Jeśli przejmuję własność rozliczeń subskrypcji z innego konta, czy użytkownicy tego konta nadal mają dostęp do moich zasobów?

Tak. Jednak [role administratorów](add-change-subscription-administrator.md) i [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) mogą zostać usunięte. Utrata dostępu następuje, gdy Twoje konto znajduje się w dzierżawie usługi Azure AD innej niż dzierżawa subskrypcji, a użytkownik, który wysłał żądanie przeniesienia, przenosi subskrypcję do dzierżawy Twojego konta. Aby wyświetlić użytkowników, którzy mają przypisania ról platformy Azure umożliwiające uzyskanie dostępu do zasobów w ramach subskrypcji, wykonaj następujące czynności:

1. Odwiedź [stronę subskrypcji w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz wyświetlić, a następnie w lewym okienku wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** .
1. Wybierz pozycję **Przypisania ról** w górnej części strony. Na stronie przypisań ról są wyświetleni wszyscy użytkownicy z dostępem do subskrypcji.

Nawet jeśli [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) zostaną usunięte podczas przenoszenia, użytkownicy pierwotnego konta właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem innych mechanizmów zabezpieczeń, takich jak:

* Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

  1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  2. W menu Centrum wybierz pozycję **Wszystkie zasoby**.
  3. Wybierz zasób.
  4. Na stronie zasobu wybierz opcję **Ustawienia**. Możesz tu wyświetlać i aktualizować istniejące wpisy tajne.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Jeśli przejmę własność rozliczeń subskrypcji w trakcie cyklu rozliczeniowego, czy muszę płacić za cały cykl?

Twoje konto jest odpowiedzialne za opłacenie użycia zgłoszonego od momentu przeniesienia. Pewna część użycia, która miała miejsce przed przeniesieniem, mogła zostać zgłoszona dopiero po nim. Jest ona ujęta na rachunku konta.

### <a name="can-i-use-a-different-payment-method"></a>Czy mogę użyć innej formy płatności?

Tak. Akceptując żądanie przeniesienia, możesz wybrać istniejącą formę płatności połączoną z Twoim kontem lub wybrać nową.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Jak mogę przenieść własność mojego konta subskrypcji umowy Enterprise Agreement (EA), jeśli pierwotny właściciel konta nie należy już do organizacji?

Administrator przedsiębiorstwa może zaktualizować własność dowolnego konta nawet wtedy, gdy pierwotny właściciel nie należy już do organizacji. Może to zrobić, postępując zgodnie z instrukcjami dotyczącymi [przenoszenia własności konta dla wszystkich subskrypcji](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w portalu EA.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a> Czemu nie widzę przycisku „Przenieś subskrypcję”?

Samoobsługowe przeniesienie subskrypcji nie jest dostępne w przypadku Twojego konta rozliczeniowego. Obecnie nie obsługujemy przenoszenia własności rozliczeń subskrypcji na kontach Umowy Enterprise (EA) w witrynie Portalu Azure. Również konta Umowy klienta firmy Microsoft utworzone podczas pracy z przedstawicielem Microsoft nie obsługują przenoszenia własności rozliczeń.

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> Dlaczego mój typ subskrypcji nie obsługuje przenoszenia?

Nie wszystkie typy subskrypcji obsługują przenoszenie własności rozliczeń. Aby zobaczyć listę typów subskrypcji obsługujących przenoszenie, zobacz [Obsługiwane typy subskrypcji](#supported-subscription-types).

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> Dlaczego podczas próby przeniesienia własności rozliczeń subskrypcji widzę komunikat o błędzie odmowy dostępu?

Ten komunikat jest wyświetlany, jeśli użytkownik próbuje przenieść subskrypcję planu platformy Microsoft Azure bez wymaganych uprawnień. Aby przenieść subskrypcję planu platformy Microsoft Azure, musisz być właścicielem lub współautorem w sekcji faktury dotyczącej subskrypcji. Więcej informacji — zobacz [Sekcja Zarządzanie subskrypcjami dla celów rozliczeniowych](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
