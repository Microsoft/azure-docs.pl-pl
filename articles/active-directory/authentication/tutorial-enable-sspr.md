---
title: Włączanie Azure Active Directory samoobsługowego resetowania hasła
description: Z tego samouczka dowiesz się, jak Azure Active Directory samoobsługowe resetowanie hasła dla grupy użytkowników i przetestować proces resetowania hasła.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861827"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Samouczek: umożliwianie użytkownikom odblokowywania konta lub resetowania haseł przy Azure Active Directory samoobsługowego resetowania haseł

Azure Active Directory (Azure AD) samoobsługowe resetowanie hasła (SSPR) umożliwia użytkownikom zmianę lub zresetowanie hasła bez angażowania administratora ani pomocy technicznej. Jeśli usługa Azure AD zablokuje konto użytkownika lub zapomni hasło, może postępować zgodnie z monitami, aby się odblokować i wrócić do pracy. Ta możliwość zmniejsza liczbę telefonów do działu pomocy technicznej i zmniejsza produktywność, gdy użytkownik nie może zalogować się do swojego urządzenia lub aplikacji. Zalecamy to wideo na [temat włączania i konfigurowania funkcji SSPR w usłudze Azure AD.](https://www.youtube.com/watch?v=rA8TvhNcCvQ) Mamy również wideo dla administratorów IT dotyczące rozwiązywania sześciu najbardziej typowych komunikatów o błędach użytkownika końcowego za [pomocą usługi SSPR.](https://www.youtube.com/watch?v=9RPrNVLzT8I)

> [!IMPORTANT]
> W tym samouczku pokazano administratorowi, jak włączyć samoobsługowe resetowanie hasła. Jeśli jesteś już zarejestrowanym użytkownikiem końcowym na potrzeby samoobsługowego resetowania hasła i musisz wrócić do swojego konta, przejdź do strony [resetowania haseł w](https://passwordreset.microsoftonline.com/) usłudze Microsoft Online.
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, należy skontaktować się z działem pomocy technicznej, aby uzyskać dodatkową pomoc.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania haseł dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Testowanie procesu SSPR jako użytkownik

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działa dzierżawa usługi Azure AD z włączoną co najmniej bezpłatną lub próbną licencją usługi Azure AD. W warstwie Bezpłatna usługa SSPR działa tylko dla użytkowników chmury w usłudze Azure AD. Zmiana hasła jest obsługiwana w warstwie Bezpłatna, ale resetowanie hasła nie jest obsługiwane. 
    * W przypadku kolejnych samouczków w tej serii będziesz potrzebować licencji Azure AD — wersja Premium P1 lub próbnej na potrzeby lokalnego zapisu zwrotnego haseł.
    * W razie potrzeby [utwórz bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Konto z *uprawnieniami administratora* globalnego.
* Użytkownik niebędący administratorem z hasłem, które znasz, na przykład *użytkownik testuser.* W tym samouczku przetestujemy środowisko funkcji SSPR użytkownika końcowego przy użyciu tego konta.
    * Jeśli musisz utworzyć użytkownika, zobacz [Szybki start: dodawanie nowych](../fundamentals/add-users-azure-active-directory.md)użytkowników do Azure Active Directory.
* Grupa, do których należy użytkownik niebędący administratorem, na przykład *SSPR-Test-Group.* W tym samouczku włączysz funkcję SSPR dla tej grupy.
    * Jeśli musisz utworzyć grupę, zobacz [Tworzenie grupy](../fundamentals/active-directory-groups-create-azure-portal.md)podstawowej i dodawanie członków przy użyciu Azure Active Directory .

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Usługa Azure AD umożliwia włączenie funkcji SSPR *dla opcji Brak,* *Wybrani* lub *Wszyscy* użytkownicy. Ta szczegółowa możliwość umożliwia wybranie podzbioru użytkowników do testowania procesu rejestracji funkcji SSPR i przepływu pracy. Jeśli dobrze ci się to przyjmuje, a czas jest odpowiedni do komunikowania wymagań szerszej grupie użytkowników, możesz wybrać grupę użytkowników, aby włączyć funkcję SSPR. Możesz też włączyć funkcję SSPR dla wszystkich użytkowników w dzierżawie usługi Azure AD.

> [!NOTE]
> Obecnie można włączyć tylko jedną grupę usługi Azure AD dla funkcji SSPR przy użyciu Azure Portal. W ramach szerszego wdrożenia usługi SSPR usługa Azure AD obsługuje grupy zagnieżdżone. Upewnij się, że użytkownicy w wybieranych grupach mają przypisane odpowiednie licencje. Obecnie nie ma żadnego procesu weryfikacji tych wymagań dotyczących licencjonowania.

W tym samouczku skonfigurujemy ustawienie SSPR dla zestawu użytkowników w grupie testowej. Użyj grupy *SSPR-Test-Group* i podaj własną grupę usługi Azure AD zgodnie z potrzebami:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta z *uprawnieniami administratora globalnego.*
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Resetowanie hasła** z menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcji Funkcja samoobsługowego resetowania *hasła jest włączona* wybierz pozycję **Wybierz grupę**
1. Wyszukaj i wybierz grupę usługi Azure AD, na przykład *SSPR-Test-Group,* a następnie wybierz *pozycję Wybierz.*

    [![Wybierz grupę w chmurze Azure Portal aby włączyć samoobsługowe resetowanie hasła](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Aby włączyć funkcję SSPR dla wybranych użytkowników, wybierz pozycję **Zapisz.**

## <a name="select-authentication-methods-and-registration-options"></a>Wybieranie metod uwierzytelniania i opcji rejestracji

Gdy użytkownicy muszą odblokować swoje konto lub zresetować hasło, zostanie wyświetlony monit o inną metodę potwierdzenia. Ten dodatkowy współczynnik uwierzytelniania zapewnia, że usługa Azure AD zakończyła tylko zatwierdzone zdarzenia SSPR. Na podstawie informacji rejestracyjnych zapewnianych przez użytkownika można wybrać metody uwierzytelniania, które mają być stosowane.

1. W menu po lewej stronie strony **Metody uwierzytelniania** ustaw pozycję Liczba metod wymaganych do zresetowania **na** *1*.

    Aby zwiększyć bezpieczeństwo, można zwiększyć liczbę metod uwierzytelniania wymaganych dla usługi SSPR.

1. Wybierz pozycję **Metody dostępne dla użytkowników,** na które organizacja chce zezwolić. W tym samouczku zaznacz pola wyboru, aby włączyć następujące metody:

    * *Powiadomienie aplikacji mobilnej*
    * *Kod aplikacji mobilnej*
    * *Poczta e-mail*
    * *Telefon komórkowy*

    Możesz włączyć inne metody uwierzytelniania, takie jak *telefon w* biurze lub pytania *zabezpieczające,* zgodnie z potrzebami, aby spełnić wymagania biznesowe.

1. Aby zastosować metody uwierzytelniania, wybierz pozycję **Zapisz.**

Aby użytkownicy mogą odblokować swoje konto lub zresetować hasło, muszą zarejestrować swoje informacje kontaktowe. Usługa Azure AD używa tych informacji kontaktowych dla różnych metod uwierzytelniania, które są ustawione w poprzednich krokach.

Administrator może ręcznie podać te informacje kontaktowe lub użytkownicy mogą przejść do portalu rejestracji, aby podać te informacje samodzielnie. W tym samouczku skonfigurujesz usługę Azure AD, aby monitować użytkowników o rejestrację podczas następnego logowania.

1. W menu po lewej stronie strony **Rejestracja** wybierz pozycję Tak dla opcji *Wymagaj* od użytkowników **rejestrowania się podczas logowania.**
1. Dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** ustaw wartość *180*.

    Ważne jest, aby informacje kontaktowe są aktualne. Jeśli podczas uruchamiania zdarzenia SSPR istnieją nieaktualne informacje kontaktowe, użytkownik może nie być w stanie odblokować swojego konta ani zresetować hasła.

1. Aby zastosować ustawienia rejestracji, wybierz pozycję **Zapisz.**

## <a name="set-up-notifications-and-customizations"></a>Konfigurowanie powiadomień i dostosowań

Aby informować użytkowników o aktywności konta, możesz skonfigurować usługę Azure AD do wysyłania powiadomień e-mail w przypadku wystąpienia zdarzenia SSPR. Te powiadomienia mogą obejmować zarówno zwykłe konta użytkowników, jak i konta administratorów. W przypadku kont administratorów to powiadomienie zapewnia kolejną warstwę świadomości, gdy hasło konta administratora uprzywilejowanego jest resetowane przy użyciu funkcji SSPR. Usługa Azure AD powiadomi wszystkich administratorów globalnych, gdy ktoś użyje hasła SSPR na koncie administratora.

1. W menu po lewej stronie strony **Powiadomienia** skonfiguruj następujące opcje:

   * Dla opcji **Czy powiadamiać użytkowników o resetowaniu hasła?** ustaw wartość *Tak*.
   * Dla opcji **Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło?** ustaw wartość *Tak*.

1. Aby zastosować preferencje powiadomień, wybierz pozycję **Zapisz.**

Jeśli użytkownicy potrzebują więcej pomocy w procesie SSPR, możesz dostosować link "Skontaktuj się z administratorem". Użytkownik może wybrać ten link w procesie rejestracji SSPR i po odblokowaniu konta lub zresetowaniu hasła. Aby upewnić się, że użytkownicy potrzebują pomocy technicznej, zdecydowanie zalecamy podanie niestandardowej wiadomości e-mail lub adresu URL pomocy technicznej.

1. W menu po lewej stronie strony **Dostosowywanie** ustaw opcję Dostosuj **link do pomocy technicznej** na wartość *Tak.*
1. W polu **Niestandardowy adres e-mail lub** adres URL pomocy technicznej podaj adres e-mail lub adres URL strony internetowej, gdzie użytkownicy mogą uzyskać dodatkową pomoc od organizacji, na przykład *https: \/ /support.contoso.com/*
1. Aby zastosować link niestandardowy, wybierz pozycję **Zapisz**.

## <a name="test-self-service-password-reset"></a>Testowanie funkcji samoobsługowego resetowania haseł

Po włączeniu i skonfigurowaniu funkcji SSPR przetestuj proces SSPR przy użyciu użytkownika, który należy do grupy wybranej w poprzedniej sekcji, na przykład *Test-SSPR-Group*. W poniższym przykładzie użyto *konta użytkownika testuser.* Podaj własne konto użytkownika. Jest to część grupy włączonej dla funkcji SSPR w pierwszej sekcji tego samouczka.

> [!NOTE]
> Podczas testowania samoobsługowego resetowania hasła użyj konta bez uprawnień administratora. Domyślnie usługa Azure AD włącza samoobsługowe resetowanie hasła dla administratorów. W celu zresetowania hasła wymagane jest użycie dwóch metod uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Administrator reset policy differences (Różnice w zasadach resetowania administratora).](concept-sspr-policy.md#administrator-reset-policy-differences)

1. Aby wyświetlić proces ręcznej rejestracji, otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do *adresu https: \/ /aka.ms/ssprsetup*. Usługa Azure AD przekieruje użytkowników do tego portalu rejestracji, gdy zalogują się następnym razem.
1. Zaloguj się przy użyciu użytkownika testowego bez uprawnień administratora, takiego *jak testuser,* i zarejestruj swoje informacje kontaktowe metod uwierzytelniania.
1. Po zakończeniu wybierz przycisk z **oznaczeniem Wygląda dobrze** i zamknij okno przeglądarki.
1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do *adresu https: \/ /aka.ms/sspr*.
1. Wprowadź informacje o koncie użytkownika testowego bez uprawnień administratora, takie jak *testuser,* znaki z testu CAPTCHA, a następnie wybierz przycisk **Dalej.**

    ![Wprowadź informacje o koncie użytkownika, aby zresetować hasło](media/tutorial-enable-sspr/password-reset-page.png)

1. Wykonaj kroki weryfikacji, aby zresetować hasło. Po zakończeniu otrzymasz wiadomość e-mail z powiadomieniem o zresetowaniu hasła.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W późniejszym samouczku z tej serii skonfigurujesz zwrotny zapis haseł. Ta funkcja zapisuje zmiany haseł z funkcji resetowania hasła usługi Azure AD z powrotem do lokalnego środowiska usługi AD. Jeśli chcesz kontynuować pracę z tą serią samouczków, aby skonfigurować funkcję zapisu zwrotnego haseł, nie wyłączaj funkcji SSPR teraz.

Jeśli nie chcesz już używać funkcji SSPR, które zostały już ustawione w ramach tego samouczka, ustaw stan funkcji SSPR na **Wartość Brak,** korzystając z następujących kroków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz **pozycję Resetowanie** hasła z menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcji Funkcja samoobsługowego resetowania *hasła jest włączona* wybierz pozycję **Brak.**
1. Aby zastosować zmianę SSPR, wybierz pozycję **Zapisz.**

## <a name="faqs"></a>Często zadawane pytania

W tej sekcji wyjaśniono typowe pytania od administratorów i użytkowników końcowych, którzy wypróbują usługę SSPR:

- Dlaczego użytkownicy federowani czekają do 2  minut, gdy zobaczą, że hasło zostało zresetowane, zanim będą oni w stanie używać haseł synchronizowanych ze środowisk lokalnych?

  W przypadku użytkowników federowanych, których hasła są synchronizowane, źródłem uprawnień dla haseł jest lokalne. W związku z tym funkcji SSPR aktualizuje tylko hasła lokalne. Synchronizacja skrótów haseł z powrotem z usługą Azure AD jest zaplanowana co 2 minuty.

- Gdy nowo utworzony użytkownik, który jest wstępnie wypełniony danymi SSPR, takimi jak telefon i wiadomość e-mail, odwiedzi stronę rejestracji SSPR, nie utrac dostępu **do swojego konta!** jest wyświetlany jako tytuł strony. Dlaczego inni użytkownicy, którzy mają wstępnie wypełnione dane SSPR, nie widzą komunikatu?

  Użytkownik, który widzi, że **nie tracisz dostępu do twojego konta!** jest członkiem usługi SSPR/grup rejestracji połączonych skonfigurowanych dla dzierżawy. Użytkownicy, którzy nie zobaczą, **nie utracą dostępu do Twojego konta!** nie były częścią grup rejestracji SSPR/połączonych.

- Dlaczego niektórzy użytkownicy nie widzą wskaźnika siły hasła, gdy przejdą przez proces resetowania hasła i zresetują swoje hasło?

  Użytkownicy, którzy nie widzą słabej/silnej siły hasła, mają włączone synchronizowanie funkcji zapisu zwrotnego haseł. Ponieważ SSPR nie może określić zasad haseł w środowisku lokalnym klienta, nie może zweryfikować siły ani słabego hasła. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono samoobsługowe resetowanie haseł usługi Azure AD dla wybranej grupy użytkowników. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania hasła dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Testowanie procesu SSPR jako użytkownik

> [!div class="nextstepaction"]
> [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](./tutorial-enable-azure-mfa.md)
