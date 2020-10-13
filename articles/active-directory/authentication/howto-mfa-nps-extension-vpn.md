---
title: Sieć VPN z usługą Azure MFA przy użyciu rozszerzenia serwera NPS Azure Active Directory
description: Zintegruj infrastrukturę sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7243857db9a3726bb42815ac4c9eef661f52e47
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964727"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrowanie infrastruktury sieci VPN z usługą Azure MFA przy użyciu rozszerzenia serwera zasad sieciowych dla platformy Azure

Rozszerzenie serwera zasad sieciowych (NPS) dla systemu Azure umożliwia organizacjom ochronę Usługa telefonujących użytkowników zdalnego uwierzytelniania (RADIUS) uwierzytelniania klientów przy użyciu usługi Azure Multi-Factor Authentication opartej na chmurze [(MFA)](howto-mfaserver-nps-rdg.md), która oferuje dwuetapową weryfikację.

Ten artykuł zawiera instrukcje dotyczące integrowania infrastruktury NPS z usługą MFA przy użyciu rozszerzenia serwera NPS dla platformy Azure. Ten proces umożliwia jednoetapową weryfikację dla użytkowników próbujących nawiązać połączenie z siecią przy użyciu sieci VPN.

Usługi zasad sieciowych i dostępu sieciowego umożliwiają organizacjom:

* Przypisz centralną lokalizację zarządzania i kontroli żądań sieci, aby określić:

  * Kto może nawiązać połączenie

  * Jakie pory dnia są dozwolone

  * Czas trwania połączeń

  * Poziom zabezpieczeń, których klienci muszą używać do nawiązywania połączeń

    Zamiast określać zasady na każdym serwerze sieci VPN lub Pulpit zdalny bramy, zrób to, gdy znajdują się w centralnej lokalizacji. Protokół RADIUS służy do zapewniania scentralizowanego uwierzytelniania, autoryzacji i ewidencjonowania aktywności (AAA).

* Ustanów i Wymuszaj zasady kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia mają nieograniczony lub ograniczony dostęp do zasobów sieciowych.

* Umożliwiają wymuszenie uwierzytelniania i autoryzacji dostępu do punktów dostępu bezprzewodowego i przełączników sieci 802.1 z obsługą x.
  Aby uzyskać więcej informacji, zobacz [serwer zasad sieciowych](/windows-server/networking/technologies/nps/nps-top).

Aby zwiększyć bezpieczeństwo i zapewnić wysoki poziom zgodności, organizacje mogą zintegrować serwer zasad sieciowych z platformą Azure Multi-Factor Authentication w celu zapewnienia, że użytkownicy będą korzystać z weryfikacji dwuetapowej w celu nawiązania połączenia z portem wirtualnym na serwerze sieci VPN. Aby użytkownicy mieli dostęp, muszą podać kombinację nazwy użytkownika i hasła oraz inne informacje, które kontrolują. Te informacje muszą być zaufane i nie można ich łatwo duplikować. Może zawierać numer telefonu komórkowego, numer stacjonarny lub aplikację na urządzeniu przenośnym.

Przed udostępnieniem rozszerzenia serwera NPS dla platformy Azure Klienci, którzy chcą zaimplementować dwuetapową weryfikację dla zintegrowanych środowisk NPS i usługi MFA, musieli skonfigurować i zachować osobny serwer usługi MFA w środowisku lokalnym. Ten typ uwierzytelniania jest oferowany przez Pulpit zdalny bramy i Serwer Multi-Factor Authentication platformy Azure przy użyciu usługi RADIUS.

Za pomocą rozszerzenia serwera NPS dla platformy Azure organizacje mogą zabezpieczać uwierzytelnianie klienta usługi RADIUS przez wdrożenie lokalnego rozwiązania MFA lub opartego na chmurze rozwiązania MFA.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Gdy użytkownicy łączą się z portem wirtualnym na serwerze sieci VPN, muszą najpierw uwierzytelniać się przy użyciu różnych protokołów. Protokoły umożliwiają użycie kombinacji nazw użytkowników i haseł oraz metod uwierzytelniania opartych na certyfikatach.

Oprócz uwierzytelniania i weryfikowania tożsamości użytkownicy muszą mieć odpowiednie uprawnienia do telefonowania. W przypadku prostych implementacji uprawnienia dostępu do połączeń telefonicznych, które zezwalają na dostęp są ustawiane bezpośrednio w Active Directory obiektów użytkownika.

![Karta telefon in w Active Directory właściwości użytkowników i komputerów](./media/howto-mfa-nps-extension-vpn/image1.png)

W przypadku prostych implementacji każdy serwer sieci VPN przyznaje lub odmawia dostępu na podstawie zasad, które są zdefiniowane na każdym lokalnym serwerze sieci VPN.

W większych i bardziej skalowalnych implementacjach zasady, które udzielają lub odmawiają dostępu do sieci VPN, są scentralizowane na serwerach RADIUS. W takich przypadkach serwer sieci VPN działa jako serwer dostępu (Klient RADIUS), który przekazuje żądania połączenia i komunikaty o kontach do serwera RADIUS. Aby nawiązać połączenie z portem wirtualnym na serwerze sieci VPN, użytkownicy muszą być uwierzytelnieni i spełniać warunki, które są zdefiniowane centralnie na serwerach RADIUS.

Po zintegrowaniu rozszerzenia serwera NPS dla systemu Azure z serwerem zasad sieciowych pomyślne wyniki przebiegu uwierzytelnienia są następujące:

1. Serwer sieci VPN odbiera żądanie uwierzytelnienia od użytkownika sieci VPN, który zawiera nazwę użytkownika i hasło w celu nawiązania połączenia z zasobem, na przykład sesji Pulpit zdalny.
2. Działając jako Klient RADIUS, serwer sieci VPN konwertuje żądanie na komunikat *żądania dostępu* usługi RADIUS i wysyła go (z zaszyfrowanym hasłem) do serwera RADIUS, na którym zainstalowano rozszerzenie serwera NPS.
3. Kombinacja nazwy użytkownika i hasła jest weryfikowana w Active Directory. Jeśli nazwa użytkownika lub hasło są nieprawidłowe, serwer RADIUS wyśle komunikat o *odmowie dostępu* .
4. Jeśli są spełnione wszystkie warunki określone w żądaniu połączenia serwera NPS i zasadach sieciowych (na przykład ograniczenia dotyczące czasu lub członkostwa w grupie), rozszerzenie serwera zasad sieciowych wyzwala żądanie uwierzytelniania pomocniczego za pomocą usługi Azure Multi-Factor Authentication.
5. Usługa Azure Multi-Factor Authentication komunikuje się z usługą Azure Active Directory, pobiera szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu metody skonfigurowanej przez użytkownika (połączenie telefoniczne komórkowego, wiadomość tekstowa lub aplikacja mobilna).
6. Po pomyślnym zakończeniu żądania MFA usługa Azure Multi-Factor Authentication przekazuje wynik do rozszerzenia serwera NPS.
7. Po uwierzytelnieniu i autoryzacji próby połączenia serwer zasad sieciowych, na którym zostało zainstalowane rozszerzenie, wysyła komunikat *akceptacji dostępu* usługi RADIUS do serwera sieci VPN (Klient RADIUS).
8. Użytkownik uzyskuje dostęp do portu wirtualnego na serwerze sieci VPN i ustanawia szyfrowany tunel VPN.

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja zawiera szczegółowe informacje o wymaganiach wstępnych, które należy wykonać przed integracją usługi MFA z siecią VPN. Przed rozpoczęciem należy spełnić następujące wymagania wstępne:

* Infrastruktura sieci VPN
* Rola usług zasad sieciowych i dostępu sieciowego
* Licencja usługi Azure Multi-Factor Authentication
* Oprogramowanie systemu Windows Server
* Biblioteki
* Azure Active Directory (Azure AD) synchronizowany z lokalnym Active Directory
* Identyfikator GUID Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruktura sieci VPN

W tym artykule przyjęto założenie, że masz działającą infrastrukturę sieci VPN, która korzysta z systemu Microsoft Windows Server 2016 i że serwer sieci VPN nie jest obecnie skonfigurowany do przesyłania dalej żądań połączeń do serwera RADIUS. W tym artykule opisano konfigurowanie infrastruktury sieci VPN do korzystania z centralnego serwera RADIUS.

Jeśli nie masz działającej infrastruktury sieci VPN, możesz ją szybko utworzyć, postępując zgodnie ze wskazówkami w wielu samouczkach konfiguracji sieci VPN, które można znaleźć w witrynach firmy Microsoft i innych firm.

### <a name="the-network-policy-and-access-services-role"></a>Rola Usługi zasad sieciowych i dostępu sieciowego

Usługi zasad sieciowych i dostępu sieciowego zapewniają funkcje serwera i klienta usługi RADIUS. W tym artykule przyjęto założenie, że zainstalowano rolę Usługi zasad sieciowych i dostępu sieciowego na serwerze członkowskim lub kontrolerze domeny w danym środowisku. W tym przewodniku skonfigurujesz usługi RADIUS do konfiguracji sieci VPN. Zainstaluj rolę Usługi zasad sieciowych i dostępu sieciowego na serwerze *innym niż* serwer sieci VPN.

Informacje o instalowaniu usługi roli usługi zasad sieciowych i dostępu sieciowego w systemie Windows Server 2012 lub nowszym można znaleźć w temacie [Instalowanie serwera zasad dotyczących kondycji ochrony dostępu do sieci](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Ochrona dostępu do sieci jest przestarzała w systemie Windows Server 2016. Aby zapoznać się z opisem najlepszych rozwiązań dotyczących serwera NPS, w tym zalecenia dotyczące instalowania serwera zasad sieciowych na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwerów zasad sieciowych](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-mfa-license"></a>Licencja usługi Azure MFA

Licencja jest wymagana w przypadku usługi Azure Multi-Factor Authentication i jest dostępna za pomocą Azure AD — wersja Premium, Enterprise Mobility + Security lub Multi-Factor Authentication autonomicznej licencji. Licencje na korzystanie z usługi Azure MFA, takie jak na użytkownika lub licencje uwierzytelniania, są niezgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [jak uzyskać Multi-Factor Authentication platformy Azure](concept-mfa-licensing.md). Do celów testowych możesz użyć subskrypcji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenie serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszego z zainstalowaną rolą usług zasad sieciowych i dostępu sieciowego. Wszystkie kroki przedstawione w tym przewodniku zostały wykonane z systemem Windows Server 2016.

### <a name="libraries"></a>Biblioteki

Następujące biblioteki są instalowane automatycznie z rozszerzeniem serwera NPS:

-    [Visual C++ pakiety redystrybucyjne dla Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Moduł Microsoft Azure Active Directory dla Windows PowerShell wersja 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Jeśli moduł Microsoft Azure Active Directory PowerShell jeszcze nie istnieje, jest instalowany ze skryptem konfiguracji, który jest uruchamiany w ramach procesu instalacji. Nie ma potrzeby instalowania modułu z wyprzedzeniem, jeśli nie jest jeszcze zainstalowany.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory zsynchronizowane z lokalnym Active Directory

Aby można było korzystać z rozszerzenia serwera NPS, użytkownicy lokalni muszą zostać zsynchronizowani z Azure Active Directory i włączony dla usługi MFA. W tym przewodniku przyjęto założenie, że użytkownicy lokalni są synchronizowani z Azure Active Directory za pośrednictwem Azure AD Connect. Instrukcje dotyczące włączania użytkowników w ramach usługi MFA są podane poniżej.

Aby uzyskać informacje na temat Azure AD Connect, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID Azure Active Directory

Aby zainstalować rozszerzenie serwera NPS, należy znać identyfikator GUID Azure Active Directory. Instrukcje dotyczące znajdowania identyfikatora GUID Azure Active Directory są podane w następnej sekcji.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurowanie usługi RADIUS dla połączeń sieci VPN

Jeśli rola serwera NPS została zainstalowana na serwerze członkowskim, należy ją skonfigurować do uwierzytelniania i autoryzacji klienta sieci VPN, który żąda połączeń sieci VPN. 

W tej sekcji założono, że zainstalowano rolę usług zasad sieciowych i dostępu sieciowego, ale nie skonfigurowano jej do użycia w Twojej infrastrukturze.

> [!NOTE]
> Jeśli masz już działający serwer sieci VPN, który używa scentralizowanego serwera RADIUS do uwierzytelniania, możesz pominąć tę sekcję.
>

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w Active Directory

Aby zapewnić prawidłowe działanie w tym scenariuszu, serwer NPS musi być zarejestrowany w Active Directory.

1. Otwórz Menedżera serwera.

2. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **serwer zasad sieciowych**.

3. W konsoli serwera zasad sieciowych kliknij prawym przyciskiem myszy serwer **NPS (lokalny)**, a następnie wybierz pozycję **zarejestruj serwer w Active Directory**. Wybierz dwa razy **przycisk OK** .

    ![Opcja menu Rejestruj serwer w Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Pozostaw otwartą konsolę, aby uzyskać następną procedurę.

### <a name="use-wizard-to-configure-the-radius-server"></a>Konfigurowanie serwera RADIUS przy użyciu kreatora

Aby skonfigurować serwer RADIUS, można użyć standardowej (opartej na kreatorze) lub opcji konfiguracji zaawansowanej. W tej sekcji założono, że używasz standardowej opcji konfiguracji opartej na kreatorze.

1. W konsoli serwera zasad sieciowych wybierz **serwer NPS (lokalny)**.

2. W obszarze **Konfiguracja standardowa**wybierz opcję **serwer RADIUS dla połączeń telefonicznych lub sieci VPN**, a następnie wybierz pozycję **Skonfiguruj sieć VPN lub połączenie telefoniczne**.

    ![Konfigurowanie serwera RADIUS na potrzeby połączeń telefonicznych lub sieci VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. W oknie **Wybieranie połączeń telefonicznych lub wirtualnych sieci prywatnych** wybierz opcję **połączenia wirtualnej sieci prywatnej**, a następnie wybierz przycisk **dalej**.

    ![Konfigurowanie połączeń wirtualnej sieci prywatnej](./media/howto-mfa-nps-extension-vpn/image4.png)

4. W oknie **Określanie serwera telefonicznego lub sieci VPN** wybierz pozycję **Dodaj**.

5. W oknie **Nowy klient usługi RADIUS** Podaj przyjazną nazwę, wprowadź rozpoznawalną nazwę lub adres IP serwera sieci VPN, a następnie wprowadź hasło wspólnego klucza tajnego. Wprowadź długie i złożone hasło wspólnego klucza tajnego. Zapisz go, ponieważ będzie potrzebny w następnej sekcji.

    ![Utwórz nowe okno klienta usługi RADIUS](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Wybierz przycisk **OK**, a następnie wybierz przycisk **dalej**.

7. W oknie **Konfigurowanie metod uwierzytelniania** Zaakceptuj wybór domyślny (**uwierzytelnianie szyfrowane firmy Microsoft w wersji 2 [MS-CHAPv2])** lub wybierz inną opcję, a następnie wybierz przycisk **dalej**.

    > [!NOTE]
    > W przypadku konfigurowania protokołu uwierzytelniania rozszerzonego (EAP) należy użyć protokołu uwierzytelniania Microsoft Challenge-Handshake (CHAPv2) lub protokołu PEAP (Protected Extensible Authentication Protocol). Żaden inny protokół EAP nie jest obsługiwany.

8. W oknie **Określanie grup użytkowników** wybierz pozycję **Dodaj**, a następnie wybierz odpowiednią grupę. Jeśli grupa nie istnieje, pozostaw zaznaczenie puste, aby przyznać dostęp wszystkim użytkownikom.

    ![Określ okno grup użytkowników, aby zezwalać na dostęp lub go odmawiać](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Wybierz pozycję **Dalej**.

10. W oknie **Określanie filtrów IP** wybierz pozycję **dalej**.

11. W oknie **Określanie ustawień szyfrowania** zaakceptuj ustawienia domyślne, a następnie wybierz przycisk **dalej**.

    ![Okno Określanie ustawień szyfrowania](./media/howto-mfa-nps-extension-vpn/image8.png)

12. W oknie **Określanie nazwy obszaru** pozostaw puste pole Nazwa obszaru, Zaakceptuj ustawienie domyślne, a następnie wybierz przycisk **dalej**.

    ![Okno Określanie nazwy obszaru](./media/howto-mfa-nps-extension-vpn/image9.png)

13. W oknie **Kończenie nowego połączenia telefonicznego lub wirtualnej sieci prywatnej oraz klientów RADIUS** wybierz pozycję **Zakończ**.

    ![Okno ukończonej konfiguracji](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Weryfikowanie konfiguracji usługi RADIUS

W tej sekcji szczegółowo przedstawiono konfigurację utworzoną za pomocą kreatora.

1. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalnej) rozwiń węzeł **klienci usługi RADIUS**, a następnie wybierz pozycję **klienci usługi RADIUS**.

2. W okienku szczegółów kliknij prawym przyciskiem myszy utworzoną klienta usługi RADIUS, a następnie wybierz polecenie **Właściwości**. Właściwości klienta RADIUS (serwer sieci VPN) powinny wyglądać następująco:

    ![Weryfikowanie właściwości i konfiguracji sieci VPN](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Wybierz pozycję **Anuluj**.

4. Na serwerze zasad sieciowych w konsoli serwera NPS (lokalnego) rozwiń węzeł **zasady**, a następnie wybierz pozycję **zasady żądań połączeń**. Zasady połączeń sieci VPN są wyświetlane, jak pokazano na poniższej ilustracji:

    ![Zasady żądań połączeń przedstawiające zasady połączenia sieci VPN](./media/howto-mfa-nps-extension-vpn/image12.png)

5. W obszarze **zasady**wybierz pozycję **zasady sieciowe**. Powinny pojawić się zasady połączeń wirtualnej sieci prywatnej (VPN), które są podobne do zasad przedstawionych na poniższej ilustracji:

    ![Zasady sieciowe przedstawiające zasady połączeń wirtualnej sieci prywatnej](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurowanie serwera sieci VPN do korzystania z uwierzytelniania usługi RADIUS

W tej sekcji skonfigurujesz serwer sieci VPN do korzystania z uwierzytelniania usługi RADIUS. W instrukcjach przyjęto założenie, że masz działającą konfigurację serwera sieci VPN, ale nie skonfigurowano go do korzystania z uwierzytelniania usługi RADIUS. Po skonfigurowaniu serwera sieci VPN upewnij się, że konfiguracja działa zgodnie z oczekiwaniami.

> [!NOTE]
> Jeśli masz już działającą konfigurację serwera sieci VPN, która używa uwierzytelniania usługi RADIUS, możesz pominąć tę sekcję.
>

### <a name="configure-authentication-provider"></a>Konfigurowanie dostawcy uwierzytelniania

1. Na serwerze sieci VPN Otwórz Menedżer serwera.

2. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Routing i dostęp zdalny**.

3. W oknie **Routing i dostęp zdalny** kliknij prawym przyciskiem myszy ** \<server name> (lokalne)**, a następnie wybierz polecenie **Właściwości**.

4. W oknie ** \<server name> właściwości (lokalne)** wybierz kartę **zabezpieczenia** .

5. Na karcie **zabezpieczenia** w obszarze **dostawca uwierzytelniania**wybierz pozycję **uwierzytelnianie usługi RADIUS**, a następnie wybierz pozycję **Konfiguruj**.

    ![Konfigurowanie dostawcy uwierzytelniania usługi RADIUS](./media/howto-mfa-nps-extension-vpn/image15.png)

6. W oknie **uwierzytelnianie usługi RADIUS** wybierz pozycję **Dodaj**.

7. W oknie **Dodawanie serwera RADIUS** wykonaj następujące czynności:

    a. W polu **Nazwa serwera** wprowadź nazwę lub adres IP serwera RADIUS, który został skonfigurowany w poprzedniej sekcji.

    b. W polu wspólny **klucz tajny**wybierz pozycję **Zmień**, a następnie wprowadź wcześniej utworzone i zarejestrowane hasło wspólnego klucza tajnego.

    c. W polu **limit czasu (w sekundach)** wprowadź wartość **30**.  
    Wartość limitu czasu jest wymagana, aby zapewnić wystarczającą ilość czasu na wykonanie drugiego czynnika uwierzytelniania. Niektóre sieci VPN lub regiony wymagają ustawień limitu czasu więcej niż 30 sekund, aby uniemożliwić użytkownikom otrzymywanie wielu połączeń telefonicznych. Jeśli użytkownicy napotykają ten problem, zwiększ wartość limitu **czasu (w sekundach)** w przyrostach wynoszących 30 sekund, dopóki problem nie wystąpi ponownie.

    ![Dodawanie okna serwera RADIUS Konfigurowanie limitu czasu](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Wybierz przycisk **OK**.

### <a name="test-vpn-connectivity"></a>Testowanie łączności z siecią VPN

W tej sekcji należy potwierdzić, że klient VPN jest uwierzytelniany i autoryzowany przez serwer RADIUS podczas próby nawiązania połączenia z portem wirtualnym sieci VPN. W instrukcjach przyjęto założenie, że używasz systemu Windows 10 jako klienta sieci VPN.

> [!NOTE]
> Jeśli klient VPN został już skonfigurowany do łączenia się z serwerem sieci VPN i zapisano ustawienia, można pominąć kroki związane z konfigurowaniem i zapisywaniem obiektu połączenia sieci VPN.
>

1. Na komputerze klienckim sieci VPN wybierz przycisk **Start** , a następnie wybierz przycisk **Ustawienia** .

2. W oknie **Ustawienia systemu Windows** wybierz pozycję **Sieć & Internet**.

3. wybierz pozycję **VPN**.

4. Wybierz pozycję **Dodaj połączenie sieci VPN**.

5. W oknie **Dodawanie połączenia sieci VPN** w polu **dostawca sieci VPN** wybierz pozycję **Windows (wbudowane)**, wypełnij pozostałe pola, zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz**.

    ![Okno "Dodawanie połączenia sieci VPN"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Przejdź do **Panelu sterowania**, a następnie wybierz pozycję **Centrum sieci i udostępniania**.

7. Wybierz pozycję **Zmień ustawienia karty sieciowej**.

    ![Centrum sieci i udostępniania — Zmień ustawienia karty sieciowej](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz polecenie **Właściwości**.

9. W oknie właściwości sieci VPN wybierz kartę **zabezpieczenia** .

10. Na karcie **zabezpieczenia** upewnij się, że jest zaznaczona opcja tylko **Microsoft CHAP wersja 2 (MS-CHAP v2)** , a następnie wybierz przycisk **OK**.

    ![Opcja "Zezwalaj na te protokoły"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kliknij prawym przyciskiem myszy połączenie sieci VPN, a następnie wybierz pozycję **Połącz**.

12. W oknie **Ustawienia** wybierz pozycję **Połącz**.  
    Pomyślne połączenie zostanie wyświetlone w dzienniku zabezpieczeń na serwerze RADIUS, jako identyfikator zdarzenia 6272, jak pokazano poniżej:

    ![okno Właściwości zdarzeń z pomyślnym połączeniem](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Rozwiązywanie problemów z usługą RADIUS

Załóżmy, że konfiguracja sieci VPN działała przed skonfigurowaniem serwera sieci VPN do korzystania z scentralizowanego serwera RADIUS na potrzeby uwierzytelniania i autoryzacji. Jeśli konfiguracja działała, prawdopodobnie problem jest spowodowany błędną konfiguracją serwera RADIUS lub użyciem nieprawidłowej nazwy użytkownika lub hasła. Jeśli na przykład używasz alternatywnego sufiksu UPN w nazwie użytkownika, próba logowania może się nie powieść. Użyj tej samej nazwy konta, aby uzyskać najlepsze wyniki.

Aby rozwiązać te problemy, idealnym miejscem do uruchomienia jest sprawdzenie dzienników zdarzeń zabezpieczeń na serwerze RADIUS. Aby zaoszczędzić czas podczas wyszukiwania zdarzeń, można użyć widoków niestandardowych zasad sieciowych i dostępu do serwera na podstawie ról w Podgląd zdarzeń, jak pokazano poniżej. "Identyfikator zdarzenia 6273" wskazuje zdarzenia, w przypadku których serwer zasad sieciowych odmówił dostępu użytkownikowi.

![Podgląd zdarzeń przedstawiające zdarzenia NPAS](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie Multi-Factor Authentication

Aby uzyskać pomoc w konfigurowaniu użytkowników dla Multi-Factor Authentication Zobacz artykuł [Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartego na chmurze](howto-mfa-getstarted.md#create-conditional-access-policy) i [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Instalowanie i Konfigurowanie rozszerzenia serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania sieci VPN do korzystania z usługi MFA na potrzeby uwierzytelniania klienta na serwerze sieci VPN.

> [!NOTE]
> W kluczu rejestru REQUIRE_USER_MATCH rozróżniana jest wielkość liter. Wszystkie wartości muszą być ustawione w formacie wielką LITERą.
>

Po zainstalowaniu i skonfigurowaniu rozszerzenia serwera NPS do korzystania z usługi MFA jest wymagane każde uwierzytelnianie klienta oparte na usłudze RADIUS, które jest przetwarzane przez ten serwer. Jeśli wszyscy użytkownicy sieci VPN nie są zarejestrowani w usłudze Azure Multi-Factor Authentication, możesz wykonać jedną z następujących czynności:

* Skonfiguruj inny serwer RADIUS, aby uwierzytelniać użytkowników, którzy nie są skonfigurowani do korzystania z usługi MFA.

* Utwórz wpis rejestru, który umożliwia użytkownikom z zamiaru podawanie drugiego czynnika uwierzytelniania, jeśli są one zarejestrowane w usłudze Azure Multi-Factor Authentication.

Utwórz nową wartość ciągu o nazwie _REQUIRE_USER_MATCH w HKLM\SOFTWARE\Microsoft\AzureMfa_i ustaw wartość na *true* lub *false*.

![Ustawienie "Wymagaj dopasowania użytkownika"](./media/howto-mfa-nps-extension-vpn/image34.png)

Jeśli wartość jest równa *true* lub jest pusta, wszystkie żądania uwierzytelnienia podlegają wyzwaniem usługi MFA. Jeśli wartość jest równa *false*, wyzwania usługi MFA są wystawiane tylko dla użytkowników zarejestrowanych w usłudze Azure Multi-Factor Authentication. Używaj ustawienia *false* tylko w testach lub w środowiskach produkcyjnych w trakcie okresu dołączania.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Uzyskaj identyfikator dzierżawy Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS należy podać poświadczenia administratora i identyfikator dzierżawy usługi Azure AD. Aby uzyskać identyfikator dzierżawy, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny dzierżawy platformy Azure.
1. W menu Azure Portal wybierz pozycję **Azure Active Directory**lub Wyszukaj i wybierz pozycję **Azure Active Directory** z dowolnej strony.
1. Na stronie **Przegląd** są wyświetlane *Informacje o dzierżawie* . Wybierz ikonę **kopiowania** obok *identyfikatora dzierżawy*, jak pokazano na poniższym przykładzie zrzutu ekranu:

   ![Pobieranie identyfikatora dzierżawy z Azure Portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

Rozszerzenie serwera NPS musi być zainstalowane na serwerze, na którym jest zainstalowana rola usług zasad sieciowych i dostępu sieciowego oraz która działa jako serwer RADIUS w projekcie. *Nie* Instaluj rozszerzenia serwera NPS na serwerze sieci VPN.

1. Pobierz rozszerzenie serwera NPS z [Centrum pobierania firmy Microsoft](https://aka.ms/npsmfa).

2. Skopiuj plik wykonywalny Instalatora (*NpsExtnForAzureMfaInstaller.exe*) na serwer NPS.

3. Na serwerze NPS kliknij dwukrotnie pozycję **NpsExtnForAzureMfaInstaller.exe** i, jeśli zostanie wyświetlony monit, wybierz pozycję **Uruchom**.

4. W oknie **Konfiguracja rozszerzenia serwera NPS dla usługi Azure MFA** Przejrzyj postanowienia licencyjne dotyczące oprogramowania, zaznacz pole wyboru **Akceptuję warunki i postanowienia licencyjne** , a następnie wybierz pozycję **Zainstaluj**.

    ![Okno "rozszerzenie serwera zasad sieciowych dla Instalatora usługi Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. W oknie **Konfiguracja rozszerzenia serwera NPS dla usługi Azure MFA** wybierz pozycję **Zamknij**.  

    ![Okno potwierdzenia "Instalacja powiodła się"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia z rozszerzeniem serwera NPS przy użyciu skryptu programu PowerShell

Aby zapewnić bezpieczną komunikację i gwarancję, skonfiguruj certyfikaty do użycia przez rozszerzenie serwera NPS. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który służy do konfigurowania certyfikatu z podpisem własnym do użycia z serwerem zasad sieciowych.

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym.
* Kojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD.
* Przechowuje certyfikat w magazynie komputera lokalnego.
* Przyznaje użytkownikowi sieciowemu dostęp do prywatnego klucza certyfikatu.
* Uruchamia ponownie usługę serwera NPS.

Jeśli chcesz użyć własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, podaj rozszerzenie przy użyciu poświadczeń administracyjnych Azure Active Directory oraz wcześniej skopiowanego identyfikatora dzierżawy Azure Active Directory. Konto musi znajdować się w tej samej dzierżawie usługi Azure AD, co umożliwia włączenie rozszerzenia. Uruchom skrypt na każdym serwerze NPS, na którym instalujesz rozszerzenie serwera NPS.

1. Uruchom program Windows PowerShell jako administrator.

2. W wierszu polecenia programu PowerShell wpisz **CD "C:\Program Files\Microsoft\AzureMfa\Config"**, a następnie wybierz klawisz ENTER.

3. W następnym wierszu polecenia wprowadź **.\AzureMfaNpsExtnConfigSetup.ps1**, a następnie wybierz klawisz ENTER. Skrypt sprawdza, czy jest zainstalowany moduł programu Azure AD PowerShell. Jeśli nie jest zainstalowany, skrypt instaluje moduł.

    ![Uruchamianie skryptu konfiguracji AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Jeśli wystąpi błąd zabezpieczeń ze względu na protokół TLS, Włącz protokół TLS 1,2 przy użyciu `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` polecenia z poziomu monitu programu PowerShell.
    
    Po zweryfikowaniu instalacji modułu programu PowerShell przez skrypt zostanie wyświetlone okno logowania modułu Azure Active Directory PowerShell.

4. Wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie wybierz pozycję **Zaloguj się**.

    ![Uwierzytelnianie w usłudze Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. W wierszu polecenia Wklej skopiowany wcześniej identyfikator dzierżawy, a następnie wybierz klawisz ENTER.

    ![Wprowadź identyfikator dzierżawy usługi Azure AD skopiowany przed](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Dane wyjściowe są podobne do następujących:

    ![Okno programu PowerShell zawierające certyfikat z podpisem własnym](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Uruchom ponownie serwer.

### <a name="verify-the-configuration"></a>Weryfikowanie konfiguracji

Aby sprawdzić konfigurację, należy ustanowić nowe połączenie sieci VPN z serwerem sieci VPN. Po pomyślnym wprowadzeniu poświadczeń uwierzytelniania podstawowego połączenie sieci VPN czeka na pomyślne uwierzytelnienie, jak pokazano poniżej.

![Okno Ustawienia systemu Windows sieci VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

W przypadku pomyślnego uwierzytelnienia za pomocą pomocniczej metody weryfikacji, która została wcześniej skonfigurowana w usłudze Azure MFA, nawiązano połączenie z zasobem. Jeśli jednak uwierzytelnianie pomocnicze zakończy się niepowodzeniem, odmówiono dostępu do zasobu.

W poniższym przykładzie aplikacja Microsoft Authenticator na Windows Phone zapewnia uwierzytelnianie pomocnicze:

![Przykład monitu MFA na Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Po pomyślnym uwierzytelnieniu przy użyciu metody pomocniczej masz udzielony dostęp do portu wirtualnego na serwerze sieci VPN. Ponieważ wymagało użycia dodatkowej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przypadku używania tylko kombinacji nazwy użytkownika i hasła.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Wyświetlanie dzienników Podgląd zdarzeń dla pomyślnych zdarzeń logowania

Aby wyświetlić pomyślne zdarzenia logowania w dziennikach Podgląd zdarzeń systemu Windows, zapytaj dziennik zabezpieczeń systemu Windows na serwerze NPS, wprowadzając następujące polecenie programu PowerShell:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Podgląd zdarzeń zabezpieczeń programu PowerShell](./media/howto-mfa-nps-extension-vpn/image44.png)

Możesz również wyświetlić dziennik zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu sieciowego, jak pokazano poniżej:

![Przykładowy dziennik serwera zasad sieciowych](./media/howto-mfa-nps-extension-vpn/image45.png)

Na serwerze, na którym zainstalowano rozszerzenie serwera NPS dla usługi Azure Multi-Factor Authentication, można znaleźć Podgląd zdarzeń Dzienniki aplikacji, które są specyficzne dla rozszerzenia w *Logs\Microsoft\AzureMfa aplikacji i usług*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Przykładowe Podgląd zdarzeń okienko rejestrowania autoryzacji](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, Rozpocznij Rozwiązywanie problemów, sprawdzając, czy użytkownik jest skonfigurowany do korzystania z usługi MFA. Użytkownik nawiązuje połączenie z [Azure Portal](https://portal.azure.com). Jeśli użytkownik zostanie poproszony o uwierzytelnienie pomocnicze i może się uwierzytelnić, można wyeliminować niepoprawną konfigurację usługi MFA jako problem.

Jeśli uwierzytelnianie wieloskładnikowe działa dla użytkownika, zapoznaj się z odpowiednimi dziennikami Podgląd zdarzeń. Dzienniki obejmują zdarzenia zabezpieczeń, operacyjne bramy i dzienniki usługi Azure Multi-Factor Authentication omówione w poprzedniej sekcji.

Przykładem dziennika zabezpieczeń, który wyświetla nieudane zdarzenie logowania (Identyfikator zdarzenia 6273), przedstawiono tutaj:

![Dziennik zabezpieczeń przedstawiający nieudane zdarzenie logowania](./media/howto-mfa-nps-extension-vpn/image47.png)

Poniżej przedstawiono zdarzenie powiązane z dziennikiem usługi Azure Multi-Factor Authentication:

![Dzienniki usługi Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension-vpn/image48.png)

Aby przeprowadzić Zaawansowane rozwiązywanie problemów, zapoznaj się z plikami dziennika w formacie bazy danych serwera NPS, w których zainstalowano usługę serwera NPS. Pliki dziennika są tworzone w folderze _%systemroot%\System32\Logs_ jako pliki tekstowe rozdzielane przecinkami. Aby uzyskać opis plików dziennika, zobacz [Interpretowanie plików dziennika w formacie serwera NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)).

Wpisy w tych plikach dziennika trudno interpretować, chyba że zostaną wyeksportowane do arkusza kalkulacyjnego lub bazy danych. Wiele narzędzi do analizy usługi uwierzytelniania internetowego (IAS) można znaleźć w trybie online, aby pomóc w interpretacji plików dziennika. W tym miejscu przedstawiono dane wyjściowe z jednej z takich aplikacji do pobrania ["shareware"](https://www.deepsoftware.com/iasviewer) :

![Przykładowy Parser usługi IAS dla aplikacji "shareware"](./media/howto-mfa-nps-extension-vpn/image49.png)

Aby przeprowadzić dodatkowe Rozwiązywanie problemów, można użyć analizatora protokołów, takiego jak Wireshark lub [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide). Poniższy obraz z programu Wireshark pokazuje komunikaty RADIUS między serwerem sieci VPN i serwerem zasad sieciowych.

![Program Microsoft Message Analyzer pokazujący filtrowany ruch](./media/howto-mfa-nps-extension-vpn/image50.png)

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Następne kroki

[Pobierz Multi-Factor Authentication platformy Azure](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)