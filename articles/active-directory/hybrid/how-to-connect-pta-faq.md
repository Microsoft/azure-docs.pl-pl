---
title: 'Azure AD Connect: uwierzytelnianie przekazywane — często zadawane pytania | Microsoft Docs'
description: Odpowiedzi na często zadawane pytania dotyczące Azure Active Directory uwierzytelniania przekazywanego
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
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb9d9e7c60344a2a4c538c6d3bf59e337e4569c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836329"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory uwierzytelnianie przekazywane: często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące uwierzytelniania przekazywanego za pomocą usługi Azure Active Directory (Azure AD). Kontynuuj sprawdzanie zaktualizowanej zawartości.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Jakie metody logowania do usługi Azure AD, uwierzytelniania przekazywanego, synchronizacji skrótów haseł i Active Directory Federation Services (AD FS) należy wybrać?

Zapoznaj się z [tym przewodnikiem](./choose-ad-authn.md) , aby porównać różne metody logowania do usługi Azure AD i jak wybrać właściwą metodę logowania dla organizacji.

## <a name="is-pass-through-authentication-a-free-feature"></a>Czy uwierzytelnianie przekazywane jest bezpłatne?

Uwierzytelnianie przekazywane jest bezpłatną funkcją. Nie są potrzebne żadne płatne wersje usługi Azure AD, które mają być używane.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Czy [dostęp warunkowy](../conditional-access/overview.md) działa z uwierzytelnianiem przekazującym?

Tak. Wszystkie funkcje dostępu warunkowego, w tym usługa Azure AD Multi-Factor Authentication, działają z uwierzytelnianiem przekazującym.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Czy uwierzytelnianie przekazywane obsługuje "alternatywny identyfikator" jako nazwę użytkownika, a nie "userPrincipalName"?
Tak, logowanie przy użyciu wartości innej niż nazwa UPN, takiej jak alternatywny adres e-mail, jest obsługiwane zarówno w przypadku uwierzytelniania przekazywanego (PTA), jak i synchronizacji skrótów haseł (PHS). Aby uzyskać więcej informacji na temat [alternatywnego identyfikatora logowania](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Czy synchronizacja skrótów haseł działa jako rezerwowa do uwierzytelniania przekazywanego?

Nie. Uwierzytelnianie przekazywane nie _jest_ automatycznie przełączane w tryb failover na potrzeby synchronizacji skrótów haseł. Aby uniknąć niepowodzeń logowania użytkownika, należy skonfigurować uwierzytelnianie przekazywane w celu zapewnienia [wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Co się stanie w przypadku przełączenia z synchronizacją skrótów haseł na uwierzytelnianie przekazywane?

W przypadku używania Azure AD Connect do przełączania metody logowania z synchronizacji skrótów haseł na uwierzytelnianie przekazywane, uwierzytelnianie przekazywane jest podstawową metodą logowania dla użytkowników w domenach zarządzanych. Należy pamiętać, że skróty haseł wszystkich użytkowników, które wcześniej były synchronizowane przez synchronizację skrótów haseł, pozostają przechowywane w usłudze Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Czy mogę zainstalować łącznik [usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) na tym samym serwerze, na którym znajduje się Agent uwierzytelniania przekazywanego?

Tak. Te konfiguracje są obsługiwane przez te wersje agenta uwierzytelniania przekazywanego, w wersji 1.5.193.0 lub nowszej.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jakie wersje Azure AD Connect i agenta uwierzytelniania przekazywanego są potrzebne?

Aby ta funkcja działała, musisz mieć wersję 1.1.750.0 lub nowszą dla Azure AD Connect i 1.5.193.0 lub nowszego dla agenta uwierzytelniania przekazywanego. Zainstaluj wszystkie programy na serwerach z systemem Windows Server 2012 R2 lub nowszym.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co się stanie, jeśli hasło użytkownika wygasło i spróbuje się zalogować przy użyciu uwierzytelniania przekazywanego?

Jeśli skonfigurowano funkcję [zapisywania zwrotnego haseł](../authentication/concept-sspr-writeback.md) dla określonego użytkownika, a użytkownik loguje się przy użyciu uwierzytelniania przekazywanego, może zmienić lub zresetować swoje hasła. Hasła są zapisywane z powrotem do Active Directory lokalnego zgodnie z oczekiwaniami.

Jeśli nie skonfigurowano funkcji zapisywania zwrotnego haseł dla określonego użytkownika lub jeśli użytkownik nie ma przypisanej prawidłowej licencji usługi Azure AD, użytkownik nie może zaktualizować swojego hasła w chmurze. Nie mogą oni zaktualizować hasła, nawet jeśli ich hasło wygasło. Użytkownik zobaczy ten komunikat: "Twoja organizacja nie zezwala na aktualizowanie hasła w tej witrynie. Zaktualizuj je zgodnie z metodą zalecaną przez organizację lub poproszenie administratora o pomoc. Użytkownik lub administrator musi zresetować swoje hasło w Active Directory lokalnym.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak uwierzytelnianie przekazywane chroni przed atakami polegającymi na wymuszeniu hasła?

[Przeczytaj informacje o inteligentnej blokadzie](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Jak agenci uwierzytelniania przekazywanego komunikują się za pośrednictwem portów 80 i 443?

- Agenci uwierzytelniania wysyłają żądania HTTPS przez port 443 dla wszystkich operacji na funkcjach.
- Agenci uwierzytelniania wysyłają żądania HTTP przez port 80 w celu pobrania list odwołania certyfikatów (CRL) protokołu TLS/SSL.

     >[!NOTE]
     >Ostatnie aktualizacje zmniejszyły liczbę portów wymaganą przez funkcję. Jeśli masz starsze wersje Azure AD Connect lub agenta uwierzytelniania, pozostaw te porty otwarte również: 5671, 8080, 9090, 9091, 9350, 9352 i 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Czy agenci uwierzytelniania przekazywanego komunikują się za pośrednictwem wychodzącego serwera proxy sieci Web?

Tak. Jeśli w środowisku lokalnym włączono funkcję autowykrywania serwera proxy sieci Web, agenci uwierzytelniania automatycznie próbują zlokalizować i użyć serwera proxy sieci Web w sieci.

Jeśli w danym środowisku nie masz usługi WPAD, możesz dodać informacje o serwerze proxy (jak pokazano poniżej), aby zezwolić agentowi uwierzytelniania przekazującego na komunikację z usługą Azure AD:
- Skonfiguruj informacje o serwerze proxy w programie Internet Explorer przed zainstalowaniem agenta uwierzytelniania przekazywanego na serwerze. Pozwoli to na ukończenie instalacji agenta uwierzytelniania, ale nadal będzie on wyświetlany jako **nieaktywny** w portalu administracyjnym.
- Na serwerze przejdź do lokalizacji "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Edytuj plik konfiguracyjny "AzureADConnectAuthenticationAgentService" i Dodaj następujące wiersze (Zastąp ciąg "http \: //contosoproxy.com:8080" rzeczywistym adresem serwera proxy):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Czy można zainstalować dwóch lub więcej agentów uwierzytelniania przekazującego na tym samym serwerze?

Nie, tylko jeden Agent uwierzytelniania przekazywanego można zainstalować na jednym serwerze. Jeśli chcesz skonfigurować uwierzytelnianie przekazywane w celu zapewnienia wysokiej dostępności, [postępuj zgodnie z instrukcjami w tym miejscu](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Czy muszę ręcznie odnowić certyfikaty używane przez agentów uwierzytelniania przekazywanego?

Komunikacja między każdym agentem uwierzytelniania przekazywanego i usługą Azure AD jest zabezpieczona przy użyciu uwierzytelniania opartego na certyfikatach. Te [Certyfikaty są automatycznie odnawiane co kilka miesięcy przez usługę Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Nie ma potrzeby ręcznego odnawiania tych certyfikatów. W razie potrzeby można oczyścić starsze wygasłe certyfikaty.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak mogę usunąć agenta uwierzytelniania przekazywanego?

Tak długo, jak działa Agent uwierzytelniania przekazującego, pozostaje aktywny i ciągle obsługuje żądania logowania użytkownika. Jeśli chcesz odinstalować agenta uwierzytelniania, przejdź do pozycji **Panel sterowania — > programy — > programy i funkcje** i Odinstaluj zarówno program **Microsoft Azure AD Connect Agent** , jak i **Microsoft Azure AD Connect Agent Aktualizator** .

W przypadku zaznaczenia bloku uwierzytelnianie przekazywane w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com) po zakończeniu poprzedniego kroku zostanie wyświetlony Agent uwierzytelniania wyświetlany jako **nieaktywny**. Jest to _oczekiwane_. Agent uwierzytelniania jest automatycznie usuwany z listy po upływie 10 dni.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Już używam AD FS do logowania się do usługi Azure AD. Jak mogę przełączyć je na uwierzytelnianie przekazywane?

W przypadku migrowania z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego zdecydowanie zalecamy zapoznanie się z naszym szczegółowym przewodnikiem wdrażania opublikowanym [tutaj](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Czy można używać uwierzytelniania przekazywanego w środowisku Active Directorym wielolasach?

Tak. Środowiska z obsługą wielu lasów są obsługiwane, jeśli istnieją zaufania lasów (dwukierunkowe) między Active Directory lasów i jeśli Routing sufiksów nazw jest prawidłowo skonfigurowany.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Czy uwierzytelnianie przekazywane zapewnia Równoważenie obciążenia przez wielu agentów uwierzytelniania?

Nie, zainstalowanie wielu agentów uwierzytelniania Pass-through gwarantuje tylko [wysoką dostępność](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Nie zapewnia to deterministycznego równoważenia obciążenia między agentami uwierzytelniania. Każdy agent uwierzytelniania (losowo) może przetwarzać określone żądanie logowania użytkownika.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Ile agentów uwierzytelniania Pass-through trzeba zainstalować?

Zainstalowanie wielu agentów uwierzytelniania przekazywanego zapewnia [wysoką dostępność](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Jednak nie zapewnia to deterministycznego równoważenia obciążenia między agentami uwierzytelniania.

Należy wziąć pod uwagę szczytowe i średnie obciążenie żądań logowania, które powinny być widoczne w dzierżawie. W ramach testu porównawczego pojedynczy Agent uwierzytelniania może obsłużyć 300 do 400 uwierzytelnień na sekundę w standardowym 4-rdzeniowym PROCESORze, 16 GB pamięci RAM.

Aby oszacować ruch sieciowy, Skorzystaj z następujących wskazówek dotyczących ustalania wielkości:
- Każde żądanie ma rozmiar ładunku (0,5 K + 1K * num_of_agents) b; oznacza to, że dane z usługi Azure AD do agenta uwierzytelniania. W tym miejscu wartość "num_of_agents" wskazuje liczbę agentów uwierzytelniania zarejestrowanych w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku (KB bajtów); oznacza to, że dane z agenta uwierzytelniania do usługi Azure AD.

W przypadku większości klientów co najmniej dwa lub trzy agenci uwierzytelniania są wystarczające do zapewnienia wysokiej dostępności i pojemności. Należy zainstalować agentów uwierzytelniania blisko kontrolerów domeny, aby zwiększyć opóźnienia logowania.

>[!NOTE]
>Istnieje limit systemu 40 agentów uwierzytelniania dla dzierżawy.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Czy mogę zainstalować pierwszego agenta uwierzytelniania przekazywanego na serwerze innym niż ten, na którym działa Azure AD Connect?

Nie, ten scenariusz _nie_ jest obsługiwany.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Dlaczego do włączenia uwierzytelniania przekazywanego jest wymagane konto administratora globalnego tylko w chmurze?

Zaleca się włączenie lub wyłączenie uwierzytelniania przekazywanego przy użyciu konta administratora globalnego tylko w chmurze. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../fundamentals/add-users-azure-active-directory.md). W ten sposób gwarantujesz, że nie będziesz mieć zablokowanej dzierżawy.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak mogę wyłączyć uwierzytelnianie przekazywane?

Uruchom ponownie Kreatora Azure AD Connect i Zmień metodę logowania użytkownika z uwierzytelniania przekazywanego na inną metodę. Ta zmiana powoduje wyłączenie uwierzytelniania przekazywanego w dzierżawie i odinstalowanie agenta uwierzytelniania z serwera programu. Agentów uwierzytelniania należy odinstalować ręcznie z innych serwerów.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co się stanie po odinstalowaniu agenta uwierzytelniania przekazywanego?

W przypadku odinstalowania agenta uwierzytelniania przekazywanego z serwera serwer zatrzyma akceptowanie żądań logowania. Aby uniknąć przerwania możliwości logowania użytkownika w dzierżawie, przed odinstalowaniem agenta uwierzytelniania przekazującego upewnij się, że jest uruchomiony inny Agent uwierzytelniania.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Mam starszą dzierżawę, która była pierwotnie skonfigurowana przy użyciu AD FS.  Ostatnio przeprowadzono migrację do usługi PTA, ale teraz nie widzimy żadnych zmian nazw UPN synchronizowanych z usługą Azure AD.  Dlaczego nasze nazwy UPN nie są synchronizowane?

Odp.: w następujących okolicznościach zmiany lokalnych nazw UPN mogą nie być synchronizowane, jeśli:

- Dzierżawa usługi Azure AD została utworzona przed 15 czerwca 2015
- Początkowo jesteś federacyjny z dzierżawą usługi Azure AD przy użyciu AD FS do uwierzytelniania
- Przełączono się z użytkownikami zarządzanymi przy użyciu usługi PTA jako uwierzytelnianie

Wynika to z faktu, że domyślne zachowanie dzierżawców utworzonych przed 15 czerwca 2015 było blokować zmiany nazwy UPN.  Jeśli zachodzi potrzeba odblokowania zmian nazw UPN, należy uruchomić następujące cmdlt programu PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Dzierżawy utworzone po 15 czerwca 2015 mają domyślne zachowanie synchronizowania zmian nazw UPN.   



## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): informacje o scenariuszach, które są obsługiwane i które nie są.
- [Szybki Start](how-to-connect-pta-quick-start.md): Rozpoczynanie pracy z uwierzytelnianiem przekazywanym usługi Azure AD.
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md): informacje na temat konfigurowania możliwości inteligentnego blokowania w dzierżawie w celu ochrony kont użytkowników.
- [Głębokie szczegółowe](how-to-connect-pta-how-it-works.md): zrozumienie, jak działa funkcja uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): informacje na temat rozwiązywania typowych problemów z funkcją uwierzytelniania przekazywanego.
- [Głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj szczegółowe informacje techniczne dotyczące funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne w usłudze Azure AD](how-to-connect-sso.md): Dowiedz się więcej o tej funkcji uzupełniającej.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): użyj forum Azure Active Directory, aby obsłużyć nowe żądania funkcji.
