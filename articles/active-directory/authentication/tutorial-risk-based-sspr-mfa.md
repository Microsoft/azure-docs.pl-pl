---
title: Ochrona logowania użytkownika oparta na ryzyku w Azure Active Directory
description: W tym samouczku dowiesz się, jak włączyć usługę Azure Identity Protection, aby chronić użytkowników, gdy na koncie zostanie wykryte ryzykowne zachowanie logowania.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d0fcd57a71baec54fbed2dd41a936895ad9a462
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966580"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>Samouczek: Użyj wykrywania ryzyka dla logowania użytkowników, aby wyzwolić zmiany w usłudze Azure Multi-Factor Authentication lub hasła

Aby chronić użytkowników, można skonfigurować zasady oparte na ryzyku w Azure Active Directory (Azure AD), które automatycznie reagują na ryzykowne zachowania. Zasady Azure AD Identity Protection mogą automatycznie blokować próby logowania lub wymagać dodatkowych akcji, takich jak wymaganie zmiany hasła lub monitu o usługę Azure Multi-Factor Authentication. Te zasady współpracują z istniejącymi zasadami dostępu warunkowego usługi Azure AD jako dodatkową warstwą ochrony dla organizacji. Użytkownicy mogą nigdy nie wyzwolić ryzykownego zachowania w ramach jednej z tych zasad, ale organizacja jest chroniona, jeśli zostanie podjęta próba złamania zabezpieczeń.

> [!IMPORTANT]
> W tym samouczku pokazano, jak włączyć oparty na ryzyku Multi-Factor Authentication platformy Azure.
>
> Jeśli Twój zespół IT nie włączył możliwości korzystania z usługi Azure Multi-Factor Authentication lub masz problemy podczas logowania, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Poznaj dostępne zasady dla Azure AD Identity Protection
> * Włącz rejestrację usługi Azure Multi-Factor Authentication
> * Włączanie zmiany haseł opartej na ryzykach
> * Włączanie uwierzytelniania wieloskładnikowego opartego na ryzykach
> * Testowanie zasad opartych na ryzyku dla prób logowania użytkowników

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z co najmniej Azure AD — wersja Premium włączeniu licencji P2 lub wersji próbnej.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego* .
* Usługa Azure AD skonfigurowana do samoobsługowego resetowania haseł i Multi-Factor Authentication platformy Azure
    * W razie potrzeby [Uzupełnij samouczek, aby włączyć usługę Azure AD SSPR](tutorial-enable-sspr.md).
    * W razie potrzeby [Uzupełnij samouczek, aby włączyć usługę Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Omówienie Azure AD Identity Protection

Codziennie firma Microsoft zbiera i analizuje biliony anonimowe sygnałów w ramach prób logowania użytkownika. Sygnały te pomagają tworzyć wzorce dobrego działania związanego z logowaniem użytkownika i identyfikować potencjalne, ryzykowne próby logowania. Azure AD Identity Protection mogą przeglądać próby logowania użytkowników i podejmować dodatkowe działania w przypadku podejrzanych zachowań:

Niektóre z następujących akcji mogą wyzwolić Azure AD Identity Protection wykrywania ryzyka:

* Użytkownicy z nieujawnionymi poświadczeniami.
* Logowania z anonimowych adresów IP.
* Niemożliwa podróż do nietypowych lokalizacji.
* Logowania z zainfekowanych urządzeń.
* Logowania z adresów IP z podejrzanymi działaniami.
* Logowania z nieznanych lokalizacji.

Poniższe trzy zasady są dostępne w Azure AD Identity Protection, aby chronić użytkowników i odpowiadać na podejrzane działania. Możesz włączyć lub wyłączyć wymuszanie zasad, wybierz opcję Użytkownicy lub grupy, dla których zasady mają być stosowane, i zdecyduj, czy chcesz zablokować dostęp przy logowaniu lub Monituj o dodatkową akcję.

* Zasady ryzyka związanego z użytkownikiem
    * Identyfikuje konta użytkowników, które mogą mieć naruszone poświadczenia, i odpowiada na nie. Może monitować użytkownika o utworzenie nowego hasła.
* Zasady dotyczące ryzyka związanego z logowaniem
    * Identyfikuje i reaguje na podejrzane próby logowania. Może monitować użytkownika o dostarczenie dodatkowych form weryfikacji za pomocą usługi Azure Multi-Factor Authentication.
* Zasady rejestracji uwierzytelniania wieloskładnikowego
    * Upewnij się, że użytkownicy są zarejestrowani do usługi Azure Multi-Factor Authentication. Jeśli zasady dotyczące ryzyka związanego z logowaniem w usłudze MFA są wyświetlane, użytkownik musi już być zarejestrowany dla Multi-Factor Authentication platformy Azure.

Po włączeniu zasad dotyczących ryzyka związanego z użytkownikiem zasad lub logowaniem można również wybrać próg dla poziomu ryzyka — *niska i powyżej*, *średni i wyższy*lub *wysoki*. Ta elastyczność pozwala określić, jak agresywne ma być wymuszanie wszelkich kontroli nad podejrzanymi zdarzeniami logowania.

Aby uzyskać więcej informacji na temat Azure AD Identity Protection, zobacz [co to jest Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Włącz zasady rejestracji usługi MFA

Azure AD Identity Protection obejmuje domyślne zasady, które mogą pomóc użytkownikom zarejestrowanym w usłudze Azure Multi-Factor Authentication. W przypadku używania dodatkowych zasad do ochrony zdarzeń związanych z logowaniem użytkownicy muszą być już zarejestrowani do uwierzytelniania wieloskładnikowego. Włączenie tych zasad nie wymaga, aby użytkownicy wykonywały uwierzytelnianie wieloskładnikowe przy każdym zdarzeniu logowania. Zasady sprawdzają tylko stan rejestracji użytkownika i pytają je przed zarejestrowaniem, jeśli jest to konieczne.

Zaleca się włączenie zasad rejestracji usługi MFA dla użytkowników, którzy mają być włączeni do dodatkowych zasad Azure AD Identity Protection. Aby włączyć te zasady, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, wybierz pozycję **zabezpieczenia**, a następnie w obszarze tytuł menu *Ochrona* wybierz pozycję **Ochrona tożsamości**.
1. Z menu po lewej stronie wybierz pozycję **zasady rejestracji usługi MFA** .
1. Domyślnie zasady mają zastosowanie do *wszystkich użytkowników*. W razie potrzeby wybierz pozycję **przypisania**, a następnie wybierz użytkowników lub grupy, dla których mają zostać zastosowane zasady.
1. W obszarze *formanty*wybierz pozycję **dostęp**. Upewnij się, że opcja *Wymagaj rejestracji w usłudze Azure MFA* jest zaznaczona, a następnie wybierz **pozycję Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na *włączone*, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sposób, w jaki użytkownicy mogą rejestrować się w usłudze MFA w Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Włącz zasady ryzyka dla użytkowników dotyczące zmiany hasła

Firma Microsoft współpracuje z naukowcami, organami ścigania, różnymi zespołami ds. zabezpieczeń w firmie Microsoft i innymi zaufanymi źródłami w celu wyszukiwania par „nazwa użytkownika i hasło”. Jeśli jedna z tych par pasuje do konta w danym środowisku, można zażądać zmiany hasła opartego na ryzyku. Te zasady i akcje wymagają aktualizacji hasła przed zalogowaniem się, aby upewnić się, że wszystkie wcześniej uwidocznione poświadczenia nie będą już działać.

Aby włączyć te zasady, wykonaj następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **zasady ryzyka użytkownika** .
1. Domyślnie zasady mają zastosowanie do *wszystkich użytkowników*. W razie potrzeby wybierz pozycję **przypisania**, a następnie wybierz użytkowników lub grupy, dla których mają zostać zastosowane zasady.
1. W obszarze *warunki*wybierz  **pozycję Wybierz warunki > wybierz poziom ryzyka**, a następnie wybierz *średni i powyżej*.
1. Wybierz pozycję **Wybierz**, a następnie opcję **gotowe**.
1. W obszarze *dostęp*wybierz pozycję **dostęp**. Upewnij się, że opcja **Zezwalaj na dostęp** i *Wymagaj zmiany hasła* jest zaznaczona, a następnie wybierz **pozycję Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na *włączone*, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sposób włączania zasad ryzyka dla użytkowników w Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Włącz zasady dotyczące ryzyka związanego z logowaniem usługi MFA

Większość użytkowników ma normalne zachowanie, które może być śledzone. Gdy wykraczają poza ten normę, może być ryzykowne, aby umożliwić im Pomyślne logowanie. Zamiast tego możesz chcieć zablokować tego użytkownika lub poproś o przeprowadzenie uwierzytelniania wieloskładnikowego. Jeśli użytkownik pomyślnie ukończy wyzwanie usługi MFA, można rozważyć jego prawidłową próbę logowania i udzielić dostępu do aplikacji lub usługi.

Aby włączyć te zasady, wykonaj następujące czynności:

1. Z menu po lewej stronie wybierz **zasady dotyczące ryzyka związanego z logowaniem** .
1. Domyślnie zasady mają zastosowanie do *wszystkich użytkowników*. W razie potrzeby wybierz pozycję **przypisania**, a następnie wybierz użytkowników lub grupy, dla których mają zostać zastosowane zasady.
1. W obszarze *warunki*wybierz  **pozycję Wybierz warunki > wybierz poziom ryzyka**, a następnie wybierz *średni i powyżej*.
1. Wybierz pozycję **Wybierz**, a następnie opcję **gotowe**.
1. W obszarze *dostęp*wybierz **pozycję Wybierz formant**. Upewnij się, że opcja **Zezwalaj na dostęp** i *Wymagaj uwierzytelniania wieloskładnikowego* jest zaznaczona, a następnie wybierz **pozycję Wybierz**.
1. Ustaw ustawienie **Wymuszaj zasady** na *włączone*, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sposób włączania zasad dotyczących ryzyka związanego z logowaniem w Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Zdarzenia podpisywania ryzykownych testów

Większość zdarzeń logowania użytkownika nie wyzwala zasad opartych na ryzyku skonfigurowanych w poprzednich krokach. Użytkownik nigdy nie widzi monitu o dodatkowe uwierzytelnianie wieloskładnikowe lub resetowania hasła. Jeśli ich poświadczenia pozostaną bezpieczne i zachowanie ich spójności, zdarzenia logowania będą się kończyły pomyślnie.

Aby przetestować zasady Azure AD Identity Protection utworzone w poprzednich krokach, trzeba zasymulować ryzykowne zachowanie lub potencjalne ataki. Kroki prowadzące do tych testów różnią się w zależności od zasad Azure AD Identity Protection, które chcesz zweryfikować. Aby uzyskać więcej informacji o scenariuszach i krokach, zobacz [symulowanie wykrywania ryzyka w Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zostały zakończone testy i nie chcesz już korzystać z zasad opartych na ryzyku, Wróć do poszczególnych zasad, które chcesz wyłączyć, a następnie ustaw ustawienie **Wymuszaj zasady** na *wyłączone*.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono zasady użytkownika oparte na ryzyku dla Azure AD Identity Protection. W tym samouczku omówiono:

> [!div class="checklist"]
> * Poznaj dostępne zasady dla Azure AD Identity Protection
> * Włącz rejestrację usługi Azure Multi-Factor Authentication
> * Włączanie zmiany haseł opartej na ryzykach
> * Włączanie uwierzytelniania wieloskładnikowego opartego na ryzykach
> * Testowanie zasad opartych na ryzyku dla prób logowania użytkowników

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
