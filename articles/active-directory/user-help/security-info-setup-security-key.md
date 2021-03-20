---
title: Konfigurowanie klucza zabezpieczeń jako metody weryfikacji — Azure AD
description: Jak skonfigurować stronę informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować swoją tożsamość, aby użyć klucza zabezpieczeń Fast Identity online (FIDO2) jako metody weryfikacji.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88797670"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Skonfiguruj klucz zabezpieczeń jako metodę weryfikacji

Kluczy zabezpieczeń można używać jako metody logowania bezhasło w organizacji. Klucz zabezpieczeń to urządzenie fizyczne, które jest używane z unikatowym numerem PIN do logowania się do konta służbowego. Ponieważ klucze zabezpieczeń wymagają posiadania urządzenia fizycznego i czegoś tylko ty, jest on traktowany jako silniejsza metoda weryfikacji niż nazwa użytkownika i hasło.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Jeśli nie widzisz opcji klucz zabezpieczeń, istnieje możliwość, że Twoja organizacja nie zezwala na korzystanie z tej opcji w celu weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z działem pomocy technicznej w organizacji, aby uzyskać pomoc.

## <a name="security-verification-versus-password-reset-authentication"></a>Weryfikacja zabezpieczeń a uwierzytelnianie resetowania hasła

Metody informacji zabezpieczających są używane do weryfikacji zabezpieczeń dwuskładnikowych i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Sposób użycia |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="what-is-a-security-key"></a>Co to jest klucz zabezpieczeń?

Obecnie obsługujemy kilka projektów i dostawców kluczy zabezpieczeń przy użyciu protokołów uwierzytelniania bezhasło [online (Fido)](https://fidoalliance.org/fido2/) (FIDO2). Te klucze umożliwiają zalogowanie się do konta służbowego w celu uzyskania dostępu do zasobów w chmurze w organizacji przy użyciu obsługiwanego urządzenia i przeglądarki sieci Web.

Administrator lub Twoja organizacja dostarczy klucz zabezpieczeń, jeśli będzie potrzebny dla konta służbowego. Istnieją różne typy kluczy zabezpieczeń, których można użyć, na przykład klucz USB, który można podłączyć do urządzenia lub klawisza NFC, który został wybrany w czytniku NFC. Więcej informacji o kluczu zabezpieczeń, w tym o tym, jaki typ jest, można znaleźć w dokumentacji producenta.

> [!Note]
> Jeśli nie możesz użyć klucza zabezpieczeń FIDO2, istnieją inne metody weryfikacji bezhasła, których można użyć, takich jak aplikacja Microsoft Authenticator lub funkcja Windows Hello. Aby uzyskać więcej informacji na temat aplikacji Microsoft Authenticator, zobacz [co to jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md). Aby uzyskać więcej informacji na temat usługi Windows Hello, zobacz [Omówienie funkcji Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby można było zarejestrować klucz zabezpieczeń, należy wykonać następujące czynności:

- Administrator włączył tę funkcję do użycia w organizacji.

- Korzystasz z urządzenia z systemem Windows 10 w 2019 Update i przy użyciu obsługiwanej przeglądarki.

- Dysponujesz fizycznym kluczem zabezpieczeń zatwierdzonym przez administratora lub Twoją organizację. Klucz zabezpieczeń musi być zarówno FIDO2, jak i zgodny z firmą Microsoft. Jeśli masz pytania dotyczące klucza zabezpieczeń i tego, czy jest on zgodny, skontaktuj się z działem pomocy technicznej Twojej organizacji.

## <a name="register-your-security-key"></a>Rejestrowanie klucza zabezpieczeń

Przed zalogowaniem się do konta służbowego przy użyciu klucza należy utworzyć klucz zabezpieczeń i nadać mu unikatowy numer PIN. Być może zarejestrowano maksymalnie 10 kluczy na Twoim koncie. 

1. Przejdź do strony **mój profil** w obszarze https://myaccount.microsoft.com i zaloguj się, jeśli jeszcze tego nie zrobiono.

2. Wybierz pozycję **Informacje o zabezpieczeniach**, wybierz pozycję **Dodaj metodę**, a następnie wybierz pozycję **klucz zabezpieczeń** z listy rozwijanej **Dodaj metodę** .

    ![Pole dodawania metody z wybranym kluczem zabezpieczeń](media/security-info/security-info-security-key-add-method.png)

3. Wybierz pozycję **Dodaj**, a następnie wybierz typ klucza zabezpieczeń, czyli **urządzenie USB** lub **urządzenie NFC**.

    ![Wybierz, czy masz klucz zabezpieczeń USB lub NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Jeśli nie masz pewności, jakiego typu klucz zabezpieczeń masz, zapoznaj się z dokumentacją producenta. Jeśli nie masz pewności co do producenta, skontaktuj się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

4. Klucz zabezpieczeń jest dostępny fizycznie, a następnie w polu **klucz zabezpieczeń** wybierz pozycję **dalej**.

    ![Pole rejestracji uruchamiania klucza zabezpieczeń](media/security-info/security-info-security-key-start-setup.png)

    Zostanie wyświetlone nowe okno umożliwiające przeprowadzenie kroków konfigurowania nowej metody logowania.

5. W polu **Konfigurowanie nowej metody logowania** wybierz pozycję **dalej**, a następnie:

    - Jeśli klucz zabezpieczeń jest urządzeniem USB, Wstaw klucz zabezpieczeń do portu USB urządzenia.

    - Jeśli Twój klucz zabezpieczeń jest urządzeniem NFC, naciśnij swój klucz zabezpieczeń do czytnika.

6. Wpisz unikatowy numer PIN klucza zabezpieczeń w polu **zabezpieczenia systemu Windows** , a następnie wybierz przycisk **OK**.

    Powrócisz do **Ustawienia nowej metody logowania** .

7. Wybierz opcję **Dalej**.

8. Wróć do strony **informacje zabezpieczające** wpisz nazwę rozpoznawaną później dla nowego klucza zabezpieczeń, a następnie wybierz przycisk **dalej**.

    ![Strona informacje zabezpieczające, nadawanie nazwy kluczowi zabezpieczeń](media/security-info/security-info-security-key-name.png)

    Twój klucz zabezpieczeń jest zarejestrowany i gotowy do użycia w celu logowania się do konta służbowego.

9. Wybierz pozycję **gotowe** , aby zamknąć pole **klucz zabezpieczeń** .

    Strona **informacje zabezpieczające** zostanie zaktualizowana informacjami o kluczu zabezpieczeń.

    ![Strona z informacjami o zabezpieczeniach z pokazanymi wszystkimi zarejestrowanymi metodami](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Usuwanie klucza zabezpieczeń z informacji zabezpieczających

Jeśli użytkownik nie umieści lub nie chce już korzystać z klucza zabezpieczeń, można usunąć klucz z informacji zabezpieczających. Ponieważ spowoduje to zatrzymanie klucza zabezpieczeń z użyciem konta służbowego, klucz zabezpieczeń kontynuuje przechowywanie danych i poświadczeń. Aby usunąć dane i poświadczenia z samego klucza zabezpieczeń, należy postępować zgodnie z instrukcjami w sekcji [Resetowanie zabezpieczeń zgodnych z firmą Microsoft](#reset-your-security-key) w tym artykule.

1. Wybierz łącze **Usuń** z klucza zabezpieczeń do usunięcia.

2. W polu **Usuń klucz zabezpieczeń** wybierz pozycję **OK** .

    Klucz zabezpieczeń został usunięty i nie będzie już można go używać do logowania się do konta służbowego.

>[!Important]
>Jeśli usuniesz klucz zabezpieczeń przez pomyłkę, możesz zarejestrować go ponownie, korzystając z instrukcji przedstawionych w sekcji [jak zarejestrować klucz zabezpieczeń](#register-your-security-key) w tym artykule.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Zarządzanie ustawieniami klucza zabezpieczeń z ustawień systemu Windows

Ustawieniami klucza zabezpieczeń można zarządzać z poziomu aplikacji **Ustawienia systemu Windows** , w tym resetowania klucza zabezpieczeń i tworzenia nowego numeru PIN klucza zabezpieczeń.

### <a name="reset-your-security-key"></a>Zresetuj klucz zabezpieczeń

Aby usunąć wszystkie informacje o koncie przechowywanych w fizycznym kluczu zabezpieczeń, należy przywrócić klucz z powrotem do domyślnych ustawień fabrycznych. Zresetowanie klucza zabezpieczeń spowoduje usunięcie wszystkich danych z klucza, co pozwoli na rozpoczęcie od nowa.

>[!IMPORTANT]
>Zresetowanie klucza zabezpieczeń spowoduje usunięcie wszystkich danych z klucza i zresetowanie go do domyślnych ustawień fabrycznych.
>
> **Wszystkie dane i poświadczenia zostaną wyczyszczone.**

#### <a name="to-reset-your-security-key"></a>Aby zresetować klucz zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Wstaw swój klucz zabezpieczeń do portu USB lub naciśnij swój czytnik NFC, aby zweryfikować swoją tożsamość.

3. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w zależności od konkretnego producenta klucza zabezpieczeń. Jeśli klucz producenta nie znajduje się na liście instrukcji na ekranie, zapoznaj się z witryną producenta, aby uzyskać więcej informacji.

4. Wybierz pozycję **Zamknij** , aby zamknąć ekran **Zarządzanie** .

### <a name="create-a-new-security-key-pin"></a>Utwórz nowy numer PIN klucza zabezpieczeń

Nowy numer PIN klucza zabezpieczeń można utworzyć dla klucza zabezpieczeń.

#### <a name="to-create-a-new-security-key-pin"></a>Aby utworzyć nowy numer PIN klucza zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Wstaw swój klucz zabezpieczeń do portu USB lub naciśnij swój czytnik NFC, aby zweryfikować swoją tożsamość.
3. Wybierz pozycję **Dodaj** z obszaru **numeru PIN klucza zabezpieczeń** , wpisz i potwierdź nowy numer PIN klucza zabezpieczeń, a następnie wybierz przycisk **OK**.

     Klucz zabezpieczeń jest aktualizowany przy użyciu nowego numeru PIN klucza zabezpieczeń, który będzie używany z kontem służbowym. Jeśli zdecydujesz się ponownie zmienić kod PIN, możesz wybrać przycisk **Zmień** .
4. Wybierz pozycję **Zamknij** , aby zamknąć ekran **Zarządzanie** .

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Aby zarejestrować klucz zabezpieczeń, należy mieć co najmniej jedną zarejestrowaną dodatkową metodę weryfikacji zabezpieczeń. Aby uzyskać więcej informacji, zobacz [sekcję Omówienie](./security-info-setup-auth-app.md) . 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat metod weryfikacji bezhaseł, przeczytaj artykuł [Azure AD, który rozpoczyna publiczną wersję zapoznawczą kluczy zabezpieczeń FIDO2, włączając](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) w to Blog dotyczący logowania bezhaseł lub zapoznaj się z informacjami o [tym, co to jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md) i [Windows Hello — Omówienie](https://www.microsoft.com/windows/windows-hello) .

- Aby uzyskać bardziej szczegółowe informacje o [kluczach zabezpieczeń zgodnych z firmą Microsoft](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).