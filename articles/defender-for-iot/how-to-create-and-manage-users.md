---
title: Tworzenie użytkowników i zarządzanie nimi
description: Twórz i Zarządzaj użytkownikami czujników oraz lokalną konsolę zarządzania. Użytkownikom można przypisać rolę Administrator, analityk zabezpieczeń lub użytkownika tylko do odczytu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/03/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: dff379c99fa7383c7f7844cf8d195a345e88a335
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466273"
---
# <a name="about-defender-for-iot-console-users"></a>Informacje o usłudze Defender dla użytkowników konsoli IoT

W tym artykule opisano sposób tworzenia i zarządzania użytkownikami czujników oraz lokalnej konsoli zarządzania. Role użytkowników obejmują administratorów, analityków zabezpieczeń lub użytkowników tylko do odczytu. Każda rola jest skojarzona z zakresem uprawnień do narzędzi dla czujnika lub lokalnej konsoli zarządzania. Role są zaprojektowane w celu ułatwienia szczegółowego, bezpiecznego dostępu do usługi Azure Defender dla IoT.

Funkcje są również dostępne do śledzenia aktywności użytkownika i włączania logowania Active Directory.

Domyślnie każdy czujnik i lokalna Konsola zarządzania są instalowane z użytkownikiem *cyberx i pomocą techniczną* . Ci użytkownicy mają dostęp do zaawansowanych narzędzi do rozwiązywania problemów i instalacji. Użytkownicy będący administratorami powinni zalogować się przy użyciu tych poświadczeń użytkownika, utworzyć użytkownika administracyjnego, a następnie utworzyć dodatkowych użytkowników dla analityków zabezpieczeń i użytkowników tylko do odczytu.

## <a name="role-based-permissions"></a>Uprawnienia oparte na rolach
Dostępne są następujące role użytkowników:

- **Tylko do odczytu**: Użytkownicy tylko do odczytu wykonują zadania, takie jak wyświetlanie alertów i urządzeń na mapie urządzeń. Ci użytkownicy mają dostęp do opcji wyświetlanych w obszarze **nawigacji**.

- **Analityk zabezpieczeń**: analitycy zabezpieczeń mają uprawnienia użytkownika tylko do odczytu. Mogą również wykonywać działania na urządzeniach, potwierdzać alerty i korzystać z narzędzi do badania. Ci użytkownicy mają dostęp do opcji wyświetlanych w obszarze **Nawigacja** i **Analiza**.

- **Administrator**: Administratorzy mają dostęp do wszystkich narzędzi, w tym definiowania konfiguracji systemu, tworzenia użytkowników i zarządzania nimi. Ci użytkownicy mają dostęp do opcji wyświetlanych w obszarze **nawigowanie**, **Analizowanie** i **administrowanie**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Uprawnienia oparte na rolach dla narzędzi lokalnej konsoli zarządzania

W tej sekcji opisano uprawnienia dostępne dla administratorów, analityków zabezpieczeń i użytkowników tylko do odczytu w lokalnej konsoli zarządzania.  

| Uprawnienie | Tylko do odczytu | Analityk zabezpieczeń | Administrator |
|--|--|--|--|
| Wyświetlanie i filtrowanie mapy przedsiębiorstwa | ✓ | ✓ | ✓ |
| Kompiluj lokację |  |  | ✓ |
| Zarządzanie lokacją (Dodawanie i edytowanie stref) |  |  | ✓ |
| Wyświetlanie i filtrowanie spisu urządzeń | ✓ | ✓ | ✓ |
| Wyświetlanie alertów i zarządzanie nimi: Potwierdzanie, uczenie i Przypinanie | ✓ | ✓ | ✓ |
| Generowanie raportów |  | ✓ | ✓ |
| Wyświetlanie raportów oceny ryzyka |  | ✓ | ✓ |
| Ustawianie wykluczeń alertów |  | ✓ | ✓ |
| Wyświetlanie lub definiowanie grup dostępu |  |  | ✓ |
| Zarządzanie ustawieniami systemu |  |  | ✓ |
| Zarządzanie użytkownikami |  |  | ✓ |
| Wyślij dane alertu do partnerów |  |  | ✓ |
| Zarządzanie certyfikatami |  |  | ✓ |
| Limit czasu sesji, gdy użytkownicy nie są aktywni | 30 minut | 30 minut  | 30 minut  |

#### <a name="assign-users-to-access-groups"></a>Przypisywanie użytkowników do grup dostępu

Administratorzy mogą ulepszyć kontrolę dostępu użytkowników w usłudze Defender dla IoT, przypisując użytkowników do określonych *grup dostępu*. Grupy dostępu są przypisywane do stref, lokacji, regionów i jednostek pracy, w których znajduje się czujnik. Przypisując użytkownikom dostęp do grup, administratorzy uzyskują szczególną kontrolę nad tym, gdzie użytkownicy zarządzają wykrywaniem urządzeń i analizują je. 

Praca w ten sposób dotyczy dużych organizacji, w których uprawnienia użytkowników mogą być złożone lub określane przez globalne zasady zabezpieczeń organizacji. Aby uzyskać więcej informacji, zobacz [Definiowanie globalnej kontroli dostępu](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Uprawnienia oparte na rolach dla narzędzi czujników

W tej sekcji opisano uprawnienia dostępne dla administratorów czujnika, analityków zabezpieczeń i użytkowników tylko do odczytu.  

| Uprawnienie | Tylko do odczytu | Analityk zabezpieczeń | Administrator |
|--|--|--|--|
| Wyświetlanie pulpitu nawigacyjnego | ✓ | ✓ | ✓ |
| Widoki powiększenia mapy formantów |  |  | ✓ |
| Wyświetlanie alertów | ✓ | ✓ | ✓ |
| Zarządzanie alertami: potwierdzenie, nauka i numer PIN |  | ✓ | ✓ |
| Wyświetlanie zdarzeń na osi czasu |  | ✓ | ✓ |
| Autoryzuj urządzenia, znane urządzenia skanujące, urządzenia programistyczne |  | ✓ | ✓ |
| Wyświetl dane badania | ✓ | ✓ | ✓ |
| Zarządzanie ustawieniami systemu |  |  | ✓ |
| Zarządzanie użytkownikami |  |  | ✓ |
| Serwery DNS do wyszukiwania wstecznego |  |  | ✓ |
| Wyślij dane alertu do partnerów |  | ✓ | ✓ |
| Utwórz Komentarze alertu |  | ✓ | ✓ |
| Wyświetl historię zmian programistycznych | ✓ | ✓ | ✓ |
| Utwórz niestandardowe reguły alertów |  | ✓ | ✓ |
| Zarządzaj wieloma powiadomieniami jednocześnie |  | ✓ | ✓ |
| Zarządzanie certyfikatami |  |  | ✓ |
| Limit czasu sesji, gdy użytkownicy nie są aktywni | 30 minut | 30 minut | 30 minut |

## <a name="define-users"></a>Definiowanie użytkowników

W tej sekcji opisano sposób definiowania użytkowników. Użytkownicy Cyberx, pomocy technicznej i administratorów mogą dodawać, usuwać i aktualizować definicje innych użytkowników.

Aby zdefiniować użytkownika:

1. W okienku po lewej stronie czujnika lub lokalnej konsoli zarządzania wybierz pozycję **Użytkownicy**.
1. W oknie **Użytkownicy** wybierz pozycję **Utwórz użytkownika**.
1. W okienku **Tworzenie użytkownika** Zdefiniuj następujące parametry:

   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika.
   - **Poczta e-mail**: wprowadź adres e-mail użytkownika.
   - **Imię**: Wprowadź imię użytkownika.
   - **Nazwisko**: Wprowadź nazwisko użytkownika.
   - **Rola**: Zdefiniuj rolę użytkownika. Zobacz [uprawnienia oparte na rolach](#role-based-permissions).
   - **Grupa dostępu**: Jeśli tworzysz użytkownika dla lokalnej konsoli zarządzania, zdefiniuj grupę dostępu użytkownika. Zobacz [Definiowanie globalnej kontroli dostępu](how-to-define-global-user-access-control.md).
   - **Hasło**: Wybierz typ użytkownika w następujący sposób:
     - **Użytkownik lokalny**: Zdefiniuj hasło dla użytkownika czujnika lub lokalnej konsoli zarządzania. Hasło musi zawierać co najmniej sześć znaków i musi zawierać litery i cyfry.
     - **Active Directory użytkownika**: możesz zezwolić użytkownikom na logowanie się do czujnika lub konsoli zarządzania przy użyciu poświadczeń Active Directory. Zdefiniowane grupy Active Directory mogą być skojarzone z określonymi poziomami uprawnień. Na przykład skonfiguruj konkretną grupę Active Directory i przypisz wszystkich użytkowników w grupie do typu użytkownika tylko do odczytu.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Zarządzanie użytkownikami.":::

## <a name="user-session-timeout"></a>Limit czasu sesji użytkownika

Jeśli użytkownicy nie są aktywni przy użyciu klawiatury lub myszy przez określony czas, są wylogowani z sesji i muszą zalogować się ponownie.

Gdy użytkownicy nie pracowały za pomocą myszy lub klawiatury konsoli przez okres 30 minut, zostanie wymuszone wylogowanie sesji.

Ta funkcja jest domyślnie włączona i po uaktualnieniu, ale można ją wyłączyć. Ponadto można zaktualizować czasy zliczania sesji. Czasy sesji są zdefiniowane w sekundach. Definicje są stosowane w odniesieniu do czujnika i lokalnej konsoli zarządzania.

Po upływie limitu czasu nieaktywności pojawia się komunikat przekroczenie limitu czasu sesji.

### <a name="control-inactivity-sign-out"></a>Rejestrowanie braku aktywności kontroli

Użytkownicy administratorów mogą włączać i wyłączać wylogowanie poza aktywnością oraz dostosowywać progi nieaktywności.

Aby uzyskać dostęp do polecenia:

1. Zaloguj się do interfejsu wiersza polecenia dla czujnika lub lokalnej konsoli zarządzania przy użyciu usługi Defender dla poświadczeń administracyjnych IoT.

1. Wprowadź `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Aby wyłączyć tę funkcję, przejdź `infinity_session_expiration = true` do `infinity_session_expiration = false` .

Aby zaktualizować okresy zliczania wylogowania, Dostosuj `= <number>` wartość do wymaganego czasu.

## <a name="track-user-activity"></a>Śledzenie aktywności użytkownika 

Aktywność użytkownika można śledzić na osi czasu zdarzeń na każdym czujniku. Na osi czasu są wyświetlane zdarzenia lub urządzenia, których dotyczy, oraz godzina i Data przeprowadzenia działania przez użytkownika.

Aby wyświetlić aktywność użytkownika:

1. Zaloguj się do czujnika.
1. Na osi czasu zdarzeń Włącz opcję **operacje użytkownika** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Wyświetl aktywność użytkownika.":::

## <a name="integrate-with-active-directory-servers"></a>Integracja z serwerami Active Directory 

Skonfiguruj czujnik lub lokalną konsolę zarządzania do pracy z Active Directory. Pozwala to Active Directory użytkownikom na dostęp do konsoli usługi Defender dla usługi IoT przy użyciu poświadczeń Active Directory.

Obsługiwane są dwa typy uwierzytelniania opartego na protokole LDAP:

- **Pełne uwierzytelnianie**: szczegóły użytkownika są pobierane z serwera LDAP. Przykłady to imię i nazwisko, nazwisko, adres e-mail i uprawnienia użytkownika.

- **Zaufany użytkownik**: pobierane jest tylko hasło użytkownika. Inne pobierane szczegóły użytkownika są oparte na użytkownikach zdefiniowanych w czujniku.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Uprawnienia Active Directory i Defender dla usługi IoT

Można kojarzyć grupy Active Directory zdefiniowane w tym miejscu z określonymi poziomami uprawnień. Na przykład skonfiguruj konkretną grupę Active Directory i przypisz uprawnienia tylko do odczytu dla wszystkich użytkowników w grupie.

Aby skonfigurować Active Directory:

1. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Wyświetl ustawienia systemu Active Directory.":::

2. W okienku **Ustawienia systemu** wybierz pozycję **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Edytuj konfiguracje Active Directory.":::

3. W oknie dialogowym **Edytowanie konfiguracji Active Directory** wybierz pozycję **Active Directory Integracja włączona**  >  **Zapisz**. Zostanie rozwinięte okno dialogowe **Edytowanie konfiguracji Active Directory** i można teraz wprowadzić parametry w celu skonfigurowania Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Wprowadź parametry, aby skonfigurować Active Directory.":::

    > [!NOTE]
    > - Należy zdefiniować tutaj parametry LDAP dokładnie tak, jak pojawiają się one w Active Directory.
    > - Dla wszystkich parametrów Active Directory użyj tylko małych liter. Używaj małych liter, nawet jeśli konfiguracje w Active Directory używają wielkich liter.
    > - Nie można skonfigurować zarówno protokołu LDAP, jak i LDAPs dla tej samej domeny. Można jednak używać obu jednocześnie dla różnych domen.

4. Ustaw parametry serwera Active Directory w następujący sposób:

   | Parametr serwera | Opis |
   |--|--|
   | Nazwa FQDN kontrolera domeny | Ustaw w pełni kwalifikowaną nazwę domeny (FQDN) dokładnie tak, jak pojawia się na serwerze LDAP. Na przykład wprowadź `host1.subdomain.domain.com`. |
   | Port kontrolera domeny | Zdefiniuj port, na którym jest skonfigurowany protokół LDAP. |
   | Domena podstawowa | Ustaw nazwę domeny (na przykład `subdomain.domain.com` ) i typ połączenia zgodnie z konfiguracją LDAP. |
   | Grupy Active Directory | Wprowadź nazwy grup, które są zdefiniowane w konfiguracji Active Directory na serwerze LDAP. |
   | Domeny zaufane | Aby dodać domenę zaufaną, należy dodać nazwę domeny i typ połączenia zaufanej domeny. <br />Domeny zaufane można skonfigurować tylko dla użytkowników, którzy zostali zdefiniowani w obszarze Użytkownicy. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Grupy ActiveDirectory dla lokalnej konsoli zarządzania

W przypadku tworzenia grup Active Directory dla użytkowników lokalnej konsoli zarządzania należy utworzyć regułę grupy dostępu dla każdej grupy Active Directory. Lokalna Konsola zarządzania Active Directory poświadczenia nie będą działały, jeśli dla grupy użytkowników Active Directory nie istnieje reguła grupy dostępu. Zobacz [Definiowanie globalnej kontroli dostępu](how-to-define-global-user-access-control.md).

1. Wybierz pozycję **Zapisz**.

2. Aby dodać zaufany serwer, wybierz pozycję **Dodaj serwer** i skonfiguruj inny serwer.

## <a name="resetting-passwords"></a>Resetowanie haseł

### <a name="cyberx-or-support-user"></a>Użytkownik CyberX lub pomoc techniczna

Tylko użytkownik **CyberX** i **Pomoc techniczna** mają dostęp do funkcji **odzyskiwania hasła** . Jeśli użytkownik **CyberX** lub **pomocy technicznej** nie pamięta swojego hasła, może zresetować hasło za pomocą opcji **odzyskiwania hasła** na stronie logowania usługi Defender for IoT.

Aby zresetować hasło dla użytkownika CyberX lub pomocy technicznej:

1. Na ekranie logowania do usługi Defender for IoT wybierz pozycję  **Odzyskiwanie hasła**. Zostanie otwarty ekran **Odzyskiwanie hasła** .

1. Wybierz opcję **CyberX** lub **support** i skopiuj unikatowy identyfikator.

1. Przejdź do Azure Portal i wybierz pozycję **Lokacje i czujniki**.  

1. Wybierz ikonę **Filtr subskrypcji** na :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  górnym pasku narzędzi i wybierz subskrypcję, z którą jest połączony czujnik.

1. Wybierz kartę **odzyskiwanie na lokalnym komputerze konsoli zarządzania** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Wybierz przycisk Odzyskaj lokalne zarządzanie, aby pobrać plik odzyskiwania.":::

1. Wprowadź unikatowy identyfikator otrzymany na ekranie **odzyskiwania hasła** i wybierz polecenie **Odzyskaj**. `password_recovery.zip`Plik zostanie pobrany.

    > [!NOTE]
    > Nie zmieniaj pliku odzyskiwania hasła. Jest to podpisany plik i nie będzie działał w przypadku naruszenia go.

1. Na ekranie **Odzyskiwanie hasła** wybierz pozycję **Przekaż**. Zostanie otwarte okno **odzyskiwanie pliku odzyskiwania hasła** .

1. Wybierz pozycję **Przeglądaj** , aby zlokalizować `password_recovery.zip` plik, lub przeciągnij `password_recovery.zip` do okna.

    > [!NOTE]
    > Może pojawić się komunikat o błędzie wskazujący, że plik jest nieprawidłowy. Aby naprawić ten komunikat o błędzie, upewnij się, że wybrano odpowiednią subskrypcję przed pobraniem `password_recovery.zip` i pobraniem.  

1. Po wybraniu opcji **dalej** zostanie wyświetlone hasło użytkownika i wygenerowanego przez system hasła do konsoli zarządzania.

### <a name="administrator-security-analyst-and-read-only-user"></a>Administrator, analityk zabezpieczeń i użytkownik tylko do odczytu

Analitycy tylko do odczytu i zabezpieczenia nie mogą resetować własnego hasła i muszą kontaktować się z użytkownikiem przy użyciu ról Administrator, pomoc techniczna lub CyberX, aby zresetować swoje hasło. Użytkownik administrator musi skontaktować się z użytkownikiem **CyberX** lub **pomocą techniczną** w celu zresetowania hasła.

Aby zresetować hasło użytkownika do czujnika:

1. Administrator, pomoc techniczna lub użytkownik roli CyberX powinni zalogować się do czujnika.

1. Wybierz pozycję **Użytkownicy** w panelu po lewej stronie.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Wybierz opcję użytkownika z okienka po lewej stronie.":::

1. Znajdź użytkownika i wybierz opcję **Edytuj** z menu rozwijanego **Akcje** .

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Wybierz pozycję Edytuj z menu rozwijanego akcje.":::

1. Wprowadź nowe hasło w polach **nowe hasło** i **Potwierdź nowe hasło** .

1. Wybierz pozycję **Aktualizuj**.

Aby zresetować hasło użytkownika w lokalnej konsoli zarządzania:

1. Administrator, pomoc techniczna lub użytkownik roli CyberX powinni zalogować się do czujnika.

1. Wybierz pozycję **Użytkownicy** w panelu po lewej stronie.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Na panelu po lewej stronie wybierz opcję użytkownika.":::

1. Znajdź użytkownika i wybierz ikonę edycji :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Wprowadź nowe hasło w polach **nowe hasło** i **Potwierdź nowe hasło** .

1. Wybierz pozycję **Aktualizuj**.

## <a name="see-also"></a>Zobacz też

[Aktywowanie i Konfigurowanie czujnika](how-to-activate-and-set-up-your-sensor.md) 
 [Aktywuj i skonfiguruj lokalną konsolę zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 [Śledź działanie czujnika](how-to-track-sensor-activity.md)
