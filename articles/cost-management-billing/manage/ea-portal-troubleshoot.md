---
title: Rozwiązywanie problemów z dostępem do portalu Umowy Enterprise platformy Azure
description: W tym artykule opisano najczęstsze problemy z Umową Enterprise (EA) w portalu EA platformy Azure.
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: fdcce868cb53c0fdd6a51f2b351491e1ee998d61
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691182"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Rozwiązywanie problemów z dostępem do portalu Umowy Enterprise platformy Azure

W tym artykule opisano najczęstsze problemy z Umową Enterprise (EA) w portalu EA platformy Azure. Portal EA platformy Azure służy do zarządzania użytkownikami i kosztami umowy Enterprise. Podczas konfigurowania lub aktualizowania dostępu do portalu EA platformy Azure mogą wystąpić poniższe problemy.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemy z dodawaniem administratora do rejestracji

Istnieją różne typy poziomów uwierzytelniania dla rejestracji przedsiębiorstwa. W razie zastosowania nieprawidłowych poziomów uwierzytelniania mogą wystąpić problemy przy próbie uzyskania dostępu do portalu EA platformy Azure.

Portal EA platformy Azure służy do przyznawania uprawnień użytkownikom z różnymi poziomami uwierzytelniania. Administrator przedsiębiorstwa może zaktualizować poziom uwierzytelniania w celu spełnienia wymagań bezpieczeństwa organizacji.

### <a name="authentication-level-types"></a>Typy poziomów uwierzytelniania

- Tylko konto Microsoft — dla organizacji, które chcą tworzyć użytkowników, używać ich i zarządzać nimi za pomocą kont Microsoft.
- Konto służbowe — dla organizacji, które skonfigurowały usługę Active Directory Federation w chmurze i mają wszystkie konta w jednej dzierżawie.
- Konto służbowe między dzierżawami — dla organizacji, które skonfigurowały usługę Active Directory Federation w chmurze i mają konta w wielu dzierżawach.
- Konto mieszane — umożliwia dodawanie użytkowników przy użyciu konta Microsoft lub konta służbowego.

Pierwsze konto służbowe dodane do rejestracji określa domenę _domyślną_ lub _główną_. Aby dodać konto służbowe do innej dzierżawy, należy zmienić poziom uwierzytelniania w obszarze Rejestracja na potrzeby uwierzytelniania między dzierżawami.

Aby zaktualizować poziom uwierzytelniania:

1. Zaloguj się do portalu EA platformy Azure jako administrator przedsiębiorstwa.
2. Kliknij pozycję **Zarządzaj zespołem** na panelu nawigacyjnym po lewej stronie.
3. Kliknij kartę **Rejestracja**.
4. W obszarze **Szczegóły rejestracji** wybierz pozycję **Poziom uwierzytelniania**.
5. Kliknij symbol ołówka.
6. Kliknij pozycję **Zapisz**.

![Przykład pokazujący poziomy uwierzytelniania ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Konta Microsoft muszą mieć skojarzony identyfikator utworzony pod adresem [https://signup.live.com](https://signup.live.com/).

Konta służbowe są dostępne dla organizacji, które skonfigurowały usługę Active Directory Federation i mają wszystkie konta w jednej dzierżawie. Użytkowników można dodawać przy użyciu uwierzytelniania użytkowników federacyjnych w firmie lub szkole, jeśli wewnętrzna usługa Active Directory firmy jest federacyjna.

Jeśli Twoja organizacja nie korzysta z federacji Active Directory, nie możesz użyć służbowego adresu e-mail. Zamiast tego zarejestruj się lub utwórz nowy adres e-mail i zarejestruj go jako konto Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Nie można uzyskać dostępu do portalu EA platformy Azure

Jeśli podczas próby zalogowania się do portalu EA platformy Azure zostanie wyświetlony komunikat o błędzie, wykonaj następujące kroki rozwiązywania problemów:

- Upewnij się, że używasz poprawnego adresu URL portalu EA platformy Azure, czyli https://ea.azure.com.
- Ustal, czy dostęp do portalu EA platformy Azure był dodany jako konto służbowe, czy jako konto Microsoft.
  - Jeśli używasz konta służbowego, wprowadź służbowy adres e-mail i hasło służbowe. Twoje hasło służbowe jest udostępniane przez Twoją organizację. W razie problemów z hasłem możesz skontaktować się z działem IT, aby dowiedzieć się, jak je zresetować.
  - Jeśli korzystasz z konta Microsoft, wprowadź adres e-mail i hasło do konta Microsoft. Jeśli nie pamiętasz hasła do konta Microsoft, możesz je zresetować pod adresem [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Aby się zalogować, użyj sesji przeglądarki w trybie prywatnym lub incognito, tak aby nie były przechowywane żadne pliki cookie ani buforowane informacje z poprzednich lub istniejących sesji. Wyczyść pamięć podręczną przeglądarki i otwórz okno https://ea.azure.com w trybie prywatnym lub incognito.
- Jeśli podczas korzystania z konta Microsoft zostanie wyświetlony komunikat o błędzie _Nieprawidłowy użytkownik_, przyczyną może być posiadanie wielu kont Microsoft. Konto, za pomocą którego próbowano się zalogować, nie jest podstawowym adresem e-mail.
Komunikat _Nieprawidłowy użytkownik_ może być też spowodowany użyciem błędnego typu konta podczas dodawania użytkownika do rejestracji. Na przykład użyto konta służbowego, a nie konta Microsoft. W tym przykładzie inny administrator EA musi dodać prawidłowe konto. W przeciwnym razie należy się skontaktować z [pomocą techniczną](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Jeśli musisz sprawdzić alias podstawowy, przejdź do strony [https://account.live.com](https://account.live.com). Następnie kliknij **swoje informacje**, a potem pozycję **Zarządzaj logowaniem do firmy Microsoft**. Postępuj zgodnie z monitami, aby zweryfikować alternatywny adres e-mail i uzyskać kod umożliwiający dostęp do poufnych informacji. Wprowadź kod zabezpieczeń. Jeśli nie chcesz konfigurować uwierzytelniania dwuskładnikowego, wybierz opcję **Skonfiguruj je później**.
  - Zostanie wyświetlona strona **Zarządzanie logowaniem do firmy Microsoft**, na której możesz zobaczyć swoje aliasy kont. Sprawdź, czy do logowania się do portalu EA platformy Azure jest używany alias podstawowy. Jeśli nie, możesz go ustawić jako alias podstawowy. Możesz też użyć zamiast niego podstawowego aliasu dla portalu EA platformy Azure.

## <a name="no-activation-email-received"></a>Nie odebrano wiadomości e-mail dotyczącej aktywacji

Wiadomość e-mail dotycząca aktywacji z portalu EA platformy Azure jest wysyłana z adresu *waep@microsoft.com* . Jeśli nie otrzymasz takiej wiadomości, sprawdź folder spamu lub wiadomości-śmieci. Temat wiadomości to _Zaproszenie do wyświetlenia usługi Microsoft Azure i zarządzania nią_. Wiadomość jest wysyłana do każdego nowo dodanego administratora EA.

Jeśli masz pewność, że skonfigurowano Cię jako administratora EA, nie musisz czekać na otrzymanie wiadomości e-mail dotyczącej aktywacji w celu zalogowania się do portalu EA platformy Azure. Zamiast tego możesz przejść do strony https://ea.azure.com i zalogować się przy użyciu adresu e-mail (służbowego, szkolnego lub konta Microsoft) i hasła.

## <a name="azure-ea-activation-faq"></a>Aktywacja rejestracji Azure EA — często zadawane pytania

W tej sekcji artykułu przedstawiono rozwiązania typowych problemów związanych z aktywacją rejestracji Azure EA.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Chcę dodać nowego administratora EA do mojej rejestracji

Nowego administratora przedsiębiorstwa mogą dodać istniejący administratorzy przedsiębiorstwa. Jeśli jesteś administratorem EA, zaloguj się do witryny EA Portal > kliknij pozycję **Zarządzanie** > **+ Dodaj administratora** w prawym górnym rogu, aby dodać nowego administratora EA. Aby dodawać użytkowników, upewnij się, że masz ich adres e-mail i preferowaną metodę logowania, na przykład za pośrednictwem uwierzytelniania za pomocą konta służbowego bądź identyfikatora Microsoft Live ID.

Jeśli nie jesteś administratorem EA, skontaktuj się z administratorami EA w firmie, aby poprosić o dodanie Cię do rejestracji. Po dodaniu Cię do rejestracji otrzymasz wiadomość e-mail umożliwiającą aktywację.

Jeśli jednak administratorzy EA nie mogą Ci pomóc, możemy dodać Twoje konto w ich imieniu, jeśli podasz nam następujące informacje:
- numer rejestracji,
- adres e-mail do dodania oraz typ uwierzytelniania (konto służbowe/konto Microsoft),
- zatwierdzenie w wiadomości e-mail od administratora EA.

Po uzyskaniu wszystkich wymaganych informacji prześlij żądanie na adres [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Chcę zaktualizować pierwszego administratora EA w rejestracji

Pierwszego administratora EA można zaktualizować w centrum usługi licencjonowania zbiorowego, aktualizując informacje kontaktowe oraz administratora online w portalu. Aktualizacja portalu EA zajmie około 24 godzin. Po jego zaktualizowaniu nowy administrator EA otrzyma wiadomość e-mail dotyczącą aktywacji.

Jeśli nie masz dostępu do portalu VLSC lub jeśli początkowy administrator EA nie może już zarządzać rejestracją i nie ma dostępu do portalu EA, prześlij żądanie na adres [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport), aby poprosić o aktualizację. Podaj następujące informacje:
- Numer rejestracji
- Adres e-mail do dodania oraz typ uwierzytelniania (konto służbowe/konto Microsoft)
- Przyczyna zmiany początkowego administratora EA
- Zatwierdzenie w wiadomości e-mail od początkowego administratora EA

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Obecny administrator EA nie pracuje już w naszej firmie

Rejestracja EA może mieć wielu administratorów EA. Można skontaktować się z innym administratorem EA w celu dodania nowego administratora EA/właściciela konta/administratora działu. Jeśli jednak nie masz pewności, kto jest administratorem EA w Twojej firmie, lub dla danej rejestracji nie ma innego dostępnego administratora EA, skontaktuj się z nami, podając następujące informacje:
- Numer rejestracji
- Adres e-mail do dodania oraz typ uwierzytelniania (konto służbowe/konto Microsoft)
- Informacja o tym, że obecny administrator EA nie pracuje już w naszej firmie

Pamiętaj, że jeśli dana rejestracja obejmuje innych administratorów EA, skontaktujemy się z nimi i poprosimy o zatwierdzenie zmian administracyjnych w rejestracji.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Moja rejestracja jest wyświetlana jako oczekująca. Jak mogę aktywować moją rejestrację?

Rejestracje będą oczekujące, jeśli początkowy administrator EA nie zalogował się jeszcze do rejestracji. Jeśli jesteś administratorem EA, zaloguj się do witryny Azure EA Portal. Oczekująca rejestracja może być niewidoczna na stronie docelowej ze wszystkimi numerami rejestracji. Usuń zaznaczenie pola „aktywne” w prawym górnym rogu witryny EA Portal. Ta akcja spowoduje wyświetlenie oczekującej rejestracji. Kliknij rejestrację, aby uzyskać dostęp do informacji, a po przejściu do strony Zarządzanie w rejestracji jej stan zostanie zaktualizowany z „oczekująca” na „aktywna”.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Dlaczego moje konto jest zablokowane w stanie „oczekujące”?

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji, ich stan jest wyświetlany jako „oczekujące”. Po otrzymaniu powitalnej wiadomości e-mail z informacjami o aktywacji właściciel konta może się zalogować, aby aktywować swoje konto. Zalogowanie spowoduje zaktualizowanie stanu konta z „oczekujące” na „aktywne”.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Wystąpił błąd podczas logowania się do witryny Azure EA Portal

Istnieje kilka możliwych przyczyn występowania błędów po zalogowaniu się w witrynie Azure EA Portal. Wykonaj następujące kroki rozwiązywania problemów:

 1. Upewnij się, że używasz poprawnego adresu URL witryny EA Portal: [https://ea.azure.com](https://ea.azure.com).
 1. Ustal, czy dostęp do witryny Azure EA Portal był dodany jako konto służbowe, czy jako identyfikator Microsoft Live ID: Jeśli używasz konta służbowego, wprowadź służbowy adres e-mail i hasło. Jeśli używasz identyfikatora Microsoft Live ID, wprowadź adres e-mail i hasło usługi Microsoft Live ID. Jeśli nie pamiętasz hasła usługi Microsoft Live ID, zresetuj je pod adresem [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Zaleca się logowanie za pomocą prywatnej przeglądarki, aby nie były zachowywane żadne pliki cookie ani pamięć podręczna z poprzednich/istniejących sesji. Wyczyść pamięć podręczną i użyj trybu przeglądania prywatnego/okna incognito, aby otworzyć witrynę [https://ea.azure.com](https://ea.azure.com).
 1. Jeśli podczas korzystania z konta Microsoft wystąpi błąd nieprawidłowego użytkownika, może to być spowodowane tym, że masz wiele kont Microsoft, a konto, za pomocą którego próbujesz się zalogować, nie jest aliasem podstawowym. Aby sprawdzić alias podstawowy, przejdź do strony account.live.com:
    - Przejdź do pozycji „Twoje informacje” > „Zarządzanie adresem e-mail lub numerem telefonu”.
    - Postępuj zgodnie z monitami na ekranie, aby zweryfikować alternatywny adres e-mail i uzyskać kod umożliwiający dostęp do poufnych informacji.
    - Wprowadź kod zabezpieczeń.
    - Jeśli wolisz skonfigurować uwierzytelnianie dwuetapowe później, wybierz pozycję „Konfiguruj później”.
    - Zostanie wyświetlona strona „Zarządzanie aliasami konta”, na której będą widoczne Twoje aliasy konta. Sprawdź dokładnie, czy do logowania się do witryny Azure EA Portal jest używany alias podstawowy. Jeśli tak nie jest, możesz go ustawić jako swój alias podstawowy lub użyć zamiast niego aliasu podstawowego dla witryny EA Portal.

Jeśli powyższe kroki rozwiązywania problemów nie powiodły się, prześlij żądanie na adres [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport), podając następujące informacje:
- Używana przeglądarka i jej wersja.
- Zrzut ekranu przedstawiający komunikat o błędzie.
- Adres URL strony wyświetlającej błąd.  
- Data, godzina i strefa czasowa wystąpienia błędu.
- Ponadto pomocne będzie uzyskanie pliku dziennika. Poniżej przedstawiono procedurę przechwytywania śledzenia sieci:
  1. Otwórz program Internet Explorer.
  1. Naciśnij klawisz F12, który spowoduje otwarcie pola w dolnej części ekranu programu IE.
  1. Wybierz kartę **Sieć**.
  1. Kliknij pozycję **Rozpocznij przechwytywanie**.
  1. Wykonaj akcję, która powoduje błąd.
  1. Po wystąpieniu błędu kliknij pozycję **Zatrzymaj przechwytywanie**.
  1. Zapisz plik i dołącz informacje we wniosku o pomoc techniczną.
  1. Pamiętaj, aby we wniosku o pomoc techniczną podać numer rejestracji oraz adres e-mail.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Jaka jest różnica między kontem służbowym a kontem Microsoft?

**Konto Microsoft:** Konta, które zostały skojarzone z identyfikatorem Live ID na stronie [https://signup.live.com](https://signup.live.com).

**Konto służbowe:** Dostępne tylko dla firm, które skonfigurowały usługę Active Directory z federacją z chmurą i mają wszystkie konta w jednej dzierżawie. Użytkowników można dodawać przy użyciu autoryzacji za pomocą konta służbowego, jeśli wewnętrzna usługa Active Directory firmy jest sfederowana z chmurą.

  Od września 2016 r. firma Microsoft nie zezwala już na rejestrację służbowego adresu e-mail jako konta Microsoft. Aby uzyskać więcej informacji, zapoznaj się z następującymi materiałami: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Jeśli Twoja organizacja nie jest sfederowana z chmurą, nie będziesz w stanie używać służbowego adresu e-mail. Zamiast tego zarejestruj się lub utwórz nowy adres e-mail i zarejestruj go jako konto Microsoft.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Nie pamiętam hasła do witryny Azure EA Portal

Jeśli nie pamiętasz hasła usługi Microsoft Live ID, zresetuj je pod adresem [https://account.live.com/password/reset](https://account.live.com/password/reset).

Jeśli nie pamiętasz hasła służbowego, skontaktuj się z administratorem IT w Twojej firmie.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Mam prawidłowe konto służbowe, ale nie mogę go dodać do witryny EA Portal

Jeśli masz konto służbowe w innej dzierżawie, zmień poziom autoryzacji na stronie szczegółów rejestracji na „Konto służbowe między dzierżawami”, aby umożliwić dodanie tego konta.

## <a name="next-steps"></a>Następne kroki

- Administratorzy portalu EA platformy Azure powinni przeczytać artykuł [Administracja portalu Azure EA](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
