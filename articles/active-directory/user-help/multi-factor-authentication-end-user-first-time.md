---
title: Co to jest strona Dodatkowa weryfikacja? — Azure AD
description: Jak przejść do strony dodatkowej weryfikacji zabezpieczeń na potrzeby weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77062561"
---
# <a name="what-is-the-additional-verification-page"></a>Co to jest strona Dodatkowa weryfikacja?

Dotarła do Ciebie wiadomość e-mail z działu IT lub przełożonego z informacją, że organizacja dodała dodatkową weryfikację zabezpieczeń do Twojego konta. Co to oznacza? Oznacza to, że Twoja organizacja podejmuje dodatkowe kroki, aby upewnić się, że jesteś osobą, za którą podajesz się podczas logowania. Ta dodatkowa weryfikacja, znana także jako weryfikacja dwuskładnikowa, odbywa się za pomocą kombinacji nazwy użytkownika, hasła oraz urządzenia przenośnego lub telefonu.

Weryfikacja dwuetapowa jest bezpieczniejsza niż tylko hasło, ponieważ opiera się on na dwóch formach uwierzytelniania: coś znanego i dla Ciebie. Coś, co znasz, to hasło. Coś, co masz, to telefon lub urządzenie, które często masz przy sobie. Weryfikacja dwuetapowa może pomóc w zablokowaniu przez użytkownika złośliwych hakerów, ponieważ nawet jeśli mają swoje hasło, szanse się, że nie mają one również Twojego urządzenia.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących korzystać z weryfikacji dwuskładnikowej przy użyciu konta służbowego (np. alain@contoso.com). Jeśli jesteś administratorem, aby uzyskać informacje na temat sposobu włączania weryfikacji dwuskładnikowej dla pracowników lub innych użytkowników, zapoznaj się z [dokumentacją dotyczącą uwierzytelniania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Kto decyduje o możliwości użycia tej funkcji?

W zależności od typu konta o konieczności korzystania z weryfikacji dwuskładnikowej może zdecydować Twoja organizacja lub ta decyzja może zostać przeniesiona na użytkownika.

- **Konto służbowe.** Jeśli używasz konta służbowego (na przykład alain@contoso.com), to Twoja organizacja decyduje, czy musisz korzystać z weryfikacji dwuskładnikowej i konkretnych metod weryfikacji. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia.

- **Osobiste konto Microsoft.** Weryfikację dwuskładnikowa można skonfigurować dla osobistych kont Microsoft (na przykład alain@outlook.com). Jeśli masz problemy z weryfikacją dwuetapową i osobistą konto Microsoft, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Ze względu na to, czy należy używać tej funkcji, możesz ją włączać i wyłączać w dowolnym momencie.

    >[!Note]
    >Jeśli masz problemy z weryfikacją dwuetapową i jednym z osobistych kont Microsoft (na przykład danielle@outlook.com), możesz wypróbować sugestie dotyczące [sposobu korzystania z weryfikacji dwuetapowej z konto Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Dostęp do strony dodatkowej weryfikacji zabezpieczeń

Po włączeniu i skonfigurowaniu weryfikacji dwuskładnikowej zostanie wyświetlony monit z prośbą o podanie dodatkowych informacji w celu zabezpieczenia konta.

![Monit o podanie większej ilości informacji](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Aby uzyskać dostęp do strony dodatkowej weryfikacji zabezpieczeń

1. Wybierz pozycję **dalej** z monitu **więcej informacji** .

    Zostanie wyświetlona strona **dodatkowa Weryfikacja zabezpieczeń** .

2. Na stronie **dodatkowej weryfikacji zabezpieczeń** należy zdecydować, która metoda weryfikacji dwuskładnikowej ma zostać użyta w celu zweryfikowania, czy po zalogowaniu się do konta służbowego lub szkolnego. Dostępne są następujące opcje:

    | Metoda kontaktu | Opis |
    | --- | --- |
    | Aplikacja mobilna | <ul><li>**Otrzymuj powiadomienia o weryfikacji.** Ta opcja powoduje wypchnięcie powiadomienia do aplikacji Authenticator na telefonie Smartphone lub tablecie. Wyświetl powiadomienie i, jeśli jest ono wiarygodne, wybierz pozycję **Uwierzytelnij** w aplikacji. Przed uwierzytelnieniem może być wymagane wprowadzenie numeru PIN przez użytkownika lub szkołę.</li><li>**Użyj kodu weryfikacyjnego.** W tym trybie aplikacja Authenticator generuje kod weryfikacyjny, który aktualizuje co 30 sekund. Wprowadź najbardziej aktualny kod weryfikacyjny na ekranie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Numer telefonu uwierzytelniania | <ul><li>**Połączenie telefoniczne** umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia.</li><li>**Wiadomość SMS** zawiera wiadomość tekstową zawierającą kod weryfikacyjny. Po wyświetleniu monitu w tekście należy odpowiedzieć na wiadomość tekstową lub wprowadzić kod weryfikacyjny podany w interfejsie logowania.</li></ul> |
    | Telefon służbowy | Umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="next-steps"></a>Następne kroki

Po uzyskaniu dostępu do strony **dodatkowej weryfikacji zabezpieczeń** należy wybrać i skonfigurować metodę weryfikacji dwuskładnikowej:

- [Konfigurowanie urządzenia przenośnego jako metody weryfikacji](multi-factor-authentication-setup-phone-number.md)

- [Konfigurowanie telefonu biurowego jako metody weryfikacji](multi-factor-authentication-setup-office-phone.md)

- [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Powiązane zasoby

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Zarządzanie hasłami aplikacji](multi-factor-authentication-end-user-app-passwords.md)

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md) 
