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
ms.date: 10/05/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 1ce461f50a6775bf7884fedc4cfa4dc7cb41bb8c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408147"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto

W tym artykule przedstawiono kroki wymagane do przeniesienia własności rozliczeń subskrypcji platformy Azure na inne konto. Przed przeniesieniem własności rozliczeń dla subskrypcji przeczytaj [Informacje na temat przenoszenia własności rozliczeń dla subskrypcji platformy Azure](../understand/subscription-transfer.md).

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [Przełączanie subskrypcji platformy Azure na inną ofertę](switch-azure-offer.md). Aby kontrolować, kto może uzyskać dostęp do zasobów w ramach subskrypcji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli jesteś klientem z Umową Enterprise (EA), administrator w Twoim przedsiębiorstwie może przenosić własność rozliczeń subskrypcji między kontami. Aby uzyskać więcej informacji, zobacz [Zmienianie właściciela konta](ea-portal-administration.md#change-account-owner).

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

1. Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
1. Zaktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Klucze dostępu dla usług, takich jak Storage. Więcej informacji — zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, rozważ zaktualizowanie identyfikatora partnera w ramach subskrypcji. Identyfikator partnera można zaktualizować w witrynie [Azure Portal](https://portal.azure.com). Więcej informacji — zobacz [Łączenie identyfikatora partnera z kontami platformy Azure](link-partner-id.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy związane z transferem subskrypcji, skorzystaj z poniższych informacji dotyczących rozwiązywania problemów.

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

- Przejrzyj i zaktualizuj role administratora usługi, współadministratorów i przypisania ról platformy Azure. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).