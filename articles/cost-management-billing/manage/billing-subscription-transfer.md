---
title: Przeniesienie własności rozliczeń subskrypcji platformy Azure
description: Opis sposobu przeniesienia własności rozliczeń subskrypcji platformy Azure na inne konto.
keywords: przeniesienie subskrypcji platformy Azure, przeniesienie subskrypcji platformy Azure na inne konto, zmiana właściciela subskrypcji platformy Azure, transfer subskrypcji platformy Azure na inne konto, transfer rozliczeń na platformie Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: c3142fd41830487453a3cc980a87cdca72cf7213
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094140"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto

W tym artykule przedstawiono kroki wymagane do przeniesienia własności rozliczeń subskrypcji platformy Azure na inne konto. Przed przeniesieniem własności rozliczeń dla subskrypcji przeczytaj [Informacje na temat przenoszenia własności rozliczeń dla subskrypcji platformy Azure](../understand/subscription-transfer.md).

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [Przełączanie subskrypcji platformy Azure na inną ofertę](switch-azure-offer.md). Aby kontrolować, kto może uzyskać dostęp do zasobów w ramach subskrypcji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli jesteś klientem z Umową Enterprise (EA), administrator w Twoim przedsiębiorstwie może przenosić własność rozliczeń subskrypcji między kontami. Aby uzyskać więcej informacji, zobacz [Zmienianie własności subskrypcji platformy Azure lub konta](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Tylko administrator rozliczeń konta może przenieść własność subskrypcji.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta rozliczeniowego z subskrypcją, którą chcesz przenieść. Jeśli nie masz pewności, czy jesteś administratorem, lub jeśli chcesz określić, kto nim jest, zobacz [Określanie administratora rozliczeń konta](../understand/subscription-transfer.md#whoisaa).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Wybierz pozycję **Subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeniowego, a następnie **subskrypcji** lub **subskrypcji platformy Azure**.
1. Wybierz pozycję **Przenieś własność rozliczeń subskrypcji** dla subskrypcji, którą chcesz przenieść.  
   ![Wybierz subskrypcję do przeniesienia](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Wprowadź adres e-mail użytkownika, który jest administratorem rozliczeń konta i będzie nowym właścicielem subskrypcji.
1. Jeśli przenosisz subskrypcję do konta w innej dzierżawie usługi Azure AD, zaznacz, że chcesz przenieść subskrypcję do dzierżawy nowego konta. Więcej informacji — zobacz [Przenoszenie subskrypcji do konta w innej dzierżawie usługi Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Jeśli przeniesiesz subskrypcję do dzierżawy usługi Azure AD nowego konta, wszystkie [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) umożliwiające uzyskiwanie dostępu do zasobów w ramach subskrypcji zostaną trwale usunięte. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami w ramach subskrypcji. Alternatywnie możesz usunąć zaznaczenie opcji **Dzierżawa usługi Azure AD subskrypcji** w celu przeniesienia własności rozliczeń bez przeniesienia subskrypcji do dzierżawy nowego konta. Jeśli to zrobisz, istniejące przypisania ról platformy Azure umożliwiające dostęp do zasobów platformy Azure zostaną zachowane.  
    ![Strona wysyłania przeniesienia](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Wybierz opcję **Wyślij żądanie przeniesienia**.
1. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.  
   ![Wiadomość e-mail dotycząca przeniesienia subskrypcji wysłana do adresata](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Aby zatwierdzić żądanie przeniesienia, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami. Użytkownik następnie wybiera formę płatności, która będzie używana do płacenia za subskrypcję. Jeśli użytkownik nie ma konta platformy Azure, musi utworzyć nowe konto.  
   ![Pierwsza strona internetowa przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Druga strona internetowa przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Trzecia strona internetowa przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. To wszystko! Subskrypcja została przeniesiona.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Przenoszenie subskrypcji do innego konta w dzierżawie usługi Azure AD

Dzierżawa usługi Azure Active Directory (AD) jest tworzona podczas tworzenia konta na platformie Azure. Dzierżawa reprezentuje Twoje konto. Dzierżawa służy do zarządzania dostępem do Twoich subskrypcji i zasobów.

Nowo utworzona subskrypcja jest hostowana w dzierżawie usługi Azure AD Twojego konta. Jeśli chcesz zapewnić innym użytkownikom dostęp do swojej subskrypcji lub jej zasobów, musisz zaprosić ich do dołączenia do dzierżawy. Ułatwia to kontrolowanie dostępu do subskrypcji i zasobów.

Jeśli przenosisz własność rozliczeń subskrypcji do konta w innej dzierżawie usługi Azure AD, możesz przenieść subskrypcję do dzierżawy nowego konta. W takim przypadku wszyscy użytkownicy, grupy lub jednostki usługi mający [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) do zarządzania subskrypcjami i ich zasobami utracą ten dostęp. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami. Nowy właściciel musi ręcznie dodać tych użytkowników do subskrypcji, aby zapewnić dostęp użytkownikom, którzy go utracili. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Przenoszenie subskrypcji programu Visual Studio i Partner Network

Subskrypcje programu Visual Studio i Microsoft Partner Network zawierają comiesięczne, powiązane z nimi środki na korzystanie z platformy Azure. Po przeniesieniu tych subskrypcji środki nie są dostępne na docelowym koncie rozliczeniowym. Subskrypcja używa kredytu na docelowym koncie rozliczeniowym. Jeśli na przykład Robert przeniesie subskrypcję Visual Studio Enterprise na konto Janiny 9 września, a Janina zaakceptuje przeniesienie, po zakończeniu przeniesienia subskrypcja zacznie korzystać z kredytu na koncie Janiny. Środki będą resetowane każdego dziewiątego dnia miesiąca.

## <a name="next-steps-after-accepting-billing-ownership"></a>Kroki po zaakceptowaniu własności rozliczeń

Jeśli zaakceptujesz własność rozliczeń subskrypcji platformy Azure, zalecamy zapoznanie się z następującymi krokami:

1. Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) oraz [Przypisywanie ról platformy azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).
1. Zaktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Klucze dostępu dla usług, takich jak Storage. Więcej informacji — zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, rozważ zaktualizowanie identyfikatora partnera w ramach subskrypcji. Identyfikator partnera można zaktualizować w witrynie [Azure Portal](https://portal.azure.com). Więcej informacji — zobacz [Łączenie identyfikatora partnera z kontami platformy Azure](link-partner-id.md).

## <a name="cancel-a-transfer-request"></a>Anulowanie żądania przeniesienia

Tylko jedno żądanie przeniesienia jest aktywne w danym momencie. Żądanie przeniesienia jest ważne przez 15 dni. Po upływie 15 dni żądanie przeniesienia wygasa.

Aby anulować żądanie przeniesienia:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **subskrypcji** > wybierz subskrypcję, dla której wysłano żądanie transferu, a następnie wybierz pozycję **Przenieś własność rozliczeń**.
1. W dolnej części strony wybierz pozycję **Anuluj żądanie przeniesienia**.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="Przykład z oknem przeniesienia własności rozliczeń z opcją anulowania żądania przeniesienia" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy związane z transferem subskrypcji, skorzystaj z poniższych informacji dotyczących rozwiązywania problemów.

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Oryginalny właściciel rozliczeń subskrypcji platformy Azure opuści organizację

> [!Note]
> Ta sekcja dotyczy tylko konta rozliczeniowego umowy klienta firmy Microsoft. Sprawdź, czy masz dostęp do [umowy klienta firmy Microsoft](mca-request-billing-ownership.md#check-for-access).

Istnieje możliwość, że oryginalny właściciel konta rozliczeń, który utworzył konto platformy Azure i subskrypcję platformy Azure, opuści Twoją organizację. Jeśli taka sytuacja się zdarzy, jego tożsamość użytkownika nie jest już w Azure Active Directory organizacji. Następnie subskrypcja platformy Azure nie ma właściciela rozliczenia. Ta sytuacja uniemożliwia wszystkim użytkownikom wykonywanie operacji rozliczeniowych na koncie, w tym wyświetlanie i płacenie rachunków. Subskrypcja może przejść w stan przeszłości. Ostatecznie subskrypcja może zostać wyłączona z powodu braku płatności. Ostatecznie subskrypcja może zostać usunięta i będzie mieć wpływ na każdą usługę uruchomioną w ramach subskrypcji.

Jeśli subskrypcja nie ma już prawidłowego właściciela konta rozliczeniowego, na platformie Azure jest wysyłana wiadomość e-mail do innych właścicieli konta rozliczeniowego, administratorów usługi (jeśli istnieje), Co-Administrators (jeśli istnieją) i właściciele subskrypcji, którzy informują o sytuacji i udostępniają im link do akceptowania własności rozliczeń subskrypcji. Jeden z użytkowników może wybrać łącze, aby zaakceptować własność rozliczeń. Aby uzyskać więcej informacji na temat ról rozliczeń, zobacz [role rozliczeń](understand-mca-roles.md) i role [klasyczne oraz role RBAC platformy Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Oto przykład wiadomości e-mail.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="Zrzut ekranu przedstawiający przykładową wiadomość e-mail w celu zaakceptowania własności rozliczeń." lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

Ponadto platforma Azure wyświetla transparent w oknie Szczegóły subskrypcji w Azure Portal do właścicieli rozliczeń, administratorów usług, współadministratorów i właścicieli subskrypcji. Wybierz link na banerze, aby zaakceptować własność rozliczeń.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="Zrzut ekranu przedstawiający przykład subskrypcji bez prawidłowego właściciela rozliczania." lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>Opcja „Przenieś subskrypcję” jest niedostępna

<a name="no-button"></a> 

Samoobsługowe przeniesienie subskrypcji nie jest dostępne w przypadku Twojego konta rozliczeniowego. Obecnie nie obsługujemy przenoszenia własności rozliczeń subskrypcji na kontach Umowy Enterprise (EA) w witrynie Portalu Azure. Również konta Umowy klienta firmy Microsoft utworzone podczas pracy z przedstawicielem Microsoft nie obsługują przenoszenia własności rozliczeń.

###  <a name="not-all-subscription-types-can-transfer"></a>Nie wszystkie typy subskrypcji mogą być przenoszone

Nie wszystkie typy subskrypcji obsługują przenoszenie własności rozliczeń. Aby zobaczyć listę typów subskrypcji obsługujących przenoszenie, zobacz [Obsługiwane typy subskrypcji](../understand/subscription-transfer.md#supported-subscription-types).

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Błąd odmowy dostępu wyświetlany podczas próby przeniesienia własności rozliczeń subskrypcji

Ten komunikat jest wyświetlany, jeśli użytkownik próbuje przenieść subskrypcję planu platformy Microsoft Azure bez wymaganych uprawnień. Aby przenieść subskrypcję planu platformy Microsoft Azure, musisz być właścicielem lub współautorem w sekcji faktury dotyczącej subskrypcji. Więcej informacji — zobacz [Sekcja Zarządzanie subskrypcjami dla celów rozliczeniowych](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) oraz [Przypisywanie ról platformy azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).