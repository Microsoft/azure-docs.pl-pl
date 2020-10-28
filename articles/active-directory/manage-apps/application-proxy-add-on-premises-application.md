---
title: Samouczek — Dodawanie lokalnego serwera proxy aplikacji w usłudze Azure AD
description: Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku pokazano, jak przygotować środowisko do użytku z serwerem proxy aplikacji. Następnie używa Azure Portal, aby dodać aplikację lokalną do dzierżawy usługi Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0ece14fb1a96ac8cc66f4d35d027b9d93d1f800e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792824"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku przygotujemy środowisko do obsługi serwera proxy aplikacji. Gdy środowisko będzie gotowe, za pomocą witryny Azure Portal dodamy aplikację lokalną do dzierżawy usługi Azure AD.

Ten samouczek umożliwia opanowanie następujących czynności:

> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Sprawdza, czy użytkownik testowy może zalogować się do aplikacji przy użyciu konta usługi Azure AD

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać aplikację lokalną do usługi Azure AD, potrzebne są:

* [Subskrypcja Microsoft Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory)
* Konto administratora aplikacji
* Tożsamości użytkowników muszą być synchronizowane z katalogu lokalnego lub tworzone bezpośrednio w dzierżawach usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnienie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji serwera proxy aplikacji i posiadanie informacji o identyfikatorze użytkownika w celu przeprowadzenia rejestracji jednokrotnej (SSO).

### <a name="windows-server"></a>Server systemu Windows

Aby użyć serwera proxy aplikacji, potrzebujesz serwera systemu Windows z systemem Windows Server 2012 R2 lub nowszym. Na serwerze zainstalujesz łącznik serwera proxy aplikacji. Serwer łącznika musi się łączyć z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które zamierzasz opublikować.

Aby zapewnić wysoką dostępność w środowisku produkcyjnym, zalecamy użycie więcej niż jednego serwera systemu Windows. W tym samouczku wystarczy nam jeden serwer systemu Windows.

> [!IMPORTANT]
> W przypadku instalowania łącznika w systemie Windows Server 2019 należy wyłączyć obsługę protokołu HTTP2 w składniku WinHttp dla ograniczonego delegowania protokołu Kerberos. Ta wartość jest domyślnie wyłączona we wcześniejszych wersjach obsługiwanych systemów operacyjnych. Dodanie następującego klucza rejestru i ponowne uruchomienie serwera spowoduje wyłączenie go w systemie Windows Server 2019. Należy pamiętać, że jest to klucz rejestru dla całego komputera.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp] "EnableDefaultHttp2"=dword:00000000
> ```
>

#### <a name="recommendations-for-the-connector-server"></a>Zalecenia dotyczące serwera łącznika

1. Serwer łącznika powinien znajdować się blisko serwerów aplikacji, aby wydajność komunikacji między łącznikiem a aplikacją była optymalna. Aby uzyskać więcej informacji, zobacz [Network topology considerations (Kwestie związane z topologią sieci)](application-proxy-network-topology.md).
1. Serwer łącznika i serwery aplikacji sieci Web powinny należeć do tej samej domeny Active Directory lub zakresu zaufanych domen. Posiadanie serwerów w tej samej domenie lub domen ufających jest wymaganiem do korzystania z logowania jednokrotnego (SSO) ze zintegrowanym uwierzytelnianiem systemu Windows (IWA) i ograniczonego delegowania protokołu Kerberos (KCD). Jeśli serwer łącznika znajduje się w innej domenie usługi Active Directory niż serwery aplikacji internetowych, korzystanie z logowania jednokrotnego wymaga użycia delegowania opartego na zasobach. Aby uzyskać więcej informacji, zobacz [KCD for single sign-on with Application Proxy (Ograniczone delegowanie Kerberos dla logowania jednokrotnego przy użyciu serwera proxy aplikacji)](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Jeśli wdrożono serwer proxy ochrony hasłem usługi Azure AD, nie instaluj jednocześnie serwera proxy usługi Azure serwer proxy aplikacji usługi Azure AD i usługi Azure AD Password Protection na tym samym komputerze. Usługa Azure serwer proxy aplikacji usługi Azure AD i serwer proxy ochrony hasłem usługi Azure AD instalują różne wersje usługi Azure AD Connect Agent Aktualizator. Te różne wersje są niezgodne, jeśli są zainstalowane razem na tym samym komputerze.

#### <a name="tls-requirements"></a>Wymagania protokołu TLS

Przed zainstalowaniem łącznika serwera proxy aplikacji na serwerze łącznika systemu Windows należy włączyć protokół TLS 1.2.

Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Uruchom ponownie serwer.

## <a name="prepare-your-on-premises-environment"></a>Przygotowywanie środowiska lokalnego

Zacznij od włączenia komunikacji do centrów danych platformy Azure w celu przygotowania środowiska do serwer proxy aplikacji usługi Azure AD platformy Azure. Jeśli w ścieżce znajduje się zapora, upewnij się, że jest otwarta. Otwarta Zapora umożliwia łącznikowi wykonywanie żądań HTTPS (TCP) do serwera proxy aplikacji.

> [!IMPORTANT]
> Jeśli instalujesz łącznik dla Azure Government Cloud, postępuj zgodnie z [procedurami](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud) [wstępnymi](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) i instalacją. Wymaga to włączenia dostępu do innego zestawu adresów URL i dodatkowego parametru do uruchomienia instalacji.

### <a name="open-ports"></a>Otwieranie portów

Otwórz następujące porty dla ruchu **wychodzącego** .

   | Numer portu | Zastosowanie |
   | --- | --- |
   | 80 | Pobieranie list odwołania certyfikatów (CRL) podczas weryfikowania certyfikatu TLS/SSL |
   | 443 | Cała komunikacja wychodząca do usługi serwera proxy aplikacji |

Jeśli zapora wymusza ruch odpowiednio dla użytkowników, którzy go generują, otwórz również porty 80 i 443 dla ruchu pochodzącego z usług systemu Windows działających jako usługi sieciowe.

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Port | Zastosowanie |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |Łącznik używa tych adresów URL do weryfikowania certyfikatów. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS |Łącznik używa tych adresów URL podczas procesu rejestracji. |
| ctldl.windowsupdate.com | 80/HTTP |Łącznik używa tego adresu URL podczas procesu rejestracji. |

Można zezwolić na połączenia z &ast; . msappproxy.NET i &ast; . ServiceBus.Windows.NET, Jeśli zapora lub serwer proxy umożliwia skonfigurowanie list dozwolonych DNS. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP i tagów usług platformy Azure — chmury publicznej](https://www.microsoft.com/download/details.aspx?id=56519). Zakresy adresów IP są aktualizowane co tydzień.

## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika

Aby użyć serwera proxy aplikacji, Zainstaluj łącznik na każdym serwerze z systemem Windows, który jest używany z usługą serwera proxy aplikacji. Łącznik to agent, który zarządza połączeniami wychodzącymi z lokalnych serwerów aplikacji do serwera proxy aplikacji w usłudze Azure AD. Łącznik można zainstalować na serwerach, na których zainstalowano również innych agentów uwierzytelniania, takich jak program Azure AD Connect.


Aby zainstalować łącznik:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
1. Wybierz swoją nazwę użytkownika w prawym górnym rogu. Upewnij się, że logujesz się do katalogu, który używa serwera proxy aplikacji. Jeśli musisz zmienić katalogi, wybierz pozycję **Przełącz katalog** i wybierz katalog, w którym jest używany serwer proxy aplikacji.
1. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory** .
1. W obszarze **Zarządzanie** wybierz pozycję **serwer proxy aplikacji** .
1. Wybierz pozycję **Pobierz usługę łącznika** .

    ![Pobierz usługę łącznika, aby zobaczyć warunki korzystania z usługi](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Zapoznaj się z warunkami użytkowania usługi. Gdy wszystko będzie gotowe, wybierz pozycję **Akceptuj postanowienia & Pobierz** .
1. W dolnej części okna wybierz pozycję **Uruchom** , aby zainstalować łącznik. Zostanie otwarty kreator instalacji.
1. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować usługę. Po wyświetleniu monitu o zarejestrowanie łącznika na serwerze proxy aplikacji w dzierżawie usługi Azure AD, wprowadź swoje poświadczenia administratora aplikacji.
    - Jeśli korzystasz z programu Internet Explorer (IE) i opcja **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ma wartość **Wł** , ekran rejestracji może nie być widoczny. Aby uzyskać dostęp, wykonaj instrukcje podane w komunikacie o błędzie. Upewnij się, że **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** jest **wyłączona** .

### <a name="general-remarks"></a>Uwagi ogólne

Jeśli wcześniej zainstalowano łącznik, zainstaluj go ponownie, aby uzyskać najnowszą wersję. Aby wyświetlić informacje o poprzednio wydanych wersjach i ich zmianach, zobacz [serwer proxy aplikacji: historia wersji](application-proxy-release-version-history.md).

Jeśli masz więcej niż jeden serwer systemu Windows dla aplikacji lokalnych, musisz zainstalować i zarejestrować łącznik na każdym serwerze. Możesz tworzyć grupy łączników. Aby uzyskać więcej informacji, zobacz [Grupy łączników](application-proxy-connector-groups.md).

Jeśli do łączenia się z Internetem w Twojej organizacji są używane serwery proxy, musisz je skonfigurować pod kątem serwera proxy aplikacji.  Aby uzyskać więcej informacji, zobacz [Work with existing on-premises proxy servers (Praca z istniejącymi lokalnymi serwerami proxy)](application-proxy-configure-connectors-with-proxy-servers.md). 

Aby uzyskać informacje o łącznikach oraz ich aktualizowaniu, a także wskazówki na temat planowania wydajności, zobacz [Understand Azure AD Application Proxy connectors (Informacje dotyczące łączników serwera proxy aplikacji usługi Azure AD)](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany

Aby upewnić się, że nowy łącznik został zainstalowany poprawnie, możesz użyć witryny Azure Portal lub serwera systemu Windows.

### <a name="verify-the-installation-through-azure-portal"></a>Weryfikowanie instalacji za pomocą Azure Portal

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Zaloguj się do katalogu dzierżawy w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **serwer proxy aplikacji** w sekcji **Zarządzanie** . Na stronie widać wszystkie łączniki i grupy łączników.
1. Wyświetl łącznik, aby sprawdzić jego szczegóły. Łączniki powinny być domyślnie rozwinięte. Jeśli łącznik, który chcesz wyświetlić, nie jest rozwinięty, rozwiń łącznik, aby wyświetlić szczegóły. Zielona ikona oznacza, że łącznik jest aktywny i może łączyć się z usługą. Jednak nawet wtedy problem z siecią może uniemożliwiać łącznikowi odbieranie wiadomości.

    ![Łączniki serwer proxy aplikacji usługi Azure AD platformy Azure](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Aby uzyskać pomoc dotyczącą instalowania łącznika, zobacz [problem z instalowaniem łącznika serwera proxy aplikacji](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Weryfikowanie instalacji przy użyciu systemu Windows Server

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Otwórz Menedżera usług systemu Windows, naciskając klawisz **Windows** i wprowadzając tekst *services.msc* .
1. Sprawdź, czy stan następujących dwóch usług ma wartość **Działa** .
   - **Łącznik serwera proxy aplikacji usługi Microsoft AAD** umożliwia łączność.
   - **Aktualizator łączników serwera proxy aplikacji usługi Microsoft AAD** to automatyczna usługa aktualizacji. Sprawdza ona dostępność nowych wersji łącznika i aktualizuje go zgodnie z potrzebami.

     ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Jeśli stan usług nie jest **uruchomiony** , kliknij prawym przyciskiem myszy, aby wybrać poszczególne usługi, a następnie wybierz polecenie **Uruchom** .

## <a name="add-an-on-premises-app-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Po przygotowaniu środowiska i zainstalowaniu łącznika możemy dodać aplikacje lokalne do usługi Azure AD.  

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator.
2. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory** .
3. Wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **Nowa aplikacja** .
4. W sekcji **aplikacje lokalne** wybierz pozycję **Dodaj aplikację lokalną** .
5. W sekcji **Dodawanie własnej aplikacji lokalnej** podaj następujące informacje o aplikacji:

    | Pole | Opis |
    | :---- | :---------- |
    | **Nazwa** | Nazwa aplikacji, która będzie wyświetlana w moich aplikacjach i w Azure Portal. |
    | **Wewnętrzny adres URL** | Ten adres URL umożliwia dostęp do aplikacji w sieci prywatnej. Możesz wprowadzić określoną ścieżkę na serwerze zaplecza, która zostanie opublikowana, podczas gdy pozostała część serwera pozostanie nieopublikowana. W ten sposób możesz opublikować wiele lokacji jako różne aplikacje na tym samym serwerze, nadając im różne nazwy i reguły dostępu.<br><br>W przypadku publikowania ścieżki upewnij się, że zawiera ona wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Jeśli na przykład aplikacja jest w sieci https: \/ /yourapp/App i używa obrazów znajdujących się w protokole https: \/ /yourapp/Media, należy opublikować https: \/ /yourapp/jako ścieżkę. Wewnętrzny adres URL nie musi być stroną docelową widoczną dla użytkowników. Aby uzyskać więcej informacji, zobacz [Set a custom home page for published apps (Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji)](application-proxy-configure-custom-home-page.md). |
    | **Zewnętrzny adres URL** | Ten adres umożliwia użytkownikom dostęp do aplikacji spoza sieci. Jeśli nie chcesz używać domyślnej domeny serwera proxy aplikacji, zobacz [Custom domains in Azure AD Application Proxy (Domeny niestandardowe na serwerze proxy aplikacji usługi Azure AD)](application-proxy-configure-custom-domain.md).|
    | **Wstępne uwierzytelnianie** | Sposób, w jaki serwer proxy aplikacji weryfikuje użytkowników przed udzieleniem im dostępu do aplikacji.<br><br>**Azure Active Directory** — serwer proxy aplikacji przekierowuje użytkowników do zalogowania się w usłudze Azure AD, co umożliwia uwierzytelnienie ich uprawnień do katalogu i aplikacji. Zaleca się pozostawienie tej opcji jako domyślnej, dzięki czemu można korzystać z funkcji zabezpieczeń usługi Azure AD, takich jak dostęp warunkowy i Multi-Factor Authentication. Usługa **Azure Active Directory** jest wymagana do monitorowania aplikacji za pomocą usługi Microsoft Cloud Application Security.<br><br>**Przekazywanie** — użytkownicy nie muszą uwierzytelniać się w usłudze Azure AD w celu uzyskania dostępu do aplikacji. Można jednak na zapleczu skonfigurować wymagania dotyczące uwierzytelniania. |
    | **Grupa łączników** | Łączniki umożliwiają przetwarzanie zdalnego dostępu do aplikacji, a grupy łączników pozwalają klasyfikować łączniki i aplikacje według regionu, sieci lub przeznaczenia. Jeśli nie masz jeszcze żadnych grup łączników, Twoja aplikacja zostanie przypisana do grupy **Domyślne** .<br><br>Jeśli nawiązywanie połączeń w aplikacji odbywa się za pomocą elementów WebSocket, wszystkie łączniki w grupie muszą być w wersji 1.5.612.0 lub nowszej.|

6. W razie potrzeby skonfiguruj **dodatkowe ustawienia** . W przypadku większości aplikacji należy pozostawić domyślne wartości tych ustawień. 

    | Pole | Opis |
    | :---- | :---------- |
    | **Limit czasu aplikacji zaplecza** | Opcję tę ustaw na wartość **Długi** tylko wtedy, gdy uwierzytelnianie aplikacji i łączenie się z nią trwa długo. Domyślnie limit czasu aplikacji zaplecza ma długość 85 sekund. Po ustawieniu na wartość Long limit czasu zaplecza zostanie zwiększony do 180 sekund. |
    | **Użyj pliku cookie tylko HTTP** | Ustawienie wartości **Tak** powoduje, że pliki cookie serwera proxy aplikacji będą zawierały flagę HTTPOnly w nagłówku odpowiedzi HTTP. Jeśli korzystasz z usług pulpitu zdalnego, ustaw tę wartość na **Nie** .|
    | **Użyj bezpiecznego pliku cookie**| Ustawienie wartości **Tak** powoduje przesyłanie plików cookie za pośrednictwem bezpiecznego kanału, takiego jak zaszyfrowane żądanie HTTPS.
    | **Używaj trwałego pliku cookie**| Pozostaw wartość **Nie** . Tego ustawienia należy używać tylko w przypadku aplikacji, które nie mogą udostępniać plików cookie między procesami. Aby uzyskać więcej informacji na temat ustawień plików cookie, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Przekształć adresy URL w nagłówkach** | Pozostaw wartość **Tak** , chyba że aplikacja wymaga, aby żądanie uwierzytelnienia zawierało nagłówek oryginalnego hosta. |
    | **Przekształć adresy URL w treści aplikacji** | Zachowaj tę wartość jako **nie** , chyba że stałe linki HTML z innymi aplikacjami lokalnymi i nie używaj domen niestandardowych. Aby uzyskać więcej informacji, zobacz [Link translation with Application Proxy (Przekształcanie linków przy użyciu serwera proxy aplikacji)](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ustaw tę wartość na **Tak** , jeśli zamierzasz monitorować aplikację za pomocą usługi Microsoft Cloud App Security (MCAS). Aby uzyskać więcej informacji, zobacz [konfigurowanie monitorowania dostępu do aplikacji w czasie rzeczywistym przy użyciu Microsoft Cloud App Security i Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Wybierz pozycję **Dodaj** .

## <a name="test-the-application"></a>Testowanie aplikacji

Wszystko jest gotowe do sprawdzenia, czy aplikacja została dodana poprawnie. W kolejnych krokach dodasz do aplikacji konto użytkownika i spróbujesz się zalogować.

### <a name="add-a-user-for-testing"></a>Dodawanie użytkownika testowego

Przed dodaniem użytkownika do aplikacji sprawdź, czy konto użytkownika ma już uprawnienia dostępu do aplikacji z sieci firmowej.

Aby dodać użytkownika testowego:

1. Wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz aplikację, którą chcesz przetestować.
2. Wybierz pozycję **wprowadzenie** , a następnie wybierz pozycję **Przypisz użytkownika do testowania** .
3. W obszarze **Użytkownicy i grupy** wybierz pozycję **Dodaj użytkownika** .
4. W obszarze **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy** . Zostanie wyświetlona sekcja **Użytkownicy i grupy** .
5. Wybierz konto, które chcesz dodać.
6. Wybierz pozycję **Wybierz** , a następnie wybierz pozycję **Przypisz** .

### <a name="test-the-sign-on"></a>Testowanie logowania

Aby przetestować logowanie do aplikacji:

1. Z aplikacji, którą chcesz przetestować, wybierz pozycję **serwer proxy aplikacji** .
2. W górnej części strony wybierz pozycję **Testuj aplikację** , aby uruchomić test w aplikacji, i sprawdź, czy występują problemy z konfiguracją.
3. Upewnij się, że najpierw uruchom aplikację, aby przetestować logowanie do aplikacji, a następnie Pobierz raport diagnostyczny, aby zapoznać się ze wskazówkami dotyczącymi rozwiązywania problemów z wykrytymi problemami.

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [Troubleshoot Application Proxy problems and error messages (Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach)](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym samouczku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przygotowaliśmy środowisko lokalne pod kątem obsługi serwera proxy aplikacji, a następnie zainstalowaliśmy i zarejestrowaliśmy łącznik serwera proxy aplikacji. Później dodaliśmy aplikację do dzierżawy usługi Azure AD. Sprawdziliśmy, czy użytkownik może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Zweryfikowano, że użytkownik testowy może zalogować się do aplikacji przy użyciu konta usługi Azure AD

Możesz przystąpić do konfigurowania aplikacji pod kątem logowania jednokrotnego. Użyj poniższego linku, aby wybrać metodę logowania jednokrotnego i znaleźć samouczki logowania jednokrotnego.

> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](sso-options.md#choosing-a-single-sign-on-method)
