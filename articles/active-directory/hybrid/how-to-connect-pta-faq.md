---
title: 'Usługa Azure AD Connect: uwierzytelnianie przekazywane — często zadawane pytania | Dokumenty firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące uwierzytelniania przekazywania usługi Azure Active Directory
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
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661d4f622dce45aeca1d41ead60f05ccdcfbc9c9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406876"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Uwierzytelnianie przekazywania usługi Azure Active Directory: często zadawane pytania

Ten artykuł dotyczy często zadawanych pytań dotyczących uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD). Sprawdzaj ponownie zaktualizowaną zawartość.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Które z metod logowania się do usługi Azure AD, uwierzytelniania przekazowego, synchronizacji skrótów haseł i usług federacyjnych Active Directory (AD FS) należy wybrać?

Zapoznaj się z [tym przewodnikiem,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) aby porównać różne metody logowania usługi Azure AD i jak wybrać odpowiednią metodę logowania dla twojej organizacji.

## <a name="is-pass-through-authentication-a-free-feature"></a>Czy uwierzytelnianie przekazywane jest bezpłatną funkcją?

Uwierzytelnianie przekazywane jest bezpłatną funkcją. Nie potrzebujesz żadnych płatnych wersji usługi Azure AD, aby z niej korzystać.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>Czy uwierzytelnianie przekazywane jest dostępne w [chmurze Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) i [chmurze Microsoft Azure dla instytucji rządowych?](https://azure.microsoft.com/features/gov/)

Nie. Uwierzytelnianie przekazywane jest dostępne tylko w wystąpieniu usługi Azure AD na całym świecie.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Czy [dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) działa z uwierzytelnianiem przekazywać?

Tak. Wszystkie funkcje dostępu warunkowego, w tym uwierzytelnianie wieloskładnikowe platformy Azure, współpracują z uwierzytelnianiem przekazywania.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Czy uwierzytelnianie przekazywane obsługuje "Alternatywny identyfikator" jako nazwę użytkownika, a nie "userPrincipalName"?
Zaloguj się przy użyciu wartości innej niż UPN, takiej jak alternatywny adres e-mail, jest obecnie testowany w prywatnej wersji zapoznawczej zarówno pod kątem uwierzytelniania przekazywanego (PTA), jak i synchronizacji skrótów haseł (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Czy synchronizacja skrótów haseł działa jako rezerwa do uwierzytelniania przekazywanego?

Nie. Uwierzytelnianie przekazywane _nie_ automatycznie przechodzi w tryb failover do synchronizacji skrótów haseł. Aby uniknąć błędów logowania użytkownika, należy skonfigurować uwierzytelnianie przekazywane pod kątem [wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Co się stanie, gdy przełączyć się z synchronizacji skrótu hasła do uwierzytelniania przekazywanego?

Korzystając z usługi Azure AD Connect, aby przełączyć metodę logowania z synchronizacji skrótu hasła do uwierzytelniania przekazywanego, uwierzytelnianie przekazywane staje się podstawową metodą logowania dla użytkowników w domenach zarządzanych. Należy pamiętać, że skróty haseł wszystkich użytkowników, które zostały wcześniej zsynchronizowane przez synchronizację skrótów haseł, pozostają przechowywane w usłudze Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Czy mogę zainstalować łącznik [serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) na tym samym serwerze co agent uwierzytelniania przekazywania?

Tak. Zmieniono nazwę wersji agenta uwierzytelniania przekazywania, wersja 1.5.193.0 lub nowsza, obsługuje tę konfigurację.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Jakich wersji usługi Azure AD Connect i agenta uwierzytelniania przekazywanego potrzebujesz?

Aby ta funkcja działała, potrzebujesz wersji 1.1.750.0 lub nowszej dla usługi Azure AD Connect i 1.5.193.0 lub nowszej dla agenta uwierzytelniania przekazywania. Zainstaluj całe oprogramowanie na serwerach z systemem Windows Server 2012 R2 lub nowszym.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co się stanie, jeśli hasło mojego użytkownika wygasło i spróbuje się zalogować przy użyciu uwierzytelniania przekazywanego?

Jeśli skonfigurowano [storadztwo hasła](../authentication/concept-sspr-writeback.md) dla określonego użytkownika i jeśli użytkownik loguje się przy użyciu uwierzytelniania przekazywania, może zmienić lub zresetować swoje hasła. Hasła są zapisywane z powrotem do lokalnej usługi Active Directory zgodnie z oczekiwaniami.

Jeśli nie skonfigurowano stornowanego hasła dla określonego użytkownika lub jeśli użytkownik nie ma przypisanej prawidłowej licencji usługi Azure AD, użytkownik nie może zaktualizować hasła w chmurze. Nie mogą zaktualizować hasła, nawet jeśli ich hasło wygasło. Zamiast tego użytkownik widzi ten komunikat: "Twoja organizacja nie zezwala na aktualizowanie hasła w tej witrynie. Zaktualizuj go zgodnie z metodą zalecaną przez organizację lub zapytaj administratora, czy potrzebujesz pomocy." Użytkownik lub administrator musi zresetować swoje hasło w lokalnej usłudze Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>W jaki sposób uwierzytelnianie przekazywane chroni przed atakami z użyciem hasła?

[Przeczytaj informacje o smart lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co agenci uwierzytelniania przekazywanego komunikują się za pośrednictwem portów 80 i 443?

- Agenci uwierzytelniania żądają protokołu HTTPS za pośrednictwem portu 443 dla wszystkich operacji funkcji.
- Agenci uwierzytelniania żądają http za pośrednictwem portu 80, aby pobrać listy odwołania certyfikatów TLS/SSL (CRL).

     >[!NOTE]
     >Ostatnie aktualizacje zmniejszyły liczbę portów, których wymaga ta funkcja. Jeśli masz starsze wersje usługi Azure AD Connect lub agenta uwierzytelniania, należy również zachować te porty otwarte: 5671, 8080, 9090, 9091, 9350, 9352 i 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Czy agenci uwierzytelniania przekazywania mogą komunikować się za pośrednictwem wychodzącego serwera proxy sieci Web?

Tak. Jeśli funkcja automatycznego odnajdowania serwera proxy sieci Web (WPAD) jest włączona w środowisku lokalnym, agenci uwierzytelniania automatycznie próbują zlokalizować i używać serwera proxy sieci Web w sieci.

Jeśli nie masz WPAD w swoim środowisku, możesz dodać informacje proxy (jak pokazano poniżej), aby umożliwić agentowi uwierzytelniania przekazywania do komunikowania się z usługą Azure AD:
- Skonfiguruj informacje o serwerze proxy w programie Internet Explorer przed zainstalowaniem agenta uwierzytelniania przekazywania na serwerze. Umożliwi to ukończenie instalacji agenta uwierzytelniania, ale nadal będzie ono wyświetlane jako **nieaktywne w** portalu administracyjnym.
- Na serwerze przejdź do pozycji "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Edytuj plik konfiguracyjny "AzureADConnectAuthenticationAgentService" i dodaj\:następujące wiersze (zastąp "http //contosoproxy.com:8080" rzeczywistym adresem serwera proxy):

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

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Czy mogę zainstalować co najmniej dwa agenci uwierzytelniania przekazywania na tym samym serwerze?

Nie, na jednym serwerze można zainstalować tylko jednego agenta uwierzytelniania przekazywania. Jeśli chcesz skonfigurować uwierzytelnianie przekazywane pod kątem wysokiej dostępności, [postępuj zgodnie z instrukcjami tutaj](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Czy muszę ręcznie odnawiać certyfikaty używane przez agentów uwierzytelniania przekazywania?

Komunikacja między każdym agentem uwierzytelniania przekazywania a usługą Azure AD jest zabezpieczona przy użyciu uwierzytelniania opartego na certyfikatach. Certyfikaty te [są automatycznie odnawiane co kilka miesięcy przez usługę Azure AD.](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents) Nie ma potrzeby ręcznego odnawiania tych certyfikatów. W razie potrzeby można wyczyścić starsze wygasłe certyfikaty.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak usunąć agenta uwierzytelniania przekazowego?

Tak długo, jak agent uwierzytelniania przekazywania jest uruchomiony, pozostaje aktywny i stale obsługuje żądania logowania użytkownika. Jeśli chcesz odinstalować agenta uwierzytelniania, przejdź do **Panelu sterowania -> programy > programy i funkcje** i odinstaluj **agenta uwierzytelniania Microsoft Azure AD Connect** i programy **aktualatora agenta usługi Microsoft Azure AD Connect.**

Jeśli po wykonaniu poprzedniego kroku sprawdź blok Uwierzytelnianie przekazywane w [centrum administracyjnym usługi Azure Active Directory,](https://aad.portal.azure.com) zostanie wyświetlony agent uwierzytelniania wyświetlany jako **nieaktywny.** Jest to _oczekiwane_. Agent uwierzytelniania jest automatycznie usuwany z listy po kilku dniach.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Używam już usług AD FS do logowania się do usługi Azure AD. Jak przełączyć go na uwierzytelnianie przekazywane?

W przypadku migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego zdecydowanie zalecamy przestrzeganie opublikowanego [tutaj](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)naszego szczegółowego przewodnika po wdrożeniach.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Czy można używać uwierzytelniania przekazywanego w wielolesowym środowisku usługi Active Directory?

Tak. Środowiska wielu lasów są obsługiwane, jeśli między lasami usługi Active Directory istnieją relacje zaufania lasu (dwukierunkowe) i jeśli routing sufiksu nazw jest poprawnie skonfigurowany.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Czy uwierzytelnianie przekazywane zapewnia równoważenie obciążenia przez wielu agentów uwierzytelniania?

Nie, zainstalowanie wielu agentów uwierzytelniania przekazywanego zapewnia tylko [wysoką dostępność.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Nie zapewnia deterministyczne równoważenia obciążenia między agentami uwierzytelniania. Każdy agent uwierzytelniania (losowo) może przetworzyć określone żądanie logowania użytkownika.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Ile agentów uwierzytelniania przekazywanego muszę zainstalować?

Zainstalowanie wielu agentów uwierzytelniania przekazywanego zapewnia [wysoką dostępność.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Ale nie zapewnia deterministyczne równoważenie obciążenia między agentami uwierzytelniania.

Należy wziąć pod uwagę szczyt i średnie obciążenie żądań logowania, które można oczekiwać, aby zobaczyć w dzierżawie. W ramach punktu odniesienia pojedynczy agent uwierzytelniania może obsługiwać uwierzytelnianie od 300 do 400 na sekundę na standardowym 4-rdzeniowym procesorze CPU o pamięci RAM 16 GB.

Aby oszacować ruch sieciowy, użyj następujących wskazówek dotyczących rozmiaru:
- Każde żądanie ma rozmiar ładunku (0,5 K + 1K * num_of_agents) bajtów; dane z usługi Azure AD do agenta uwierzytelniania. W tym miejscu "num_of_agents" wskazuje liczbę agentów uwierzytelniania zarejestrowanych w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku 1K bajtów; dane z agenta uwierzytelniania do usługi Azure AD.

Dla większości klientów dwóch lub trzech agentów uwierzytelniania w sumie są wystarczające dla wysokiej dostępności i pojemności. Należy zainstalować agentów uwierzytelniania w pobliżu kontrolerów domeny, aby poprawić opóźnienie logowania.

>[!NOTE]
>Istnieje limit systemowy 40 agentów uwierzytelniania na dzierżawę.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Czy mogę zainstalować pierwszego agenta uwierzytelniania przekazywania na serwerze innym niż ten, na który działa usługa Azure AD Connect?

Nie, ten scenariusz _nie_ jest obsługiwany.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Dlaczego potrzebuję konta administratora globalnego tylko w chmurze, aby włączyć uwierzytelnianie przekazywane?

Zaleca się włączenie lub wyłączenie uwierzytelniania przekazywanego przy użyciu konta administratora globalnego tylko w chmurze. Dowiedz się więcej o [dodawaniu konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). W ten sposób gwarantuje, że nie zostaniesz zablokowany z dzierżawy.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak wyłączyć uwierzytelnianie przekazywane?

Uruchom ponownie kreatora usługi Azure AD Connect i zmień metodę logowania użytkownika z uwierzytelniania przekazywania na inną metodę. Ta zmiana wyłącza uwierzytelnianie przekazywane w dzierżawie i odinstalowuje agenta uwierzytelniania z serwera. Należy ręcznie odinstalować agentów uwierzytelniania z innych serwerów.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co się stanie, gdy odinstaluję agenta uwierzytelniania przekazowego?

Jeśli odinstalujesz agenta uwierzytelniania przekazywania z serwera, spowoduje to, że serwer przestanie akceptować żądania logowania. Aby uniknąć złamania możliwości logowania użytkownika w dzierżawie, upewnij się, że masz innego agenta uwierzytelniania uruchomionego przed odinstalowaniem agenta uwierzytelniania przekazywania.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Mam starszą dzierżawę, która została pierwotnie skonfigurowana przy użyciu usług AD FS.  Niedawno zmigrowaliśmy do PTA, ale teraz nie widzimy naszych zmian w usłudze UPN synchronizujących się z usługą Azure AD.  Dlaczego nasze zmiany w upn nie są synchronizowane?

Odp.: W następujących okolicznościach lokalne zmiany w lokalnej sieci UPN mogą nie zostać zsynchronizowane, jeśli:

- Dzierżawa usługi Azure AD została utworzona przed 15 czerwca 2015 r.
- Początkowo byłeś sfederowany z dzierżawą usługi Azure AD przy użyciu usług AD FS do uwierzytelniania
- Przełączony do zarządzania użytkowników przy użyciu PTA jako uwierzytelniania

Jest tak, ponieważ domyślne zachowanie dzierżawców utworzonych przed 15 czerwca 2015 było zablokowanie zmian w upn.  Aby odblokowować zmiany w sieci UPN, należy uruchomić następujące polecenie cmdlt programu PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Dzierżawcy utworzoni po 15 czerwca 2015 r. mają domyślne zachowanie synchronizowania zmian w upn.   



## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, które scenariusze są obsługiwane, a które nie.
- [Szybki start:](how-to-connect-pta-quick-start.md)Wystartuj i działa na podstawie uwierzytelniania przekazywania usługi Azure AD.
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada:](../authentication/howto-password-smart-lockout.md)Dowiedz się, jak skonfigurować funkcję smart lockout w dzierżawie w celu ochrony kont użytkowników.
- [Głębokie głębokie nurkowanie techniczne:](how-to-connect-pta-how-it-works.md)Dowiedz się, jak działa funkcja uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Głębokie skanowanie zabezpieczeń:](how-to-connect-pta-security-deep-dive.md)uzyskaj głębokie informacje techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD:](how-to-connect-sso.md)Dowiedz się więcej o tej uzupełniającej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum usługi Azure Active Directory, aby złożyć nowe żądania funkcji.

