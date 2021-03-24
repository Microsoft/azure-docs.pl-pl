---
title: Włącz Azure Active Directory samoobsługowego resetowania hasła
description: W tym samouczku dowiesz się, jak Azure Active Directory włączyć Samoobsługowe resetowanie hasła dla grupy użytkowników i przetestować proces resetowania hasła.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253aa080b9c160141a274c57e0895291c78d2048
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887771"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Samouczek: umożliwienie użytkownikom odblokowania konta lub resetowania haseł przy użyciu Azure Active Directory samoobsługowego resetowania hasła

Azure Active Directory (usługa Azure AD) funkcja samoobsługowego resetowania haseł (SSPR) umożliwia użytkownikom zmianę lub Resetowanie hasła bez konieczności korzystania z administratora ani skontaktuj się z pomocą techniczną. Jeśli konto użytkownika jest zablokowane lub zapomni swoje hasło, może wykonać monit o odblokowanie siebie i zawracanie do pracy. Ta możliwość zmniejsza liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu ani w aplikacji. Oto film wideo dotyczący [konfigurowania i włączania samoobsługowego resetowania haseł w dzierżawie](https://www.youtube.com/watch?v=rA8TvhNcCvQ) (**zalecane**). Mamy również film wideo dla administratorów IT, który [rozwiązuje sześć najczęstszych komunikatów o błędach użytkowników końcowych za pomocą SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> W tym samouczku pokazano, jak włączyć funkcję samoobsługowego resetowania hasła. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do strony https://aka.ms/sspr .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włącz Samoobsługowe resetowanie hasła dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Przetestuj proces SSPR jako użytkownik

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z włączoną obsługą co najmniej Azure AD — wersja Bezpłatna lub wersji próbnej. W warstwie Bezpłatna SSPR działa tylko dla użytkowników chmury w usłudze Azure AD.
    * W przypadku kolejnych samouczków w tej serii wymagana jest licencja na Azure AD — wersja Premium P1 lub próbna dla lokalnego zapisywania zwrotnego haseł.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego* .
* Użytkownik niebędący administratorem z hasłem znanym, takim jak *Użytkownik testowy*. W tym samouczku przetestujesz środowisko SSPR użytkownika końcowego za pomocą tego konta.
    * Jeśli musisz utworzyć użytkownika, zobacz [Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Grupa, do której należy użytkownik niebędący administratorem, na przykład *SSPR-test-Group*. W tym samouczku włączysz SSPR dla tej grupy.
    * Jeśli musisz utworzyć grupę, zobacz jak [utworzyć grupę i dodać członków w Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Usługa Azure AD umożliwia włączenie opcji SSPR dla opcji *Brak*, *wybrane* lub *Wszyscy* użytkownicy. Ta szczegółowa funkcja umożliwia wybranie podzbioru użytkowników do testowania procesu rejestracji SSPR i przepływu pracy. Jeśli masz doświadczenie z procesem i można przekazać wymagania przez szerszego zestawu użytkowników, możesz wybrać grupę użytkowników do włączenia do SSPR. Możesz również włączyć SSPR dla wszystkich użytkowników w dzierżawie usługi Azure AD.

> [!NOTE]
>
> Obecnie można włączyć tylko jedną grupę usługi Azure AD dla SSPR przy użyciu Azure Portal. W ramach szerszego wdrożenia SSPR są obsługiwane zagnieżdżone grupy. Upewnij się, że użytkownicy w wybranych grupach mają przypisane odpowiednie licencje. Obecnie nie ma żadnego procesu weryfikacji tych wymagań licencyjnych.

W tym samouczku skonfigurujesz SSPR dla zestawu użytkowników w grupie testowej. W poniższym przykładzie jest używana grupa *SSPR-test-Group* . W razie konieczności Podaj własną grupę usługi Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego* .
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Resetowanie hasła** w menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcja samoobsługowe *Resetowanie hasła* wybierz pozycję **Wybierz grupę** .
1. Wyszukaj i wybierz grupę usługi Azure AD, np. *SSPR-test-Group*, a następnie wybierz *pozycję Wybierz*.

    [![Wybierz grupę w Azure Portal, aby włączyć funkcję samoobsługowego resetowania ](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) hasła](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Aby włączyć SSPR dla wybranych użytkowników, wybierz pozycję **Zapisz**.

## <a name="select-authentication-methods-and-registration-options"></a>Wybierz metody uwierzytelniania i opcje rejestracji

Gdy użytkownicy muszą odblokować swoje konto lub zresetować swoje hasło, są monitowani o podanie dodatkowej metody potwierdzenia. Ten dodatkowy czynnik uwierzytelniania gwarantuje, że tylko zatwierdzone zdarzenia SSPR są wykonywane. Możesz wybrać metody uwierzytelniania, które mają być dozwolone, na podstawie informacji rejestracyjnych udostępnianych przez użytkownika.

1. Na stronie **metody uwierzytelniania** w menu po lewej stronie Ustaw **liczbę metod wymaganych do zresetowania** do *1*.

    Aby zwiększyć bezpieczeństwo, można zwiększyć liczbę metod uwierzytelniania wymaganych przez SSPR.

1. Wybierz **metody dostępne dla użytkowników** , których organizacja chce zezwolić. Na potrzeby tego samouczka zaznacz pola wyboru, aby włączyć następujące metody:

    * *Powiadomienie aplikacji mobilnej*
    * *Kod aplikacji mobilnej*
    * *Poczta e-mail*
    * *Telefon komórkowy*

    Dodatkowe metody uwierzytelniania, takie jak *telefon biurowy* lub *pytania zabezpieczające*, można włączyć odpowiednio do potrzeb firmy.

1. Aby zastosować metody uwierzytelniania, wybierz pozycję **Zapisz**.

Zanim użytkownicy będą mogli odblokować swoje konto lub zresetować hasło, muszą zarejestrować swoje informacje kontaktowe. Te informacje kontaktowe są używane w przypadku różnych metod uwierzytelniania skonfigurowanych w poprzednich krokach.

Administrator może ręcznie podać te informacje kontaktowe, a użytkownicy mogą przejść do portalu rejestracji, aby podać same informacje. W tym samouczku Skonfiguruj użytkownikom monit o rejestrację podczas następnego logowania.

1. Na stronie **rejestracja** w menu po lewej stronie wybierz pozycję *tak* , aby **wymagać od użytkowników rejestrowania się podczas logowania**.
1. Ważne jest, aby informacje kontaktowe były aktualne. Jeśli informacje kontaktowe są nieaktualne po rozpoczęciu zdarzenia SSPR, użytkownik może nie być w stanie odblokować konta ani resetować hasła.

    Dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** ustaw wartość *180*.
1. Aby zastosować ustawienia rejestracji, wybierz pozycję **Zapisz**.

## <a name="configure-notifications-and-customizations"></a>Konfigurowanie powiadomień i dostosowań

Aby zapewnić użytkownikom informacje o aktywności konta, można skonfigurować powiadomienia e-mail, które będą wysyłane po wystąpieniu zdarzenia SSPR. Powiadomienia te mogą dotyczyć zarówno zwykłych kont użytkowników, jak i kont administratorów. W przypadku kont administratorów to powiadomienie stanowi dodatkową warstwę świadomości w przypadku resetowania hasła do konta administratora uprzywilejowanego za pomocą SSPR. Wszyscy administratorzy globalni otrzymają powiadomienie, gdy SSPR jest używany na koncie administratora.

1. Na stronie **powiadomienia** w menu po lewej stronie Skonfiguruj następujące opcje:

   * Dla opcji **Czy powiadamiać użytkowników o resetowaniu hasła?** ustaw wartość *Tak*.
   * Dla opcji **Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło?** ustaw wartość *Tak*.

1. Aby zastosować preferencje powiadamiania, wybierz pozycję **Zapisz**.

Jeśli użytkownicy potrzebują dodatkowej pomocy dotyczącej procesu SSPR, można dostosować link "Skontaktuj się z administratorem". Ten link jest używany w procesie rejestracji SSPR oraz gdy użytkownik odblokowuje swoje konto lub resetuje swoje hasło. Aby upewnić się, że użytkownicy uzyskają potrzebną pomoc techniczną, zdecydowanie zaleca się podanie niestandardowego adresu e-mail lub adres URL pomocy technicznej.

1. Na stronie **Dostosowywanie** w menu po lewej stronie Ustaw opcję *Dostosuj pomoc techniczną* na **wartość tak**.
1. W polu adres **E-mail niestandardowego lub adresu URL pomocy technicznej** Podaj adres e-mail lub adres URL strony sieci Web, w którym użytkownicy mogą uzyskać dodatkową pomoc w organizacji, np. *`https://support.contoso.com/`*
1. Aby zastosować niestandardowe łącze, wybierz pozycję **Zapisz**.

## <a name="test-self-service-password-reset"></a>Testowanie funkcji samoobsługowego resetowania haseł

Po włączeniu i skonfigurowaniu SSPR Przetestuj proces SSPR przy użyciu użytkownika, który należy do grupy wybranej w poprzedniej sekcji, takiej jak *test-SSPR-Group*. W poniższym przykładzie używane jest konto *Użytkownik testowy* . Podaj własne konto użytkownika, które jest częścią grupy włączonej dla SSPR w pierwszej sekcji tego samouczka.

> [!NOTE]
> Podczas testowania funkcji samoobsługowego resetowania hasła należy użyć konta niebędącego administratorem. Domyślnie Administratorzy są włączeni do samoobsługowego resetowania hasła i muszą używać dwóch metod uwierzytelniania do resetowania hasła. Aby uzyskać więcej informacji, zobacz temat [różnice w zasadach resetowania ustawień administratora](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Aby wyświetlić proces ręcznego rejestrowania, Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito, a następnie przejdź do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Użytkownicy powinni być kierowani do tego portalu rejestracji po następnym zalogowaniu.
1. Zaloguj się przy użyciu użytkownika testowego, takiego jak *Użytkownik testowy*, i zarejestruj swoje metody uwierzytelniania.
1. Po zakończeniu wybierz przycisk oznaczony jako **dobry** i Zamknij okno przeglądarki.
1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito, a następnie przejdź do [https://aka.ms/sspr](https://aka.ms/sspr) .
1. Wprowadź informacje o koncie użytkowników niebędących administratorami, takie jak *Użytkownik testowy*, znaki z CAPTCHA, a następnie wybierz przycisk **dalej**.

    ![Wprowadź informacje o koncie użytkownika w celu zresetowania hasła](media/tutorial-enable-sspr/password-reset-page.png)

1. Wykonaj kroki weryfikacji, aby zresetować hasło. Po zakończeniu powinna zostać odebrana wiadomość e-mail z powiadomieniem, że hasło zostało zresetowane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poniższym samouczku w tej serii można skonfigurować funkcję zapisywania zwrotnego haseł. Ta funkcja zapisuje zmiany haseł z usługi Azure AD SSPR z powrotem do lokalnego środowiska usługi AD. Jeśli chcesz kontynuować pracę z tą serią samouczków, aby skonfigurować funkcję zapisywania zwrotnego haseł, nie należy wyłączać SSPR teraz.

Jeśli nie chcesz już używać funkcji SSPR skonfigurowanych w ramach tego samouczka, Ustaw stan SSPR na **Brak** , wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Resetowanie hasła** w menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcja samoobsługowe *Resetowanie hasła* wybierz pozycję **Brak**.
1. Aby zastosować zmianę SSPR, wybierz pozycję **Zapisz**.

## <a name="faqs"></a>Często zadawane pytania

W tej sekcji objaśniono typowe pytania administratorów i użytkowników końcowych, którzy próbują SSPR:

- Dlaczego użytkownicy federacyjny czekają na 2 minuty, gdy hasło zostało **zresetowane** , zanim będą mogły korzystać z haseł synchronizowanych lokalnie?

  W przypadku użytkowników federacyjnych, których hasła są synchronizowane, Źródło uprawnień dla haseł jest lokalne. W związku z tym SSPR aktualizuje tylko lokalne hasła. Synchronizacja skrótów haseł z powrotem do usługi Azure AD jest zaplanowana na co 2 minuty.

- Gdy nowo utworzony użytkownik, który jest wstępnie wypełniony danymi SSPR, takimi jak telefon i poczta e-mail, na stronie rejestracji SSPR **nie utraci dostępu do konta** . pojawia się jako tytuł strony. Dlaczego inni użytkownicy, którzy nie mają wstępnie wypełnionych danych SSPR, zobaczą ten komunikat?

  Użytkownik, który **nie utraci dostępu do Twojego konta!** jest członkiem SSPR/połączonych grup rejestracji skonfigurowanych dla dzierżawcy. Użytkownicy, którzy nie zobaczą, **nie tracą dostępu do Twojego konta** . nie są częścią SSPR/połączonej grupy rejestracji.

- Gdy niektórzy użytkownicy przechodzą przez proces SSPR i zresetują swoje hasło, dlaczego nie widzą wskaźnika siły hasła?

  Użytkownicy, którzy nie widzą mocnej/silnej siły hasła, mają włączone zsynchronizowane zapisywanie zwrotne haseł. Ponieważ SSPR nie może ustalić zasad haseł środowiska lokalnego klienta, nie może sprawdzić poprawności siły lub słabego hasła. 

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka włączono funkcję samoobsługowego resetowania hasła usługi Azure AD dla wybranej grupy użytkowników. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włącz Samoobsługowe resetowanie hasła dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Przetestuj proces SSPR jako użytkownik

> [!div class="nextstepaction"]
> [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](./tutorial-enable-azure-mfa.md)
