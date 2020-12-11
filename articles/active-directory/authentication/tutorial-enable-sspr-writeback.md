---
title: Włącz zapisywanie zwrotne haseł Azure Active Directory
description: W tym samouczku dowiesz się, jak włączyć funkcję zapisywania zwrotnego haseł samoobsługowego resetowania hasła w usłudze Azure AD za pomocą Azure AD Connect, aby synchronizować zmiany z powrotem do środowiska lokalnego Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028057"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Samouczek: Azure Active Directory Włączanie funkcji zapisywania zwrotnego do samoobsługowego resetowania haseł w środowisku lokalnym

Dzięki usłudze Azure Active Directory (Azure AD) samoobsługowego resetowania hasła (SSPR) użytkownicy mogą zaktualizować swoje hasła lub odblokować swoje konto przy użyciu przeglądarki sieci Web. W środowisku hybrydowym, w którym usługa Azure AD jest połączona ze środowiskiem lokalnym Active Directory Domain Services (AD DS), ten scenariusz może spowodować, że hasła różnią się między tymi dwoma katalogami.

Funkcję zapisywania zwrotnego haseł można użyć do synchronizowania zmian haseł w usłudze Azure AD z powrotem do środowiska lokalnego AD DS. Azure AD Connect zapewnia bezpieczny mechanizm wysyłania tych haseł z powrotem do istniejącego katalogu lokalnego z usługi Azure AD.

> [!IMPORTANT]
> W tym samouczku pokazano, jak włączyć funkcję samoobsługowego resetowania haseł do środowiska lokalnego. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do strony https://aka.ms/sspr .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj wymagane uprawnienia do zapisywania zwrotnego haseł
> * Włącz opcję zapisywania zwrotnego haseł w Azure AD Connect
> * Włączanie zapisywania zwrotnego haseł w usłudze Azure AD SSPR

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z co najmniej Azure AD — wersja Premium włączoną licencją P1 lub próbną.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencjonowania usługi Azure AD SSPR](concept-sspr-licensing.md).
* Konto z uprawnieniami *administratora globalnego* .
* Usługa Azure AD skonfigurowana do samoobsługowego resetowania hasła.
    * W razie potrzeby [Wykonaj poprzedni samouczek, aby włączyć usługę Azure AD SSPR](tutorial-enable-sspr.md).
* Istniejące lokalne środowisko AD DS skonfigurowane z bieżącą wersją Azure AD Connect.
    * W razie konieczności Skonfiguruj Azure AD Connect przy użyciu ustawień [ekspresowych](../hybrid/how-to-connect-install-express.md) lub [niestandardowych](../hybrid/how-to-connect-install-custom.md) .
    * Aby można było używać funkcji zapisywania zwrotnego haseł, kontrolery domeny muszą być w systemie Windows Server 2012 lub nowszym.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurowanie uprawnień konta dla Azure AD Connect

Azure AD Connect pozwala synchronizować użytkowników, grupy i poświadczenia między środowiskiem lokalnym AD DS i usługą Azure AD. Zazwyczaj instaluje się Azure AD Connect na komputerze z systemem Windows Server 2012 lub nowszym, który jest przyłączony do lokalnej domeny AD DS.

Aby poprawnie współpracować z funkcją zapisywania zwrotnego SSPR, konto określone w Azure AD Connect musi mieć ustawione odpowiednie uprawnienia i opcje. Jeśli nie masz pewności, które konto jest aktualnie używane, Otwórz Azure AD Connect i wybierz opcję **Wyświetl bieżącą konfigurację** . Konto, do którego należy dodać uprawnienia, znajduje się na liście **zsynchronizowane katalogi**. Następujące uprawnienia i opcje muszą zostać ustawione na koncie:

* **Resetowanie hasła**
* **Uprawnienia do zapisu** w `lockoutTime`
* **Uprawnienia do zapisu** w `pwdLastSet`
* **Rozszerzone prawa** do "niewygasania hasła" w obiekcie głównym *poszczególnych domen* w tym lesie, jeśli nie zostały one jeszcze ustawione.

Jeśli te uprawnienia nie zostaną przypisane, zapisanie zwrotne może być prawidłowo skonfigurowane, ale użytkownicy napotykają błędy podczas zarządzania hasłami lokalnymi w chmurze. Do **tego obiektu i wszystkich obiektów zależnych,** które mają być wyświetlane, należy zastosować uprawnienia.  

> [!TIP]
>
> Jeśli hasła dla niektórych kont użytkowników nie są zapisywane z powrotem w katalogu lokalnym, należy się upewnić, że dziedziczenie nie jest wyłączone dla konta w środowisku Premium AD DS. Aby funkcja działała poprawnie, należy zastosować uprawnienia do zapisu dla haseł.

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego haseł, wykonaj następujące czynności:

1. W lokalnym środowisku AD DS Otwórz **Active Directory użytkowników i komputery** przy użyciu konta z odpowiednimi uprawnieniami *administratora domeny* .
1. Upewnij się, że **funkcje zaawansowane** zostały włączone w menu **Widok** .
1. W lewym panelu kliknij prawym przyciskiem myszy obiekt, który reprezentuje katalog główny domeny, a następnie wybierz pozycję **Właściwości**  >  **zabezpieczenia**  >  **Zaawansowane**.
1. Na karcie **uprawnienia** wybierz pozycję **Dodaj**.
1. Dla **podmiotu zabezpieczeń** wybierz konto, do którego mają być stosowane uprawnienia (konto używane przez Azure AD Connect).
1. Z listy rozwijanej **Zastosuj do** wybierz pozycję **obiekty podrzędne użytkownika**.
1. W obszarze *uprawnienia* zaznacz pole wyboru dla następującej opcji:
    * **Resetowanie hasła**
1. W obszarze *Właściwości* wybierz pola dla następujących opcji. Przewiń listę, aby znaleźć te opcje, które mogą być już ustawione domyślnie:
    * **LockoutTime zapisu**
    * **PwdLastSet zapisu**

    [![Ustaw odpowiednie uprawnienia w aktywnych użytkowników i komputerach dla konta, które jest używane przez Azure AD Connect ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png)](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zastosuj/OK** , aby zastosować zmiany i zamknąć wszystkie otwarte okna dialogowe.

Aktualizacja uprawnień może potrwać do godziny lub dłużej, aby te uprawnienia były replikowane do wszystkich obiektów w katalogu.

Zasady dotyczące haseł w lokalnym środowisku AD DS mogą uniemożliwiać poprawne przetwarzanie resetowania haseł. Aby zapisywanie zwrotne haseł działało najbardziej efektywnie, zasady grupy dla *minimalnego wieku hasła* muszą mieć wartość 0. To ustawienie można znaleźć w obszarze **Konfiguracja komputera zasady > zasad > ustawienia systemu Windows > ustawienia zabezpieczeń > zasad konta** w ramach programu `gpedit.msc` .

W przypadku aktualizacji zasad grupy poczekaj na replikację zaktualizowanych zasad lub Użyj `gpupdate /force` polecenia.

> [!Note]
> Aby hasła były zmieniane natychmiast, zapisywanie zwrotne haseł musi mieć wartość 0. Jeśli jednak użytkownicy przestrzegają zasad lokalnych, a *minimalny wiek hasła* jest ustawiony na wartość większą od zera, zapisywanie zwrotne haseł nadal działa po obliczeniu zasad lokalnych.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Włącz funkcję zapisywania zwrotnego haseł w Azure AD Connect

Jedną z opcji konfiguracji w Azure AD Connect jest zapisanie zwrotne haseł. Po włączeniu tej opcji zdarzenia zmiany hasła powodują Azure AD Connect synchronizacji zaktualizowanych poświadczeń z powrotem do środowiska lokalnego AD DS.

Aby włączyć funkcję zapisywania zwrotnego SSPR, należy najpierw włączyć opcję zapisywania zwrotnego w Azure AD Connect. Na serwerze Azure AD Connect wykonaj następujące czynności:

1. Zaloguj się do serwera Azure AD Connect i uruchom Kreatora konfiguracji **Azure AD Connect** .
1. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
1. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy platformy Azure, a następnie wybierz przycisk **dalej**.
1. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
1. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.

    ![Konfigurowanie Azure AD Connect do zapisywania zwrotnego haseł](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
1. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

## <a name="enable-password-writeback-for-sspr"></a>Włącz funkcję zapisywania zwrotnego haseł dla SSPR

Po włączeniu funkcji zapisywania zwrotnego haseł w Azure AD Connect teraz Skonfiguruj usługę Azure AD SSPR do zapisywania zwrotnego. Po włączeniu funkcji zapisywania zwrotnego haseł użytkownicy, którzy zmienią lub zresetują hasło, mają zaktualizowane hasło zsynchronizowane z powrotem do środowiska lokalnego AD DS.

Aby włączyć funkcję zapisywania zwrotnego haseł w SSPR, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, wybierz pozycję **Resetowanie hasła**, a następnie wybierz pozycję **integracja lokalna**.
1. Ustawić opcję **zapisu hasła wstecz do katalogu lokalnego?** *Yes*
1. Ustaw opcję **Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła?** na *tak*.

    ![Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD na potrzeby zapisywania zwrotnego haseł](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz już używać funkcji zapisywania zwrotnego SSPR skonfigurowanej w ramach tego samouczka, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, wybierz pozycję **Resetowanie hasła**, a następnie wybierz pozycję **integracja lokalna**.
1. Ustawić opcję **zapisu haseł wstecz do katalogu lokalnego?** do *nie*.
1. Ustaw opcję **Zezwalaj użytkownikom na odblokowywanie kont bez resetowania ich hasła?** do *nie*.

Jeśli nie chcesz już używać żadnej funkcji hasła, wykonaj następujące czynności na serwerze Azure AD Connect:

1. Zaloguj się do serwera Azure AD Connect i uruchom Kreatora konfiguracji **Azure AD Connect** .
1. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
1. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy platformy Azure, a następnie wybierz przycisk **dalej**.
1. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
1. Na stronie **funkcje opcjonalne** Usuń zaznaczenie pola wyboru obok pozycji **zapisywanie zwrotne haseł** i wybierz pozycję **dalej**.
1. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
1. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono funkcję zapisywania zwrotnego usługi Azure AD SSPR do środowiska lokalnego AD DS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Skonfiguruj wymagane uprawnienia do zapisywania zwrotnego haseł
> * Włącz opcję zapisywania zwrotnego haseł w Azure AD Connect
> * Włączanie zapisywania zwrotnego haseł w usłudze Azure AD SSPR

> [!div class="nextstepaction"]
> [Ocena ryzyka podczas logowania](tutorial-risk-based-sspr-mfa.md)
