---
title: Logowanie jednokrotne oparte na protokole Kerberos w Azure Active Directory z serwerem proxy aplikacji
description: Obejmuje sposób zapewnienia logowania jednokrotnego przy użyciu serwer proxy aplikacji usługi Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 860d29d3fff2187e770a5ff00b7145fc188a497c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426498"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji

Możesz udostępnić Logowanie jednokrotne dla aplikacji lokalnych publikowanych za pomocą serwera proxy aplikacji zabezpieczonego przy użyciu zintegrowanego uwierzytelniania systemu Windows. Te aplikacje wymagają biletu protokołu Kerberos w celu uzyskania dostępu. Serwer proxy aplikacji używa ograniczonego delegowania protokołu Kerberos (KCD) do obsługi tych aplikacji. 

Możesz włączyć logowanie jednokrotne do aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA), dając uprawnienia łączników serwera proxy aplikacji w Active Directory, aby personifikować użytkowników. Łączniki używają tego uprawnienia do wysyłania i odbierania tokenów w ich imieniu.

## <a name="how-single-sign-on-with-kcd-works"></a>Jak działa Logowanie jednokrotne za pomocą KCD
Ten diagram wyjaśnia przepływ, gdy użytkownik próbuje uzyskać dostęp do aplikacji lokalnej korzystającej z IWA.

![Diagram przepływu uwierzytelniania usługi Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. Użytkownik wprowadza adres URL w celu uzyskania dostępu do lokalnej aplikacji za pomocą serwera proxy aplikacji.
2. Serwer proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD w celu wstępnego uwierzytelnienia. W tym momencie usługa Azure AD stosuje wszelkie odpowiednie zasady uwierzytelniania i autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik jest zweryfikowany, usługa Azure AD tworzy token i wysyła go do użytkownika.
3. Użytkownik przekazuje token do serwera proxy aplikacji.
4. Serwer proxy aplikacji weryfikuje token i pobiera z niego główną nazwę użytkownika (UPN), a następnie łącznik ściąga nazwę UPN, a główna nazwa usługi (SPN) za pośrednictwem podwójnego uwierzytelnionego bezpiecznego kanału.
5. Łącznik wykonuje negocjowanie ograniczone delegowanie protokołu Kerberos (KCD) z lokalną usługą AD, personifikując użytkownika w celu uzyskania tokenu Kerberos do aplikacji.
6. Active Directory wysyła token Kerberos dla aplikacji do łącznika.
7. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu Kerberos otrzymanego z usługi AD.
8. Aplikacja wysyła odpowiedź do łącznika, który jest następnie zwracany do usługi serwera proxy aplikacji i na końcu do użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z logowaniem jednokrotnym dla aplikacji IWA upewnij się, że środowisko jest gotowe z następującymi ustawieniami i konfiguracjami:

* Aplikacje, takie jak aplikacje sieci Web programu SharePoint, są ustawione tak, aby używały zintegrowanego uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Włączanie obsługi uwierzytelniania Kerberos](https://technet.microsoft.com/library/dd759186.aspx)lub dla programu SharePoint, zobacz [Planowanie uwierzytelniania Kerberos w programie SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Wszystkie aplikacje mają [nazwy główne usługi](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Serwer z uruchomionym łącznikiem i serwerem, na którym działa aplikacja, są przyłączone do domeny i częścią tej samej domeny lub domen ufających. Aby uzyskać więcej informacji na temat przyłączania do domeny, zobacz [dołączanie komputera do domeny](https://technet.microsoft.com/library/dd807102.aspx).
* Serwer z uruchomionym łącznikiem ma dostęp do odczytu atrybutu TokenGroupsGlobalAndUniversal dla użytkowników. To ustawienie domyślne mogło mieć wpływ na bezpieczeństwo środowiska.

### <a name="configure-active-directory"></a>Konfigurowanie usługi Active Directory
Konfiguracja Active Directory różni się w zależności od tego, czy łącznik serwera proxy aplikacji i serwer aplikacji znajdują się w tej samej domenie, czy nie.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Łącznik i serwer aplikacji w tej samej domenie
1. W Active Directory przejdź do pozycji **Narzędzia**  >  **Użytkownicy i komputery**.
2. Wybierz serwer, na którym jest uruchomiony łącznik.
3. Kliknij prawym przyciskiem myszy i wybierz pozycję **Właściwości**  >  **delegowanie**.
4. Wybierz pozycję **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. 
5. Wybierz pozycję **Użyj dowolnego protokołu uwierzytelniania**.
6. W obszarze **usługi, do których to konto może przedstawić delegowane poświadczenia** Dodaj wartość dla tożsamości nazwy SPN serwera aplikacji. Dzięki temu łącznik serwera proxy aplikacji może personifikować użytkowników w usłudze AD przed aplikacjami zdefiniowanymi na liście.

   ![Łącznik — zrzut ekranu okno Właściwości SVR](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Łącznik i serwer aplikacji w różnych domenach
1. Aby uzyskać listę wymagań wstępnych dotyczących pracy z usługą KCD w różnych domenach, zobacz [ograniczone delegowanie protokołu Kerberos między domenami](https://technet.microsoft.com/library/hh831477.aspx).
2. Użyj `principalsallowedtodelegateto` Właściwości konta usługi (komputera lub dedykowanego konta użytkownika domeny) aplikacji sieci Web, aby włączyć delegowanie uwierzytelniania Kerberos z serwera proxy aplikacji (łącznik). Serwer aplikacji jest uruchomiony w kontekście programu `webserviceaccount` , a serwer delegowania jest `connectorcomputeraccount` . Uruchom poniższe polecenia na kontrolerze domeny (z systemem Windows Server 2012 R2 lub nowszym) w domenie `webserviceaccount` . Użyj prostych nazw (nie nazw UPN) dla obu kont.

   Jeśli `webserviceaccount` jest to konto komputera, użyj następujących poleceń:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Jeśli `webserviceaccount` jest to konto użytkownika, użyj następujących poleceń:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego 
1. Opublikuj swoją aplikację zgodnie z instrukcjami opisanymi w artykule [publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md). Upewnij się, że wybrano **Azure Active Directory** jako **metodę wstępnego uwierzytelniania**.
2. Gdy aplikacja zostanie wyświetlona na liście aplikacji dla przedsiębiorstw, wybierz ją, a następnie kliknij pozycję **Logowanie jednokrotne**.
3. Ustaw tryb logowania jednokrotnego na **zintegrowane uwierzytelnianie systemu Windows**.  
4. Wprowadź **wewnętrzną nazwę SPN aplikacji** serwera aplikacji. W tym przykładzie nazwa SPN dla naszej opublikowanej aplikacji to http/www. contoso. com. Ta nazwa SPN musi znajdować się na liście usług, do których łącznik może przedstawić delegowane poświadczenia. 
5. Wybierz pozycję **Delegowana tożsamość logowania**, aby używać łącznika w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnymi tożsamościami lokalnymi i w chmurze](#working-with-different-on-premises-and-cloud-identities)

   ![Zaawansowana konfiguracja aplikacji](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Logowanie jednokrotne dla aplikacji innych niż systemu Windows

Przepływ delegowania protokołu Kerberos w usłudze Azure serwer proxy aplikacji usługi Azure AD jest uruchamiany, gdy usługa Azure AD uwierzytelnia użytkownika w chmurze. Gdy żądanie zostanie odebrane lokalnie, łącznik usługi Azure serwer proxy aplikacji usługi Azure AD wystawia bilet protokołu Kerberos w imieniu użytkownika, współpracując z lokalnym Active Directory. Ten proces jest określany jako ograniczone delegowanie protokołu Kerberos (KCD). 

W następnej fazie żądanie jest wysyłane do aplikacji zaplecza przy użyciu tego biletu Kerberos. 

Istnieje kilka mechanizmów, które definiują sposób wysyłania biletu protokołu Kerberos w takich żądaniach. Większość serwerów spoza systemu Windows oczekuje, że otrzymuje je w postaci tokenu SPNEGO. Ten mechanizm jest obsługiwany przez usługę Azure serwer proxy aplikacji usługi Azure AD, ale jest domyślnie wyłączony. Łącznik można skonfigurować dla SPNEGO lub standardowego tokenu Kerberos, ale nie obu.

W przypadku skonfigurowania maszyny łącznika dla SPNEGO upewnij się, że wszystkie inne łączniki w tej grupie łączników zostały również skonfigurowane z SPNEGO. Aplikacje oczekujejące standardowego tokenu Kerberos powinny być kierowane przez inne łączniki, które nie są skonfigurowane dla SPNEGO. Niektóre aplikacje sieci Web akceptują oba formaty bez konieczności wprowadzania zmian w konfiguracji. 
 

Aby włączyć SPNEGO:

1. Otwórz wiersz polecenia, który jest uruchamiany jako administrator.
2. W wierszu polecenia Uruchom następujące polecenia na serwerach łączników, które wymagają SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Aplikacje inne niż Windows zazwyczaj nazwy użytkowników użytkownika lub konta SAM zamiast adresów e-mail domeny. Jeśli ta sytuacja dotyczy Twoich aplikacji, należy skonfigurować pole tożsamości delegowanej nazwy do łączenia tożsamości w chmurze z tożsamościami aplikacji. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Praca z różnymi tożsamościami lokalnymi i w chmurze
Serwer proxy aplikacji zakłada, że użytkownicy mają dokładnie taką samą tożsamość w chmurze i lokalnie. Ale w niektórych środowiskach ze względu na zasady firmowe lub zależności aplikacji, organizacje mogą wymagać użycia identyfikatorów alternatywnych do logowania. W takich przypadkach można nadal używać KCD do logowania jednokrotnego. Skonfiguruj **delegowaną tożsamość logowania** dla każdej aplikacji w celu określenia tożsamości, która ma być używana podczas przeprowadzania logowania jednokrotnego.  

Ta funkcja pozwala wielu organizacjom, które mają różne tożsamości lokalne i w chmurze, mieć Logowanie jednokrotne z chmury do aplikacji lokalnych, bez konieczności wprowadzania przez użytkowników różnych nazw użytkowników i haseł. Obejmuje to organizacje, które:

* Wewnętrznie z wieloma domenami ( joe@us.contoso.com , joe@eu.contoso.com ) i jedną domeną w chmurze ( joe@contoso.com ).
* Zawiera wewnętrznie nazwę domeny bez obsługi routingu ( joe@contoso.usa ) i prawną jedną w chmurze.
* Nie używaj wewnętrznie nazw domen (Jan)
* Używaj różnych aliasów lokalnie i w chmurze. Na przykład joe-johns@contoso.com a joej@contoso.com  

Za pomocą serwera proxy aplikacji możesz wybrać tożsamość do użycia w celu uzyskania biletu Kerberos. To ustawienie dotyczy każdej aplikacji. Niektóre z tych opcji są odpowiednie dla systemów, które nie akceptują formatu adresu e-mail, inne są przeznaczone do alternatywnej nazwy logowania.

![Zrzut ekranu parametru tożsamości delegowanej nazwy logowania](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Jeśli zostanie użyta delegowana tożsamość logowania, wartość może nie być unikatowa we wszystkich domenach lub lasach w organizacji. Można uniknąć tego problemu, publikując te aplikacje dwukrotnie przy użyciu dwóch różnych grup łączników. Ponieważ każda aplikacja ma innych odbiorców użytkowników, można przyłączyć łączniki do innej domeny.

Jeśli **nazwa lokalnego konta sam** jest używana jako tożsamość logowania, komputer hostujący Łącznik należy dodać do domeny, w której znajduje się konto użytkownika.

### <a name="configure-sso-for-different-identities"></a>Konfigurowanie logowania jednokrotnego dla różnych tożsamości
1. Skonfiguruj ustawienia Azure AD Connect, aby główna tożsamość była adresem e-mail (pocztą). Odbywa się to w ramach procesu dostosowywania, zmieniając pole **głównej nazwy użytkownika** w ustawieniach synchronizacji. Te ustawienia określają również sposób logowania użytkowników do usługi Office 365, Windows10 urządzeń i innych aplikacji korzystających z usług Azure AD jako ich magazynu tożsamości.  
   ![Zrzut ekranu przedstawiający użytkowników — lista rozwijana głównej nazwy użytkownika](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. W ustawieniach konfiguracji aplikacji dla aplikacji, którą chcesz zmodyfikować, wybierz **delegowaną tożsamość logowania** do użycia:

   * Główna nazwa użytkownika (na przykład joe@contoso.com )
   * Alternatywna nazwa podmiotu użytkownika (na przykład joed@contoso.local )
   * Część username głównej nazwy użytkownika (na przykład Jan)
   * Część username alternatywnej nazwy głównej użytkownika (na przykład joed)
   * Nazwa lokalnego konta SAM (zależnie od konfiguracji kontrolera domeny)

### <a name="troubleshooting-sso-for-different-identities"></a>Rozwiązywanie problemów SSO dla różnych tożsamości
Jeśli wystąpi błąd w procesie logowania jednokrotnego, jest on wyświetlany w dzienniku zdarzeń komputera łącznika zgodnie z opisem w [temacie Rozwiązywanie problemów](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Jednak w niektórych przypadkach żądanie zostało pomyślnie wysłane do aplikacji zaplecza, podczas gdy ta aplikacja odpowiada na różne inne odpowiedzi HTTP. Rozwiązywanie problemów z tymi przypadkami powinno zacząć się, sprawdzając numer zdarzenia 24029 na maszynie łącznika w dzienniku zdarzeń sesji serwera proxy aplikacji. Tożsamość użytkownika, która była używana na potrzeby delegowania, pojawia się w polu "użytkownik" w szczegółach zdarzenia. Aby włączyć dziennik sesji, wybierz opcję **Pokaż dzienniki analityczne i debugowania** w menu Widok podglądu zdarzeń.

## <a name="next-steps"></a>Następne kroki

* [Jak skonfigurować aplikację serwera proxy aplikacji do korzystania z ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)

