---
title: Tworzenie haseł aplikacji ze strony informacje zabezpieczające (wersja zapoznawcza) — Azure AD
description: Twórz automatycznie generowane hasła (hasła aplikacji), które mają być używane z dowolną aplikacją nieobsługującą przeglądarki, lub dowolną aplikacją, która nie obsługuje weryfikacji dwuskładnikowej w organizacji. To hasło aplikacji jest oddzielone od normalnego hasła i można je skonfigurować ze strony z informacjami o zabezpieczeniach.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537008"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Tworzenie haseł aplikacji na stronie informacje zabezpieczające (wersja zapoznawcza)

Niektóre aplikacje, takie jak Outlook 2010, nie obsługują weryfikacji dwuetapowej. Brak pomocy technicznej oznacza, że jeśli korzystasz z weryfikacji dwuetapowej w organizacji, aplikacja nie będzie działała. Aby obejść ten problem, możesz utworzyć automatycznie generowane hasło, które będzie używane z każdą aplikacją nie korzystającą z przeglądarki, niezależnie od normalnego hasła.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Administrator może nie zezwalać na korzystanie z haseł aplikacji. Jeśli nie widzisz **haseł aplikacji** jako opcji, nie są one dostępne w Twojej organizacji.

W przypadku korzystania z haseł aplikacji ważne jest, aby pamiętać:

- Hasła aplikacji są generowane automatycznie i należy je utworzyć i wprowadzić raz na aplikację.

- Dla każdego użytkownika istnieje limit 40 haseł. Jeśli spróbujesz utworzyć jeden po tym limicie, zostanie wyświetlony monit o usunięcie istniejącego hasła przed utworzeniem nowego.

    >[!Note]
    >Klienci korzystający z pakietu Office 2013 (w tym Outlook) obsługują nowe protokoły uwierzytelniania i mogą być używani do weryfikacji dwuetapowej. Ta pomoc techniczna oznacza, że po włączeniu weryfikacji dwuetapowej nie będą już potrzebne hasła aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz artykuł [jak nowoczesne uwierzytelnianie działa dla pakietów office 2013 i office 2016 Client Apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Utwórz nowe hasła aplikacji

Jeśli korzystasz z weryfikacji dwuetapowej przy użyciu konta służbowego, a administrator włączył informacje zabezpieczające, możesz tworzyć i usuwać hasła aplikacji przy użyciu strony **informacje zabezpieczające** .

>[!Note]
>Jeśli administrator nie włączył środowiska informacje zabezpieczające, należy postępować zgodnie z instrukcjami i informacjami w sekcji [Zarządzanie hasłami aplikacji na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Aby utworzyć nowe hasło aplikacji

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz z listy rozwijanej pozycję **hasło aplikacji** , a następnie wybierz pozycję **Dodaj**.

    ![Pole dodawania metody z wybranym hasłem aplikacji](media/security-info/securityinfo-myprofile-addpassword.png)

4. Wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz przycisk **dalej**.

    ![Zrzut ekranu przedstawiający stronę "hasło aplikacji" o nazwie wprowadzonej aplikacji.](media/security-info/securityinfo-myprofile-password-appname.png)

5. Skopiuj tekst z pola **hasło** , wklej je w obszarze hasło aplikacji (w tym przykładzie w programie Outlook 2010), a następnie wybierz pozycję **gotowe**.

    ![Strona hasła aplikacji z nazwą aplikacji](media/security-info/securityinfo-myprofile-password-copytext.png)

    Hasło jest dodawane i można pomyślnie zalogować się do aplikacji.

## <a name="delete-your-app-passwords"></a>Usuwanie haseł aplikacji

Jeśli nie musisz już korzystać z aplikacji, która wymaga hasła aplikacji, możesz usunąć skojarzone hasło aplikacji. Usunięcie hasła aplikacji zwolni jedną z dostępnych miejsc hasła aplikacji do użycia w przyszłości.

>[!Important]
>W przypadku usunięcia hasła aplikacji przez pomyłkę nie ma możliwości jego cofnięcia. Należy utworzyć nowe hasło aplikacji i wprowadzić je ponownie w aplikacji, wykonując kroki opisane w sekcji [Tworzenie nowych haseł aplikacji](#create-new-app-passwords) w tym artykule.

### <a name="to-delete-an-app-password"></a>Aby usunąć hasło aplikacji

1. Na stronie **informacje zabezpieczające** wybierz łącze **Usuń** obok opcji **hasło aplikacji** dla określonej aplikacji.

    ![Link umożliwiający usunięcie metody hasła aplikacji z informacji zabezpieczających](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć **hasło aplikacji**. Po usunięciu hasła aplikacji zostanie ono usunięte z informacji zabezpieczających i zniknie ze strony z **informacjami o zabezpieczeniach** .

## <a name="for-more-information"></a>Więcej informacji

- Aby uzyskać więcej informacji na temat strony **informacje zabezpieczające** i sposobu jej konfiguracji, zobacz [informacje zabezpieczające — Omówienie](./security-info-setup-signin.md)