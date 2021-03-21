---
title: Uwierzytelnianie przekazywane przez usługę Azure AD — Szybki Start | Microsoft Docs
description: W tym artykule opisano, jak rozpocząć pracę z usługą Azure Active Directory (Azure AD) uwierzytelnianie przekazywane.
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji Active Directory, wymaganych składników usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d7d4888c17ffe46340aa85b8d2a1cc4fa7ed34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581834"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory uwierzytelnianie przekazywane: Szybki Start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Wdrażanie uwierzytelniania przekazywanego za pomocą usługi Azure AD

Uwierzytelnianie przekazujące Azure Active Directory (Azure AD) pozwala użytkownikom na logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Uwierzytelnianie przekazywane umożliwia użytkownikom Weryfikowanie haseł bezpośrednio na Active Directory lokalnych.

>[!IMPORTANT]
>W przypadku migrowania z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego zdecydowanie zalecamy zapoznanie się z naszym szczegółowym przewodnikiem wdrażania opublikowanym [tutaj](https://aka.ms/adfstoPTADPDownload).

>[!NOTE]
>Jeśli wdrażasz uwierzytelnianie przekazywane za pomocą chmury Azure Government, Wyświetl [uwagi dotyczące tożsamości hybrydowej Azure Government](./reference-connect-government-cloud.md).

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć uwierzytelnianie przekazywane w dzierżawie:

## <a name="step-1-check-the-prerequisites"></a>Krok 1. Sprawdzanie wymagań wstępnych

Upewnij się, że są spełnione następujące wymagania wstępne.

>[!IMPORTANT]
>Z punktu widzenia zabezpieczeń Administratorzy powinni traktować serwer z uruchomionym agentem PTA tak, jakby był kontrolerem domeny.  Serwery agenta PTA powinny być wzmocnione w tych samych wierszach, co w przypadku [zabezpieczania kontrolerów domeny przed atakami](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób możesz zarządzać konfiguracją dzierżawy, jeśli usługi lokalne zakończą się niepowodzeniem lub staną się niedostępne. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../fundamentals/add-users-azure-active-directory.md). Wykonanie tego kroku jest niezwykle ważne, aby upewnić się, że dzierżawa nie została zablokowana.
2. Dodaj co najmniej jedną [niestandardową nazwę domeny](../fundamentals/add-custom-domain.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy użyciu jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszym, aby uruchomić Azure AD Connect. Jeśli jeszcze nie włączono, [Włącz protokół TLS 1,2 na serwerze](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Dodaj serwer do tego samego lasu Active Directory co użytkownicy, których hasła należy zweryfikować. Należy zauważyć, że Instalacja agenta uwierzytelniania Pass-Through w wersjach systemu Windows Server Core nie jest obsługiwana. 
2. Zainstaluj [najnowszą wersję Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serwerze zidentyfikowanym w poprzednim kroku. Jeśli masz już Azure AD Connect uruchomione, upewnij się, że wersja to 1.1.750.0 lub nowsza.

    >[!NOTE]
    >Azure AD Connect wersje 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 mają problem związany z synchronizacją skrótów haseł. Jeśli _nie_ zamierzasz używać synchronizacji skrótów haseł w połączeniu z uwierzytelnianiem przekazującym, przeczytaj informacje o [wersji Azure AD Connect](./reference-connect-version-history.md).

3. Zidentyfikuj co najmniej jeden dodatkowy serwer (z systemem Windows Server 2012 R2 lub nowszym, z włączonym protokołem TLS 1,2), w którym można uruchamiać autonomiczne agenci uwierzytelniania. Te dodatkowe serwery są konieczne w celu zapewnienia wysokiej dostępności żądań logowania. Dodaj serwery do tego samego lasu Active Directory co użytkownicy, których hasła należy zweryfikować.

    >[!IMPORTANT]
    >W środowiskach produkcyjnych zalecamy korzystanie z co najmniej 3 agentów uwierzytelniania w dzierżawie. Istnieje limit systemu 40 agentów uwierzytelniania dla dzierżawy. Najlepszym rozwiązaniem jest traktowanie wszystkich serwerów, na których są uruchomione agenci uwierzytelniania jako systemy warstwy 0 (zobacz [odwołanie](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Jeśli między serwerami i usługą Azure AD istnieje Zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci uwierzytelniania mogą wykonywać żądania *wychodzące* do usługi Azure AD za pośrednictwem następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas weryfikacji certyfikatu TLS/SSL |
     | **443** | Obsługa komunikacji wychodzącej do usługi |
     | **8080** (opcjonalnie) | Agenci uwierzytelniania raportują swój stan co dziesięć minut w porcie 8080, jeśli port 443 jest niedostępny. Informacje o stanie agentów są wyświetlane w portalu usługi Azure AD. Port 8080 _nie_ jest używany podczas logowania użytkownika. |
     
     Jeśli reguły zapory są stosowane w zależności od użytkowników generujących ruch, otwórz te porty dla ruchu przychodzącego z usług systemu Windows działających jako usługi sieciowe.
   - Jeśli zapora lub serwer proxy umożliwia dodawanie wpisów DNS do dozwolonych, Dodaj połączenia do **\* . msappproxy.NET** i **\* . ServiceBus.Windows.NET**. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.
   - Jeśli masz wychodzący serwer proxy HTTP, upewnij się, że ten adres URL to autologon.microsoftazuread-sso.com, listy dozwolonych. Należy jawnie określić ten adres URL, ponieważ symbol wieloznaczny nie może zostać zaakceptowany. 
   - Agenci uwierzytelniania muszą mieć dostęp do **login.Windows.NET** i **login.microsoftonline.com** na potrzeby rejestracji wstępnej. Należy również otworzyć Zaporę dla tych adresów URL.
    - Aby sprawdzić poprawność certyfikatu, Odblokuj następujące adresy URL: **crl3.DigiCert.com:80**, **crl4.DigiCert.com:80**, **ocsp.digicert.com:80**, **www \. d-trust.net:80**, **root-C3-CA2-2009.OCSP.d-Trust.NET:80**, **CRL.Microsoft.com:80**, **oneocsp.Microsoft.com:80** i **OCSP.msocsp.com:80**. Ponieważ te adresy URL są używane do sprawdzania poprawności certyfikatu z innymi produktami firmy Microsoft, te adresy URL mogą już być odblokowane.

### <a name="azure-government-cloud-prerequisite"></a>Azure Government wymagań wstępnych w chmurze
Przed włączeniem uwierzytelniania przekazywanego za pośrednictwem Azure AD Connect z krok 2 Pobierz najnowszą wersję agenta PTA z Azure Portal.  Musisz się upewnić, że Agent jest w wersji **1.5.1742.0.** lub nowsza wersja.  Aby zweryfikować agenta, zobacz [uaktualnianie agentów uwierzytelniania](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Po pobraniu najnowszej wersji agenta postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować uwierzytelnianie Pass-Through przy użyciu Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Krok 2. Włączanie funkcji

Włącz uwierzytelnianie przekazywane za [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Uwierzytelnianie przekazywane można włączyć na serwerze podstawowym lub tymczasowym Azure AD Connect. Zdecydowanie zaleca się włączenie go z poziomu serwera podstawowego. W przypadku konfigurowania Azure AD Connect tymczasowego serwera w przyszłości **należy** kontynuować wybieranie uwierzytelniania przekazywanego jako opcję logowania; wybranie innej opcji spowoduje **wyłączenie** uwierzytelniania przekazywanego w dzierżawie i zastąpienie ustawienia na serwerze podstawowym.

Jeśli instalujesz Azure AD Connect po raz pierwszy, wybierz [ścieżkę instalacji niestandardowej](how-to-connect-install-custom.md). Na stronie **logowania użytkownika** wybierz opcję **uwierzytelnianie przekazywane** jako **metodę logowania**. Po pomyślnym ukończeniu Agent uwierzytelniania przekazywanego jest instalowany na tym samym serwerze co Azure AD Connect. Ponadto funkcja uwierzytelniania przekazywanego jest włączona w dzierżawie.

![Azure AD Connect: Logowanie użytkownika](./media/how-to-connect-pta-quick-start/sso3.png)

Jeśli zainstalowano już Azure AD Connect przy użyciu [instalacji ekspresowej](how-to-connect-install-express.md) lub niestandardowej ścieżki [instalacji](how-to-connect-install-custom.md) , wybierz zadanie **zmiany logowania użytkownika** na Azure AD Connect, a następnie wybierz przycisk **dalej**. Następnie wybierz opcję **uwierzytelnianie przekazywane** jako metodę logowania. Po pomyślnym ukończeniu Agent uwierzytelniania przekazywanego jest instalowany na tym samym serwerze co Azure AD Connect i funkcja ta jest włączona w dzierżawie.

![Azure AD Connect: Zmień Logowanie użytkownika](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane jest funkcją na poziomie dzierżawy. Włączenie tej opcji wpływa na logowanie użytkowników we _wszystkich_ domenach zarządzanych w dzierżawie. W przypadku przełączenia się z Active Directory Federation Services (AD FS) na potrzeby uwierzytelniania przekazywanego należy odczekać co najmniej 12 godzin przed zamknięciem infrastruktury AD FS. Ten czas oczekiwania polega na zapewnieniu, że użytkownicy będą mogli nadal logować się do programu Exchange ActiveSync podczas przejścia. Aby uzyskać pomoc dotyczącą migrowania z usługi AD FS do uwierzytelniania przekazywanego, zapoznaj się z naszym [opublikowanym](https://aka.ms/adfstoptadpdownload)planem wdrożenia.

## <a name="step-3-test-the-feature"></a>Krok 3. Testowanie funkcji

Postępuj zgodnie z tymi instrukcjami, aby sprawdzić, czy włączono uwierzytelnianie przekazywane poprawnie:

1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. W lewym okienku wybierz pozycję **Azure Active Directory** .
3. Wybierz **Azure AD Connect**.
4. Sprawdź, czy funkcja **uwierzytelniania przekazywanego** jest wyświetlana jako **włączona**.
5. Wybierz pozycję **uwierzytelnianie przekazywane**. Okienko **uwierzytelnianie przekazywane** zawiera listę serwerów, na których są zainstalowane agenci uwierzytelniania.

![Azure Active Directory centrum administracyjnego: Azure AD Connect okienko](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory centrum administracyjne: okienko uwierzytelniania przekazywanego](./media/how-to-connect-pta-quick-start/pta8.png)

Na tym etapie użytkownicy z wszystkich domen zarządzanych w dzierżawie mogą logować się przy użyciu uwierzytelniania przekazywanego. Jednak użytkownicy z domen federacyjnych kontynuują logowanie się przy użyciu AD FS lub innego dostawcy federacyjnego, który został wcześniej skonfigurowany. W przypadku konwersji domeny z federacyjnego na zarządzane, wszyscy użytkownicy z tej domeny automatycznie uruchamiają Logowanie przy użyciu uwierzytelniania przekazywanego. Funkcja uwierzytelniania przekazywanego nie ma wpływu na użytkowników korzystających tylko z chmury.

## <a name="step-4-ensure-high-availability"></a>Krok 4. zapewnienie wysokiej dostępności

Jeśli planujesz wdrożenie uwierzytelniania przekazywanego w środowisku produkcyjnym, należy zainstalować dodatkowych agentów uwierzytelniania autonomicznego. Zainstaluj tych agentów uwierzytelniania na serwerach _innych_ niż uruchomione Azure AD Connect. Ta konfiguracja zapewnia wysoką dostępność żądań logowania użytkowników.

>[!IMPORTANT]
>W środowiskach produkcyjnych zalecamy korzystanie z co najmniej 3 agentów uwierzytelniania w dzierżawie. Istnieje limit systemu 40 agentów uwierzytelniania dla dzierżawy. Najlepszym rozwiązaniem jest traktowanie wszystkich serwerów, na których są uruchomione agenci uwierzytelniania jako systemy warstwy 0 (zobacz [odwołanie](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Zainstalowanie wielu agentów uwierzytelniania przekazywanego zapewnia wysoką dostępność, ale nie jest to deterministyczne Równoważenie obciążenia między agentami uwierzytelniania. Aby określić liczbę agentów uwierzytelniania potrzebnych dla dzierżawy, należy wziąć pod uwagę szczytowe i średnie obciążenie żądań logowania, które powinny być widoczne dla dzierżawy. W ramach testu porównawczego pojedynczy Agent uwierzytelniania może obsłużyć 300 do 400 uwierzytelnień na sekundę w standardowym 4-rdzeniowym PROCESORze, 16 GB pamięci RAM.

Aby oszacować ruch sieciowy, Skorzystaj z następujących wskazówek dotyczących ustalania wielkości:
- Każde żądanie ma rozmiar ładunku (0,5 K + 1K * num_of_agents) b, czyli dane z usługi Azure AD do agenta uwierzytelniania. W tym miejscu wartość "num_of_agents" wskazuje liczbę agentów uwierzytelniania zarejestrowanych w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku równą 1K bajtów, czyli dane z agenta uwierzytelniania do usługi Azure AD.

W przypadku większości klientów maksymalnie trzy agenci uwierzytelniania są wystarczające do zapewnienia wysokiej dostępności i pojemności. Należy zainstalować agentów uwierzytelniania blisko kontrolerów domeny, aby zwiększyć opóźnienia logowania.

Aby rozpocząć, postępuj zgodnie z poniższymi instrukcjami, aby pobrać oprogramowanie Agent uwierzytelniania:

1. Aby pobrać najnowszą wersję agenta uwierzytelniania (w wersji 1.5.193.0 lub nowszej), zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy.
2. W lewym okienku wybierz pozycję **Azure Active Directory** .
3. Wybierz pozycję **Azure AD Connect**, wybierz pozycję **uwierzytelnianie przekazywane**, a następnie wybierz pozycję **Pobierz agenta**.
4. Wybierz przycisk **Akceptuj postanowienia & Pobierz** .

![Azure Active Directory centrum administracyjnego: Pobierz agenta uwierzytelniania przycisk](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory centrum administracyjnego: pobieranie okienka agenta](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Możesz również bezpośrednio [pobrać oprogramowanie Agent uwierzytelniania](https://aka.ms/getauthagent). _Przed_ zainstalowaniem programu Przejrzyj i zaakceptuj [warunki korzystania z usługi](https://aka.ms/authagenteula) Agent uwierzytelniania.

Istnieją dwa sposoby wdrażania autonomicznego agenta uwierzytelniania:

Najpierw można to zrobić interaktywnie, uruchamiając pobrany plik wykonywalny agenta uwierzytelniania i dostarczając poświadczenia administratora globalnego dzierżawy po wyświetleniu monitu.

Następnie można utworzyć i uruchomić skrypt wdrożenia nienadzorowanego. Jest to przydatne, gdy chcesz wdrożyć wielu agentów uwierzytelniania jednocześnie, lub zainstalować agentów uwierzytelniania na serwerach z systemem Windows, na których nie włączono interfejsu użytkownika lub nie możesz uzyskać dostępu za pomocą Pulpit zdalny. Poniżej przedstawiono instrukcje dotyczące korzystania z tej metody:

1. Uruchom następujące polecenie, aby zainstalować agenta uwierzytelniania: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` .
2. Agenta uwierzytelniania można zarejestrować za pomocą naszej usługi przy użyciu programu Windows PowerShell. Utwórz obiekt poświadczeń programu PowerShell `$cred` , który zawiera nazwę użytkownika i hasło administratora globalnego dla dzierżawy. Uruchom następujące polecenie, zastępując *\<username\>* i *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Przejdź do **folderu C:\Program Files\Microsoft Azure AD Connect Authentication Agent** i uruchom następujący skrypt przy użyciu `$cred` utworzonego obiektu:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Jeśli na maszynie wirtualnej jest zainstalowany agent uwierzytelniania, nie można sklonować maszyny wirtualnej w celu skonfigurowania innego agenta uwierzytelniania. Ta metoda nie jest **obsługiwana**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5. Konfigurowanie możliwości inteligentnego blokowania

Inteligentna blokada pomaga w blokowaniu nieprawidłowych uczestników próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania dostępu do programu. Konfigurując ustawienia blokady inteligentnej w usłudze Azure AD i/lub odpowiednie ustawienia blokady w Active Directory lokalnym, ataki można odfiltrować przed osiągnięciem Active Directory. Przeczytaj [ten artykuł](../authentication/howto-password-smart-lockout.md) , aby dowiedzieć się więcej na temat konfigurowania ustawień blokady inteligentnej w dzierżawie, aby chronić konta użytkowników.

## <a name="next-steps"></a>Następne kroki
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadp) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md): informacje na temat konfigurowania możliwości inteligentnego blokowania w dzierżawie w celu ochrony kont użytkowników.
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): informacje o scenariuszach obsługiwanych przy użyciu uwierzytelniania przekazywanego i tych, które nie są.
- [Głębokie szczegółowe](how-to-connect-pta-how-it-works.md): zrozumienie, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): informacje na temat rozwiązywania typowych problemów z funkcją uwierzytelniania przekazywanego.
- [Głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj informacje techniczne dotyczące funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne w usłudze Azure AD](how-to-connect-sso.md): Dowiedz się więcej o tej funkcji uzupełniającej.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): użyj forum Azure Active Directory, aby obsłużyć nowe żądania funkcji.
