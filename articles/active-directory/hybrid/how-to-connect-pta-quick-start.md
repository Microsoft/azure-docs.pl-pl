---
title: Uwierzytelnianie przekazywane usługi Azure AD — szybki start | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak rozpocząć pracę z uwierzytelnianiem przekazu usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Uwierzytelnianie przekazywanie usługi Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18ffb48b2e7978831155afaf2e675bb720e57544
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082207"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: szybki start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Wdrażanie uwierzytelniania przekazywania usługi Azure AD

Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) umożliwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Uwierzytelnianie przekazywane loguje użytkowników, sprawdzając ich hasła bezpośrednio w lokalnej usłudze Active Directory.

>[!IMPORTANT]
>W przypadku migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego zdecydowanie zalecamy przestrzeganie opublikowanego [tutaj](https://aka.ms/adfstoPTADPDownload)naszego szczegółowego przewodnika po wdrożeniach.

Postępuj zgodnie z tymi instrukcjami, aby wdrożyć uwierzytelnianie przekazywane w dzierżawie:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdź wymagania wstępne

Upewnij się, że istnieją następujące wymagania wstępne.

>[!IMPORTANT]
>Z punktu widzenia zabezpieczeń administratorzy powinni traktować serwer z uruchomionym agentem PTA tak, jakby był kontrolerem domeny.  Serwery agenta PTA powinny być wzmocnione zgodnie z opisem w [zabezpieczaniu kontrolerów domeny przed atakiem](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy, jeśli usługi lokalne nie powiodą się lub staną się niedostępne. Dowiedz się więcej o [dodawaniu globalnego konta administratora tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku ma kluczowe znaczenie dla zapewnienia, że nie zostaniesz zablokowany z dzierżawy.
2. Dodaj co najmniej jedną [niestandardową nazwę domeny](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Identyfikowanie serwera z systemem Windows Server 2012 R2 lub nowszym w celu uruchomienia usługi Azure AD Connect. Jeśli nie jest już włączona, [włącz TLS 1.2 na serwerze](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Dodaj serwer do tego samego lasu usługi Active Directory, co użytkownicy, których hasła należy sprawdzić.
2. Zainstaluj [najnowszą wersję usługi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serwerze określonym w poprzednim kroku. Jeśli masz już uruchomienie usługi Azure AD Connect, upewnij się, że wersja jest 1.1.750.0 lub nowsza.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli _nie_ zamierzasz używać synchronizacji skrótów haseł w połączeniu z uwierzytelnianiem przekazywania, przeczytaj [informacje o wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Zidentyfikuj jeden lub więcej dodatkowych serwerów (z systemem Windows Server 2012 R2 lub nowszym, z włączoną funkcją protokołu TLS 1.2), na których można uruchamiać autonomiczne agenty uwierzytelniania. Te dodatkowe serwery są potrzebne, aby zapewnić wysoką dostępność żądań logowania. Dodaj serwery do tego samego lasu usługi Active Directory, co użytkownicy, których hasła należy sprawdzić.

    >[!IMPORTANT]
    >W środowiskach produkcyjnych zaleca się, że masz co najmniej 3 agentów uwierzytelniania uruchomionych w dzierżawie. Istnieje limit systemowy 40 agentów uwierzytelniania na dzierżawę. I jako najlepszą praktykę, traktować wszystkie serwery z agentami uwierzytelniania jako systemy warstwy 0 (patrz [odwołanie](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Jeśli między serwerami a usługą Azure AD znajduje się zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci uwierzytelniania mogą składać żądania *wychodzące* do usługi Azure AD za pomocą następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu TLS/SSL |
     | **443** | Obsługuje całą komunikację wychodzącą z usługą |
     | **8080** (opcjonalnie) | Agenci uwierzytelniania zgłaszają swój stan co dziesięć minut za pomocą portu 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. Port 8080 _nie_ jest używany do logowania użytkowników. |
     
     Jeśli zapora wymusza reguły zgodnie z użytkownikami źródłowymi, otwórz te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy zezwala na ** \*** umieszczanie na białej liście DNS, dodaj połączenia do msappproxy.net i ** \*.servicebus.windows.net**. Jeśli nie, zezwalaj na dostęp do [zakresów adresów IP centrum danych platformy Azure,](https://www.microsoft.com/download/details.aspx?id=41653)które są aktualizowane co tydzień.
   - Agenci uwierzytelniania potrzebują dostępu do **login.windows.net** i **login.microsoftonline.com** do wstępnej rejestracji. Otwórz również zaporę dla tych adresów URL.
   - Aby potwierdzić certyfikat, odblokuj następujące adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**i **www\.microsoft.com:80**. Ponieważ te adresy URL są używane do sprawdzania poprawności certyfikatów w innych produktach firmy Microsoft, te adresy URL mogą być już odblokowane.

### <a name="azure-government-cloud-prerequisite"></a>Warunek wstępny chmury platformy Azure dla instytucji rządowych
Przed włączeniem uwierzytelniania przekazywania za pośrednictwem usługi Azure AD Connect w kroku 2 pobierz najnowszą wersję agenta PTA z witryny Azure portal.  Należy upewnić się, że agent jest wersje **x.x.xxx.x** lub nowszych.  Aby zweryfikować agenta, zobacz [Uaktualnianie agentów uwierzytelniania](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Po pobraniu najnowszej wersji agenta, należy postępować zgodnie z poniższymi instrukcjami, aby skonfigurować uwierzytelnianie przekazywane za pośrednictwem usługi Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włącz funkcję

Włącz uwierzytelnianie przekazywane za pośrednictwem [usługi Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Uwierzytelnianie przekazywane można włączyć na serwerze podstawowym lub przemieszczania usługi Azure AD Connect. Zdecydowanie zaleca się włączenie go z serwera podstawowego. Jeśli konfigurujesz serwer przejściowy usługi Azure AD Connect w przyszłości, **należy** nadal wybierać uwierzytelnianie przekazywane jako opcję logowania; wybranie innej opcji **spowoduje wyłączenie** uwierzytelniania przekazywanego w dzierżawie i zastąpienie ustawienia na serwerze podstawowym.

Jeśli instalujesz usługę Azure AD Connect po raz pierwszy, wybierz [niestandardową ścieżkę instalacji](how-to-connect-install-custom.md). Na stronie **Logowania użytkownika** wybierz pozycję **Uwierzytelnianie przekazywane** jako metodę Zaloguj **się.** Po pomyślnym zakończeniu agent uwierzytelniania przekazywania jest instalowany na tym samym serwerze co usługa Azure AD Connect. Ponadto funkcja uwierzytelniania przekazywania jest włączona w dzierżawie.

![Usługa Azure AD Connect: logowanie użytkownika](./media/how-to-connect-pta-quick-start/sso3.png)

Jeśli usługa Azure AD Connect została już zainstalowana przy użyciu [instalacji ekspresowej](how-to-connect-install-express.md) lub niestandardowej ścieżki [instalacji,](how-to-connect-install-custom.md) wybierz zadanie **zmień logowanie użytkownika w** usłudze Azure AD Connect, a następnie wybierz pozycję **Dalej**. Następnie wybierz opcję **Uwierzytelnianie przekazywane** jako metodę logowania. Po pomyślnym zakończeniu agent uwierzytelniania przekazywania jest zainstalowany na tym samym serwerze co usługa Azure AD Connect i funkcja jest włączona w dzierżawie.

![Usługa Azure AD Connect: zmienianie logowania użytkownika](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane jest funkcją na poziomie dzierżawy. Włączenie go wpływa na logowanie użytkowników we _wszystkich_ domenach zarządzanych w dzierżawie. Jeśli przełączasz się z usług federacyjnych Active Directory (AD FS) na uwierzytelnianie przekazywane, należy odczekać co najmniej 12 godzin przed zamknięciem infrastruktury usług AD FS. Ten czas oczekiwania jest zapewnienie, że użytkownicy mogą zachować logowania się do programu Exchange ActiveSync podczas przejścia. Aby uzyskać więcej pomocy na temat migracji z usług AD FS do uwierzytelniania przekazywanego, zapoznaj się ze szczegółowym planem wdrożenia opublikowanym [tutaj](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Krok 3: Przetestuj funkcję

Postępuj zgodnie z poniższymi instrukcjami, aby sprawdzić, czy włączono uwierzytelnianie przekazywane poprawnie:

1. Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługę Azure Active Directory** w lewym okienku.
3. Wybierz **usługę Azure AD Connect**.
4. Sprawdź, czy funkcja **uwierzytelniania przekazywanego** jest wyświetlana jako **Włączona**.
5. Wybierz **pozycję Uwierzytelnianie przekazywane**. Okienko **uwierzytelniania przekazywanego** zawiera listę serwerów, na których są instalowani agenci uwierzytelniania.

![Centrum administracyjne usługi Azure Active Directory: okienko Usługi Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centrum administracyjne usługi Azure Active Directory: okienko uwierzytelniania przekazywanego](./media/how-to-connect-pta-quick-start/pta8.png)

Na tym etapie użytkownicy ze wszystkich domen zarządzanych w dzierżawie mogą logować się przy użyciu uwierzytelniania przekazywanego. Jednak użytkownicy z domen federacyjnych nadal logują się przy użyciu usług AD FS lub innego dostawcy federacji, który został wcześniej skonfigurowany. Jeśli przekonwertujesz domenę z federacyjnej na zarządzaną, wszyscy użytkownicy z tej domeny automatycznie zaczną się logować przy użyciu uwierzytelniania przekazywanego. Funkcja uwierzytelniania przekazywanego nie ma wpływu na użytkowników tylko w chmurze.

## <a name="step-4-ensure-high-availability"></a>Krok 4: Zapewnij wysoką dostępność

Jeśli planujesz wdrożyć uwierzytelnianie przekazywane w środowisku produkcyjnym, należy zainstalować dodatkowe autonomiczne agentów uwierzytelniania. Zainstaluj te agenty uwierzytelniania na serwerach _innych_ niż ten z uruchomionym usługą Azure AD Connect. Ta konfiguracja zapewnia wysoką dostępność dla żądań logowania użytkownika.

>[!IMPORTANT]
>W środowiskach produkcyjnych zaleca się, że masz co najmniej 3 agentów uwierzytelniania uruchomionych w dzierżawie. Istnieje limit systemowy 40 agentów uwierzytelniania na dzierżawę. I jako najlepszą praktykę, traktować wszystkie serwery z agentami uwierzytelniania jako systemy warstwy 0 (patrz [odwołanie](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Instalowanie wielu agentów uwierzytelniania przekazywania zapewnia wysoką dostępność, ale nie deterministyczne równoważenie obciążenia między agentami uwierzytelniania. Aby określić, ile agentów uwierzytelniania jest potrzebnych dla dzierżawy, należy wziąć pod uwagę szczytowe i średnie obciążenie żądań logowania, które mogą być widoczne w dzierżawie. W ramach punktu odniesienia pojedynczy agent uwierzytelniania może obsługiwać uwierzytelnianie od 300 do 400 na sekundę na standardowym 4-rdzeniowym procesorze CPU o pamięci RAM 16 GB.

Aby oszacować ruch sieciowy, użyj następujących wskazówek dotyczących rozmiaru:
- Każde żądanie ma rozmiar ładunku (0,5 K + 1K * num_of_agents) bajtów, czyli danych z usługi Azure AD do agenta uwierzytelniania. W tym miejscu "num_of_agents" wskazuje liczbę agentów uwierzytelniania zarejestrowanych w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku 1K bajtów, czyli dane z agenta uwierzytelniania do usługi Azure AD.

Dla większości klientów trzech agentów uwierzytelniania w sumie są wystarczające dla wysokiej dostępności i pojemności. Należy zainstalować agentów uwierzytelniania w pobliżu kontrolerów domeny, aby poprawić opóźnienie logowania.

Aby rozpocząć, postępuj zgodnie z poniższymi instrukcjami, aby pobrać oprogramowanie Agenta uwierzytelniania:

1. Aby pobrać najnowszą wersję agenta uwierzytelniania (wersja 1.5.193.0 lub nowsza), zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy.
2. Wybierz **usługę Azure Active Directory** w lewym okienku.
3. Wybierz **pozycję Azure AD Connect**, wybierz pozycję **Uwierzytelnianie przekazywane,** a następnie wybierz pozycję Agent **pobierania**.
4. Wybierz przycisk **Zaakceptuj warunki & pobierz.**

![Centrum administracyjne usługi Azure Active Directory: przycisk Pobierz agenta uwierzytelniania](./media/how-to-connect-pta-quick-start/pta9.png)

![Centrum administracyjne usługi Azure Active Directory: okienko Agent pobierania](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Można również bezpośrednio [pobrać oprogramowanie Agenta uwierzytelniania](https://aka.ms/getauthagent). Przed _zainstalowaniem_ należy zapoznać się [z Warunkami korzystania z usługi](https://aka.ms/authagenteula) agenta uwierzytelniania i zaakceptować jego warunki.

Istnieją dwa sposoby wdrażania autonomicznego agenta uwierzytelniania:

Po pierwsze, można to zrobić interaktywnie, po prostu uruchamiając plik wykonywalny pobranego agenta uwierzytelniania i podając poświadczenia administratora globalnego dzierżawy po wyświetleniu monitu.

Po drugie można utworzyć i uruchomić skrypt wdrożenia nienadzorowanego. Jest to przydatne, gdy chcesz wdrożyć wielu agentów uwierzytelniania jednocześnie lub zainstalować agentów uwierzytelniania na serwerach systemu Windows, które nie mają włączonego interfejsu użytkownika lub do których nie można uzyskać dostępu za pomocą pulpitu zdalnego. Oto instrukcje dotyczące korzystania z tego podejścia:

1. Uruchom następujące polecenie, aby zainstalować `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`agenta uwierzytelniania: .
2. Agent uwierzytelniania można zarejestrować w naszej usłudze za pomocą programu Windows PowerShell. Utwórz obiekt `$cred` poświadczenia programu PowerShell, który zawiera nazwę użytkownika i hasło administratora globalnego dla dzierżawy. Uruchom następujące polecenie, * \<\> * zastępując nazwę użytkownika i * \<hasło:\>*

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Przejdź do **folderu C:\Program Files\Microsoft Azure AD** Connect `$cred` Authentication Agent i uruchom następujący skrypt przy użyciu utworzonego obiektu:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Jeśli agent uwierzytelniania jest zainstalowany na maszynie wirtualnej, nie można sklonować maszyny wirtualnej w celu skonfigurowania innego agenta uwierzytelniania. Ta metoda nie jest **podparta**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5: Konfigurowanie funkcji inteligentnej blokady

Smart Lockout pomaga w blokowaniu złych aktorów, którzy próbują odgadnąć hasła użytkowników lub używając metod brutalnej siły, aby dostać się do. Konfigurując ustawienia blokady inteligentnej w usłudze Azure AD i/ lub odpowiednie ustawienia blokady w lokalnej usłudze Active Directory, ataki można odfiltrować, zanim dotrą do usługi Active Directory. Przeczytaj [ten artykuł,](../authentication/howto-password-smart-lockout.md) aby dowiedzieć się więcej na temat konfigurowania ustawień blokady inteligentnej w dzierżawie w celu ochrony kont użytkowników.

## <a name="next-steps"></a>Następne kroki
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadp) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada:](../authentication/howto-password-smart-lockout.md)Dowiedz się, jak skonfigurować funkcję smart lockout w dzierżawie w celu ochrony kont użytkowników.
- [Bieżące ograniczenia:](how-to-connect-pta-current-limitations.md)Dowiedz się, które scenariusze są obsługiwane za pomocą uwierzytelniania przekazywanego, a które nie.
- [Głębokie głębokie nurkowanie techniczne:](how-to-connect-pta-how-it-works.md)Dowiedz się, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania](how-to-connect-pta-faq.md): znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Głębokie skanowanie zabezpieczeń:](how-to-connect-pta-security-deep-dive.md)uzyskaj informacje techniczne dotyczące funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD:](how-to-connect-sso.md)Dowiedz się więcej o tej uzupełniającej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum usługi Azure Active Directory, aby złożyć nowe żądania funkcji.
