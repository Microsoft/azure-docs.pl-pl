---
title: Zarządzanie dostępem do rozliczeń platformy Azure
description: Dowiedz się, jak zapewnić członkom zespołu dostęp do informacji rozliczeniowych platformy Azure.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: e174a0539a75945cc0a2595e28a60d928e10acf4
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166220"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure

Możesz zapewnić innym użytkownikom dostęp do informacji rozliczeniowych dla Twojego konta w witrynie Azure Portal. Typ ról rozliczeniowych oraz instrukcje zapewniające dostęp do informacji rozliczeniowych różnią się w zależności od typu konta rozliczeniowego. Aby określić typ swojego konta rozliczeniowego, zobacz [Sprawdzanie typu konta rozliczeniowego](#check-the-type-of-your-billing-account).

Artykuł dotyczy klientów z kontami usługi online firmy Microsoft. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (EA) i jesteś administratorem przedsiębiorstwa, możesz udzielić uprawnień administratorom działu i właścicielom konta w witrynie Enterprise Portal. Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md). Jeśli jesteś klientem z umową klienta firmy Microsoft, zobacz [Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administratorzy konta dla kont usługi online firmy Microsoft

Administrator konta jest jedynym właścicielem konta rozliczeniowego usługi online firmy Microsoft. Rola jest przypisywana do osoby, która zarejestrowała się na platformie Azure. Administratorzy konta są uprawnieni do wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie faktur lub zmiana rozliczeń dla subskrypcji.

## <a name="give-others-access-to-view-billing-information"></a>Przyznawanie innym osobom dostępu do wyświetlania informacji dotyczących rozliczeń

Administrator konta może udzielać innym osobom dostępu do informacji rozliczeniowych platformy Azure, przypisując jedną z następujących ról w ramach subskrypcji na swoim koncie.

- Administrator usługi
- Współadministrator
- Właściciel
- Współautor
- Czytelnik
- Czytelnik rozliczeń

Role te mają dostęp do informacji dotyczących rozliczeń w witrynie [Azure Portal](https://portal.azure.com/). Osoby, którym przypisano te role, mogą również używać [interfejsów API](usage-rate-card-overview.md) do programistycznego uzyskiwania informacji o fakturach i użyciu.

Aby przypisać role, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

** Jeśli jesteś klientem z umową EA, właściciel konta może przypisać powyższą rolę innym użytkownikom w zespole. Jednak aby użytkownicy mogli wyświetlać informacje dotyczące rozliczeń, administrator przedsiębiorstwa musi włączyć wyświetlanie opłat przez właściciela konta w witrynie Enterprise Portal.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Zezwalanie użytkownikom na pobieranie faktur

Po przypisaniu przez administratora konta odpowiednich ról innym użytkownikom muszą oni włączyć dostęp do pobierania faktur w witrynie Azure Portal. Faktury z datą starszą niż grudzień 2016 r. są dostępne tylko dla administratora konta.

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia** .

    ![Zrzut ekranu, na którym wyróżniono usługę Cost Management + Billing w sekcji Usługi.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Wybierz pozycję **Subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeniowego, a następnie wybranie pozycji **Subskrypcje** .

    ![Zrzut ekranu pokazujący wybieranie subskrypcji](./media/manage-billing-access/billing-select-subscriptions.png)

1. Wybierz pozycję **Faktury** , a następnie pozycję **Dostęp do faktury** .

    ![Zrzut ekranu przedstawia sposób delegowania dostępu do faktur](./media/manage-billing-access/aa-optin01.png)

1. Wybierz pozycję **Włączone** i zapisz.

    ![Zrzut ekranu przedstawia włączanie i wyłączanie delegowania dostępu do faktury](./media/manage-billing-access/aa-optinallow01.png)

Administrator konta można również skonfigurować wysyłanie faktur pocztą e-mail. Aby dowiedzieć się więcej, zobacz [Get your invoice in email](download-azure-invoice-daily-usage-date.md) (Pobieranie faktur za pomocą poczty e-mail).

## <a name="give-read-only-access-to-billing"></a>Przyznawanie dostępu tylko do odczytu do rozliczeń

Przypisz rolę Czytelnik rozliczeń do osoby, która potrzebuje dostępu tylko do odczytu do informacji rozliczeniowych subskrypcji, ale nie musi mieć możliwości zarządzania usługami platformy Azure ani ich tworzenia. Ta rola jest odpowiednia dla użytkowników w organizacji, którzy są odpowiedzialni za zarządzanie finansami i kosztami subskrypcji platformy Azure.

Funkcja czytelnika rozliczeń jest dostępna w wersji zapoznawczej i nie obsługuje jeszcze chmur nieglobalnych.

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia** .

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Wybierz pozycję **Subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeniowego, a następnie wybranie pozycji **Subskrypcje** .

    ![Zrzut ekranu pokazujący wybieranie subskrypcji](./media/manage-billing-access/billing-select-subscriptions.png)

1. Wybierz pozycję **Kontrola dostępu (IAM)** .
1. Wybierz pozycję **Dodaj** w górnej części strony.

    ![Zrzut ekranu pokazujący kliknięcie pozycji Dodaj przypisanie roli](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz pozycję **Czytelnik rozliczeń** .
1. W polu tekstowym **Wybierz** wpisz nazwę lub adres e-mail dla użytkownika, którego chcesz dodać.
1. Wybierz użytkownika.
1. Wybierz pozycję **Zapisz** .
    ![Zrzut ekranu, na którym wyróżniono przycisk Zapisz.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Po kilku chwilach użytkownikowi zostanie przypisana rola czytelnika rozliczeń dla subskrypcji.

** Jeśli jesteś klientem z umową EA, właściciel konta lub administrator działu może przypisać rolę Czytelnik rozliczeń członkom zespołu. Jednak aby czytelnik rozliczeń mógł wyświetlać informacje dotyczące rozliczeń dla działu lub konta, administrator przedsiębiorstwa musi włączyć zasady **Wyświetlanie opłat przez właściciela konta** lub **Wyświetlanie opłat przez właściciela działu** w witrynie Enterprise Portal.

## <a name="check-the-type-of-your-billing-account"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Następne kroki

- Użytkownicy z innymi rolami, takimi jak Właściciel lub Współautor, mogą uzyskać dostęp nie tylko do informacji rozliczeniowych, ale również do usług platformy Azure. Aby zarządzać danymi rolami, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
- Aby uzyskać więcej informacji o rolach, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
