---
title: Co to jest strona Dodatkowa weryfikacja? — Azure AD
description: Jak przejść do strony dodatkowej weryfikacji zabezpieczeń na potrzeby weryfikacji dwuskładnikowej
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88795987"
---
# <a name="what-is-the-additional-verification-page"></a>Co to jest strona Dodatkowa weryfikacja?

Twoja organizacja podejmuje dodatkowe kroki, aby upewnić się, że jesteś w trakcie logowania. Ta dodatkowa Weryfikacja zabezpieczeń jest również znana jako weryfikacja dwuskładnikowa. Składa się z kombinacji nazwy użytkownika, hasła i urządzenia przenośnego lub telefonu. Jeśli wszystko, co chcesz zrobić, wyłączy weryfikację dwuskładnikową dla konto Microsoft takich jak alain@outlook.com , użyj instrukcji w temacie [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Obraz metod uwierzytelniania koncepcyjnego](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Weryfikacja dwuetapowa jest bezpieczniejsza niż tylko hasło, ponieważ opiera się on na dwóch formach uwierzytelniania:

- Coś, co wiesz, na przykład hasło.
- Coś, czego masz, jak telefon lub inne urządzenie, które chcesz przenieść.

Weryfikacja dwuetapowa może pomóc w zawieszeniu, że złośliwi hakerzy nie zachodzą. Nawet jeśli mają swoje hasło, szanse się, że nie mają one jeszcze urządzenia.

>[!Important]
>Jeśli jesteś administratorem, aby uzyskać informacje na temat sposobu włączania weryfikacji dwuskładnikowej dla pracowników lub innych użytkowników, zapoznaj się z [dokumentacją dotyczącą uwierzytelniania Azure Active Directory](../authentication/index.yml). Ten artykuł jest przeznaczony dla użytkowników próbujących korzystać z weryfikacji dwuskładnikowej przy użyciu konta służbowego (np alain@contoso.com .).

## <a name="who-decides-if-you-use-this-feature"></a>Kto decyduje o możliwości użycia tej funkcji?

Kto decyduje o tym, czy korzystasz z weryfikacji dwuskładnikowej, zależy od typu posiadanego konta:

- **Konto służbowe.** Jeśli używasz konta służbowego (np alain@contoso.com .), to w organizacji, czy używasz weryfikacji dwuskładnikowej, wraz z określonymi metodami weryfikacji. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia.

- **Osobiste konto Microsoft.** Można skonfigurować weryfikację dwuskładnikową dla osobistych kont Microsoft (np alain@outlook.com .). Możesz ją włączyć lub wyłączyć w dowolnym momencie, korzystając z prostych instrukcji [dotyczących włączania lub wyłączania weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Jeśli masz inne problemy z weryfikacją dwuskładnikową i jednym z osobistych kont Microsoft, możesz uzyskać więcej sugestii dotyczących [korzystania z weryfikacji dwuetapowej przy użyciu konto Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>Otwórz stronę dodatkowej weryfikacji zabezpieczeń

Po włączeniu weryfikacji dwuskładnikowej w organizacji przy każdym zalogowaniu zostanie wyświetlony monit z informacją, aby uzyskać więcej informacji na temat zabezpieczenia konta.

![Monit o podanie większej ilości informacji](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Aby uzyskać dostęp do strony dodatkowej weryfikacji zabezpieczeń

1. Wybierz pozycję **dalej** z monitu **więcej informacji** .

    Zostanie wyświetlona strona **dodatkowa Weryfikacja zabezpieczeń** .

2. Na stronie **dodatkowa Weryfikacja zabezpieczeń** wybierz metodę weryfikacji dwuskładnikowej, która ma zostać użyta do sprawdzenia, czy użytkownik jest zalogowany do konta służbowego. Dostępne są następujące opcje:

    | Metoda kontaktu | Opis |
    | --- | --- |
    | Aplikacja mobilna | <ul><li>**Otrzymuj powiadomienia o weryfikacji.** Ta opcja powoduje wypchnięcie powiadomienia do aplikacji Authenticator na telefonie Smartphone lub tablecie. Wyświetl powiadomienie i, jeśli jest ono wiarygodne, wybierz pozycję **Uwierzytelnij** w aplikacji. Przed uwierzytelnieniem może być wymagane wprowadzenie numeru PIN przez użytkownika lub szkołę.</li><li>**Użyj kodu weryfikacyjnego.** W tym trybie aplikacja generuje kod weryfikacyjny, który aktualizuje co 30 sekund. Wprowadź najbardziej aktualny kod weryfikacyjny na ekranie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Numer telefonu uwierzytelniania | <ul><li>**Połączenie telefoniczne** umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia.</li><li>**Wiadomość SMS** zawiera wiadomość tekstową zawierającą kod weryfikacyjny. Po wyświetleniu monitu w tekście należy odpowiedzieć na wiadomość tekstową lub wprowadzić kod weryfikacyjny podany w interfejsie logowania.</li></ul> |
    | Telefon służbowy | Umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz krzyżyka (#) na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="next-steps"></a>Następne kroki

Po wybraniu metody weryfikacji dwuskładnikowej na stronie **dodatkowej weryfikacji zabezpieczeń** należy ją skonfigurować:

- [Konfigurowanie urządzenia przenośnego jako metody weryfikacji](multi-factor-authentication-setup-phone-number.md)

- [Konfigurowanie telefonu biurowego jako metody weryfikacji](multi-factor-authentication-setup-office-phone.md)

- [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Powiązane zasoby

- [Logowanie przy użyciu weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-signin.md)

- [Uzyskaj pomoc dotyczącą weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-troubleshoot.md)