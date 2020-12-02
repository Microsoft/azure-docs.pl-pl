---
title: Zmiana informacji kontaktowych na koncie rozliczeniowym platformy Azure
description: Opisano tutaj sposób modyfikowania informacji kontaktowych konta rozliczeniowego platformy Azure
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperfq2
ms.openlocfilehash: 05b93fd02ec229306d4e3e150b1e8baab072b597
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671987"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Zmiana informacji kontaktowych na koncie rozliczeniowym platformy Azure

Ten artykuł pomoże Ci zaktualizować informacje kontaktowe dotyczące *konta rozliczeniowego* w witrynie Azure Portal. Instrukcje aktualizacji informacji kontaktowych różnią się w zależności od typu konta rozliczeniowego. Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](view-all-accounts.md). Konto rozliczeniowe platformy Azure jest oddzielone od konta użytkownika i [konta Microsoft](https://account.microsoft.com/).

Jeśli chcesz zaktualizować informacje o profilu użytkownika w usłudze Azure Active Directory, tylko administrator użytkowników może wprowadzić dane zmiany. Jeśli nie masz przypisanej roli administratora użytkowników, skontaktuj się z administratorem użytkowników. Aby uzyskać więcej informacji na temat zmian w profilu użytkownika, zobacz [Dodawanie lub aktualizowanie informacji w profilu użytkownika przy użyciu usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Adres kupującego* — adres kupującego to adres oraz informacje kontaktowe organizacji lub osoby odpowiedzialnej za konto rozliczeniowe. Jest on widoczny na wszystkich fakturach wygenerowanych dla konta rozliczeniowego.

*Adres płatnika* — adres płatnika to adres oraz informacje kontaktowe organizacji lub osoby odpowiedzialnej za faktury wygenerowane dla konta rozliczeniowego. W przypadku konta rozliczeniowego w programie subskrypcji usług online firmy Microsoft jest jeden adres płatnika, widoczny na wszystkich fakturach generowanych dla tego konta. W przypadku konta rozliczeniowego umowy z klientem firmy Microsoft każdy profil rozliczeniowy ma swój adres płatnika, widoczny na fakturze generowanej dla tego profilu rozliczeniowego.

*Kontaktowy adres e-mail dla wiadomości email dotyczących usług i marketingu* — możesz określić adres e-mail inny niż adres e-mail, za pomocą którego się logujesz, aby otrzymywać ważne powiadomienia dotyczące rozliczeń, usług i rekomendacji dotyczących Twojego konta platformy Azure. Wiadomości e-mail z powiadomieniami o usłudze, takimi jak pilne problemy z zabezpieczeniami, zmiany cen lub zmiany powodujące niezgodność usług używanych przez Twoje konto, są zawsze wysyłane na Twój adres logowania.

## <a name="update-an-mosp-billing-account-address"></a>Aktualizowanie adresu konta rozliczeniowego w programie subskrypcji usług online firmy Microsoft

1. Zaloguj się w witrynie Azure Portal przy użyciu adresu e-mail z uprawnieniami administratora konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
    ![Zrzut ekranu przedstawiający miejsce wyszukiwania narzędzia Cost Management + Billing w witrynie Azure Portal](./media/change-azure-account-profile/search-cmb.png)
1. Wybierz pozycję **Właściwości** po lewej stronie.  
    ![Zrzut ekranu przedstawiający właściwości konta rozliczeniowego programu subskrypcji usług online firmy Microsoft](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Wybierz pozycję **Aktualizuj adres na fakturze**, aby zaktualizować adresy kupującego i płatnika. Wprowadź nowy adres, a następnie wybierz pozycję **Zapisz**.  
    ![Zrzut ekranu przedstawiający aktualizowanie adresu na koncie rozliczeniowym programu subskrypcji usług online firmy Microsoft](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Aktualizowanie adresu kupującego konta rozliczeniowego umowy z klientem Microsoft

1. Zaloguj się w witrynie Azure Portal przy użyciu adresu e-mail z przypisaną rolą właściciela lub współautora na koncie rozliczeniowym umowy z klientem firmy Microsoft.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
    ![Zrzut ekranu przedstawiający miejsce wyszukiwania w witrynie Azure Portal](./media/change-azure-account-profile/search-cmb.png)
1. Wybierz pozycję **Właściwości** z lewej strony, a następnie wybierz pozycję **Aktualizuj kupującego**.  
    ![Zrzut ekranu przedstawiający właściwości konta rozliczeniowego umowy z klientem firmy Microsoft, na którym można zmodyfikować adres kupującego](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Wprowadź nowy adres i wybierz pozycję **Zapisz**.  
    ![Zrzut ekranu przedstawiający aktualizowanie adresu kupującego na koncie umowy z klientem Microsoft](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Niektóre konta wymagają dodatkowej weryfikacji w celu aktualizacji kupującego. Jeśli na Twoim koncie wymagane jest ręczne zatwierdzenie, pojawi się monit o kontakt z pomocą techniczną platformy Azure.

## <a name="update-an-mca-billing-account-address"></a>Aktualizowanie adresu konta rozliczeniowego umowy z klientem firmy Microsoft

1. Zaloguj się w witrynie Azure Portal przy użyciu adresu e-mail z przypisaną rolą właściciela lub współautora na koncie rozliczeniowym lub w profilu rozliczeniowym umowy z klientem firmy Microsoft.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1. Po lewej stronie wybierz pozycję **Profile rozliczeniowe**.
1. Wybierz profil rozliczeniowy, aby zaktualizować adres rozliczeniowy.  
    ![Zrzut ekranu przedstawiający stronę Profile rozliczeniowe, na której wybierasz profil rozliczeniowy](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Wybierz pozycję **Właściwości** po lewej stronie.
1. Wybierz pozycję **Aktualizuj adres**.  
    ![Zrzut ekranu przedstawiający miejsce aktualizowania adresu](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Wprowadź nowy adres, a następnie wybierz pozycję **Zapisz**.  
    ![Zrzut ekranu przedstawiający aktualizowanie adresu](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Wiadomości e-mail dotyczące usług i marketingu

Co 90 dni w witrynie Azure Portal otrzymasz monit o zweryfikowanie lub zaktualizowanie swojego adresu e-mail. Firma Microsoft wysyła na ten adres wiadomości e-mail z informacjami dotyczącymi konta platformy Azure, związane z następującymi zagadnieniami:

- Powiadomienia dotyczące usług
- Alerty zabezpieczeń
- Cele związane z rozliczeniami
- Pomoc techniczna
- Komunikaty marketingowe
- Zalecenia dotyczące najlepszych rozwiązań, na podstawie Twojego użycia platformy Azure

Wprowadź adres e-mail, na który chcesz otrzymywać wiadomości dotyczące konta. Wprowadzając adres e-mail, wyrażasz zgodę na otrzymywanie wiadomości od firmy Microsoft.

![Przykład monitu o aktualizację informacji kontaktowych](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Zmienianie kontaktowego adresu e-mail

Kontaktowy adres e-mail można zmienić, korzystając z jednej z następujących metod. Aktualizacja kontaktowego adresu e-mail nie oznacza aktualizacji adresu e-mail, za pomocą którego się logujesz.

1. Jeśli jesteś administratorem konta programu subskrypcji usług online firmy Microsoft, postępuj według instrukcji w sekcji [Aktualizowanie adresu konta rozliczeniowego w programie subskrypcji usług online firmy Microsoft](#update-an-mosp-billing-account-address), a w ostatnim kroku wybierz pozycję **Aktualizuj informacje kontaktowe**. Następnie wprowadź nowy adres e-mail.
1. Przejdź do obszaru [Informacje kontaktowe](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) w witrynie Azure Portal i wprowadź nowy adres e-mail. 
1. W witrynie Azure Portal wybierz ikonę z Twoimi inicjałami lub zdjęciem. Wybierz menu kontekstowe ( **...** ). Następnie wybierz pozycję **Moje informacje kontaktowe** z menu i wprowadź nowy adres e-mail.

![Przykład aktualizacji adresu e-mail na platformie Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Rezygnacja z marketingowych wiadomości e-mail

Aby zrezygnować z otrzymywania marketingowych wiadomości e-mail:

1. Przejdź do [formularza zgłoszenia](https://account.microsoft.com/profile/permissions-link-request), aby przesłać zgłoszenie za pomocą adresu e-mail profilu. Otrzymasz pocztą e-mail link umożliwiający zaktualizowanie Twoich preferencji.
1. Wybierz ten link, aby otworzyć stronę **Manage communication permissions** (Zarządzanie uprawnieniami dotyczącymi komunikacji). Na tej stronie widoczne są typy wiadomości marketingowych, które mogą być wysyłane na Twój adres e-mail. Wyczyść wybrane elementy, których nie chcesz już otrzymywać, a następnie wybierz przycisk **Zapisz**.  
    ![Przykładowa strona do zarządzania uprawnieniami dotyczącymi komunikacji](./media/change-azure-account-profile/manage-communication-permissions.png)

Nawet jeśli zrezygnujesz z otrzymywania wiadomości marketingowych, nadal będziesz otrzymywać powiadomienia dotyczące usług odpowiednie do Twojego konta.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Aktualizowanie adresu e-mail, za pomocą którego się logujesz

Nie ma możliwości aktualizacji adresu e-mail używanego do uzyskiwania dostępu do konta. Jeśli jednak masz konto rozliczeniowe programu subskrypcji usług online firmy Microsoft, możesz zarejestrować kolejne konto przy użyciu nowego adresu e-mail i przenieść własność subskrypcji na to nowe konto. W przypadku konta rozliczeniowego umowy z klientem firmy Microsoft możesz przyznać nowemu adresowi e-mail uprawnienia do konta.

## <a name="update-your-credit-card"></a>Aktualizowanie karty kredytowej

Aby dowiedzieć się, jak zaktualizować kartę kredytową, zobacz [Zmiana karty kredytowej używanej do płacenia za subskrypcję platformy Azure](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Aktualizowanie kraju lub regionu

Zmiana kraju lub regionu dla istniejącego konta nie jest obsługiwana. Możesz jednak utworzyć nowe konto w innym kraju lub regionie, a następnie skontaktować się z pomocą techniczną platformy Azure, aby przenieść subskrypcję na to nowe konto.

## <a name="change-the-subscription-name"></a>Zmienianie nazwy subskrypcji

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Subskrypcja** w okienku po lewej stronie, a następnie wybierz subskrypcję, której nazwę chcesz zmienić.
1. Wybierz pozycję **Przegląd**, a następnie polecenie **Zmień nazwę** na pasku poleceń.  
    ![Przykład zmiany nazwy subskrypcji platformy Azure](./media/change-azure-account-profile/rename-sub.png)
1. Po zmianie nazwy wybierz pozycję **Zapisz**.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie kont rozliczeniowych](view-all-accounts.md)
