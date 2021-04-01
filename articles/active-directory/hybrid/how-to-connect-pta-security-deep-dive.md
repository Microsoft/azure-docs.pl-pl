---
title: Azure Active Directory głębokiego uwierzytelniania przekazywanie szczegółowe | Microsoft Docs
description: W tym artykule opisano sposób uwierzytelniania przekazywanego przez usługę Azure Active Directory (Azure AD) na kontach lokalnych
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji Active Directory, wymaganych składników usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280149"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory głębokiego uwierzytelniania przekazującego zabezpieczeń szczegółowe

Ten artykuł zawiera bardziej szczegółowy opis sposobu działania uwierzytelniania przekazywanego przez usługę Azure Active Directory (Azure AD). Koncentruje się na aspektach zabezpieczeń funkcji. Ten artykuł dotyczy zabezpieczeń i administratorów systemów INFORMATYCZNych, dyrektorów ds. zgodności i zabezpieczeń oraz innych informatyków, którzy są odpowiedzialni za bezpieczeństwo IT i zgodność z niewielkimi i średnimi organizacjami lub dużymi przedsiębiorstwami.

Tematy poświęcone:
- Szczegółowe informacje techniczne na temat instalowania i rejestrowania agentów uwierzytelniania.
- Szczegółowe informacje techniczne na temat szyfrowania haseł podczas logowania użytkownika.
- Zabezpieczenia kanałów między lokalnymi agentami uwierzytelniania i usługą Azure AD.
- Szczegółowe informacje techniczne na temat sposobu utrzymywania bezpiecznego działania agentów uwierzytelniania.
- Inne tematy związane z zabezpieczeniami.

## <a name="key-security-capabilities"></a>Kluczowe możliwości zabezpieczeń

Oto kluczowe aspekty zabezpieczeń tej funkcji:
- Jest on oparty na bezpiecznej architekturze z wieloma dzierżawcami, która zapewnia izolację żądań logowania między dzierżawcami.
- Hasła lokalne nigdy nie są przechowywane w chmurze w żadnej postaci.
- Agenci uwierzytelniania lokalnego, którzy nasłuchują i odpowiadają na żądania weryfikacji haseł, umożliwiają tylko połączenia wychodzące z sieci. Nie jest wymagane, aby zainstalować tych agentów uwierzytelniania w sieci obwodowej (DMZ). Najlepszym rozwiązaniem jest traktowanie wszystkich serwerów, na których są uruchomione agenci uwierzytelniania jako systemy warstwy 0 (zobacz [odwołanie](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Do komunikacji wychodzącej z agentów uwierzytelniania do usługi Azure AD są używane tylko porty standardowe (80 i 443). Nie trzeba otwierać portów przychodzących w zaporze. 
  - Port 443 jest używany do całej uwierzytelnionej komunikacji wychodzącej.
  - Port 80 jest używany tylko do pobierania list odwołania certyfikatów (CRL), aby upewnić się, że żaden z certyfikatów używanych przez tę funkcję nie został odwołany.
  - Aby zapoznać się z pełną listą wymagań sieci, zobacz [Azure Active Directory uwierzytelniania przekazywanego: Szybki Start](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Hasła zapewniane przez użytkowników podczas logowania są szyfrowane w chmurze przed zaakceptowaniem przez agentów lokalnego uwierzytelniania do walidacji w odniesieniu do Active Directory.
- Kanał HTTPS między usługą Azure AD a agentem uwierzytelniania lokalnego jest zabezpieczony przy użyciu wzajemnego uwierzytelniania.
- Chroni Twoje konta użytkowników, bezproblemowo korzystając z [zasad dostępu warunkowego usługi Azure AD](../conditional-access/overview.md), w tym Multi-Factor Authentication (MFA), [blokując starsze uwierzytelnianie](../conditional-access/concept-conditional-access-conditions.md) i przez [odfiltrowanie ataków z hasłami](../authentication/howto-password-smart-lockout.md)bezprawnego.

## <a name="components-involved"></a>Uwzględnione składniki

Aby uzyskać ogólne informacje na temat działania, usługi i bezpieczeństwa danych usługi Azure AD, zobacz [Centrum zaufania](https://azure.microsoft.com/support/trust-center/). Poniższe składniki są używane w przypadku korzystania z uwierzytelniania przekazywanego podczas logowania użytkownika:
- **Azure AD STS**: bezstanowa usługa tokenu zabezpieczającego (STS), która przetwarza żądania logowania i wystawia tokeny zabezpieczające dla przeglądarek, klientów lub usług użytkowników, zgodnie z potrzebami.
- **Azure Service Bus**: zapewnia komunikację w chmurze z obsługą komunikatów w przedsiębiorstwie i przekaźnikiem komunikacji, która ułatwia łączenie rozwiązań lokalnych z chmurą.
- **Agent uwierzytelniania Azure AD Connect**: składnik lokalny, który nasłuchuje i reaguje na żądania weryfikacji hasła.
- **Azure SQL Database**: zawiera informacje o agentach uwierzytelniania dzierżawy, w tym ich metadanych i kluczy szyfrowania.
- **Active Directory**: lokalna Active Directory, w której są przechowywane konta użytkowników i ich hasła.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalacja i rejestracja agentów uwierzytelniania

Agenci uwierzytelniania są instalowani i zarejestrowani w usłudze Azure AD, gdy:
   - [Włącz uwierzytelnianie przekazywane za Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Dodaj więcej agentów uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
Pobieranie działającego agenta uwierzytelniania obejmuje trzy główne etapy:

1. Instalacja agenta uwierzytelniania
2. Rejestracja agenta uwierzytelniania
3. Inicjowanie agenta uwierzytelniania

W poniższych sekcjach szczegółowo omówiono te etapy.

### <a name="authentication-agent-installation"></a>Instalacja agenta uwierzytelniania

Tylko Administratorzy globalni mogą instalować agenta uwierzytelniania (przy użyciu Azure AD Connect lub autonomicznego) na serwerze lokalnym. Instalacja dodaje dwa nowe pozycje w **Panelu sterowania**  >  lista **programy programy**  >  **i funkcje** :
- Sama aplikacja agenta uwierzytelniania. Ta aplikacja działa z uprawnieniami [NetworkService](/windows/win32/services/networkservice-account) .
- Aplikacja Aktualizator, która służy do autoaktualizacji agenta uwierzytelniania. Ta aplikacja jest uruchamiana z uprawnieniami [LocalSystem](/windows/win32/services/localsystem-account) .

>[!IMPORTANT]
>Z punktu widzenia zabezpieczeń Administratorzy powinni traktować serwer z uruchomionym agentem PTA tak, jakby był kontrolerem domeny.  Serwery agenta PTA powinny być wzmocnione w tych samych wierszach, co w przypadku [zabezpieczania kontrolerów domeny przed atakami](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="authentication-agent-registration"></a>Rejestracja agenta uwierzytelniania

Po zainstalowaniu agenta uwierzytelniania należy zarejestrować się w usłudze Azure AD. Usługa Azure AD przypisuje każdemu agentowi uwierzytelniania unikatowy certyfikat cyfrowy, który może być używany do bezpiecznej komunikacji z usługą Azure AD.

Procedura rejestracji wiąże także agenta uwierzytelniania z dzierżawcą. Dzięki temu usługa Azure AD wie, że ten określony Agent uwierzytelniania jest jedynym autoryzowanym do obsługi żądań weryfikacji hasła dla dzierżawy. Ta procedura jest powtarzana dla każdego nowego zarejestrowanego agenta uwierzytelniania.

Agenci uwierzytelniania używają następujących kroków, aby zarejestrować się w usłudze Azure AD:

![Rejestracja agenta](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Usługa Azure AD najpierw żąda, aby administrator globalny zalogować się do usługi Azure AD przy użyciu swoich poświadczeń. Podczas logowania Agent uwierzytelniania uzyskuje token dostępu, który może być używany w imieniu administratora globalnego.
2. Następnie Agent uwierzytelniania generuje parę kluczy: klucz publiczny i klucz prywatny.
    - Para kluczy jest generowana przy użyciu standardowego RSA 2048-bitowego szyfrowania.
    - Klucz prywatny pozostaje na serwerze lokalnym, na którym znajduje się Agent uwierzytelniania.
3. Agent uwierzytelniania wysyła żądanie rejestracji do usługi Azure AD za pośrednictwem protokołu HTTPS, z następującymi składnikami uwzględnionymi w żądaniu:
    - Token dostępu uzyskany w kroku 1.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). To żądanie dotyczy certyfikatu tożsamości cyfrowej z usługą Azure AD jako urzędu certyfikacji.
4. Usługa Azure AD weryfikuje token dostępu w żądaniu rejestracji i weryfikuje, czy żądanie pochodzi od administratora globalnego.
5. Usługa Azure AD następnie podpisuje i wyśle certyfikat tożsamości cyfrowej z powrotem do agenta uwierzytelniania.
    - Główny urząd certyfikacji w usłudze Azure AD jest używany do podpisywania certyfikatu. 

      > [!NOTE]
      > Ten urząd certyfikacji _nie_ znajduje się w magazynie zaufanych głównych urzędów certyfikacji systemu Windows.
    - Urząd certyfikacji jest używany tylko przez funkcję uwierzytelniania przekazywanego. Urząd certyfikacji jest używany tylko do podpisywania tych przedstawicieli w trakcie rejestracji agenta uwierzytelniania.
    -  Żadna z pozostałych usług Azure AD nie korzysta z tego urzędu certyfikacji.
    - Podmiot certyfikatu (nazwa wyróżniająca lub DN) ma ustawiony identyfikator dzierżawy. Ta nazwa wyróżniająca jest identyfikatorem GUID, który jednoznacznie identyfikuje dzierżawcę. Ta nazwa DN zakresów certyfikatu do użycia tylko z dzierżawcą.
6. Usługa Azure AD przechowuje klucz publiczny agenta uwierzytelniania w bazie danych programu Azure SQL Database, do której ma dostęp tylko usługa Azure AD.
7. Certyfikat (wydany w kroku 5) jest przechowywany na serwerze lokalnym w magazynie certyfikatów systemu Windows (w zależności od lokalizacji [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) ). Jest on używany zarówno przez agenta uwierzytelniania, jak i aplikacje Aktualizator.

### <a name="authentication-agent-initialization"></a>Inicjowanie agenta uwierzytelniania

Gdy zostanie uruchomiony Agent uwierzytelniania po raz pierwszy po rejestracji lub po ponownym uruchomieniu serwera, musi on bezpiecznie komunikować się z usługą Azure AD i rozpocząć akceptowanie żądań weryfikacji hasła.

![Inicjowanie agenta](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Oto jak są inicjowane agenci uwierzytelniania:

1. Agent uwierzytelniania wykonuje wychodzące żądanie Bootstrap do usługi Azure AD. 
    - To żądanie jest realizowane za pośrednictwem portu 443 i znajduje się za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS. Żądanie używa tego samego certyfikatu, który został wystawiony podczas rejestracji agenta uwierzytelniania.
2. Usługa Azure AD reaguje na żądanie, dostarczając klucz dostępu do kolejki Azure Service Bus, która jest unikatowa dla dzierżawy i jest identyfikowana przez identyfikator dzierżawy.
3. Agent uwierzytelniania wykonuje stałe wychodzące połączenie HTTPS (przez port 443) do kolejki. 
    - Agent uwierzytelniania jest teraz gotowy do pobierania i obsługi żądań weryfikacji haseł.

Jeśli w dzierżawie zarejestrowano wielu agentów uwierzytelniania, procedura inicjowania zapewnia, że każda z nich nawiązuje połączenie z tą samą kolejką Service Bus. 

## <a name="process-sign-in-requests"></a>Przetwarzanie żądań logowania 

Na poniższym diagramie pokazano, jak uwierzytelnianie przekazywane przetwarza żądania logowania użytkownika.

![Proces logowania](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Uwierzytelnianie przekazywane obsługuje żądanie logowania użytkownika w następujący sposób: 

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [programu Outlook Web App](https://outlook.office365.com/owa).
2. Jeśli użytkownik nie jest jeszcze zalogowany, aplikacja przekierowuje przeglądarkę do strony logowania usługi Azure AD.
3. Usługa Azure AD STS reaguje z powrotem na stronę **logowania użytkownika** .
4. Użytkownik wprowadza swoją nazwę użytkownika na stronie **logowania użytkownika** , a następnie wybiera przycisk **dalej** .
5. Użytkownik wprowadza hasło do strony **logowania użytkownika** , a następnie wybiera przycisk **Zaloguj** .
6. Nazwa użytkownika i hasło są przesyłane do usługi Azure AD STS w żądaniu POST protokołu HTTPS.
7. Usługa Azure AD STS pobiera klucze publiczne dla wszystkich agentów uwierzytelniania zarejestrowanych w dzierżawie z Azure SQL Database i szyfruje hasło przy ich użyciu.
    - Tworzy "N" zaszyfrowane wartości haseł dla agentów uwierzytelniania "N" zarejestrowanych w dzierżawie.
8. Usługa Azure AD STS umieszcza żądanie weryfikacji hasła, które składa się z nazwy użytkownika i zaszyfrowanej wartości hasła, do kolejki Service Bus określonej dla Twojej dzierżawy.
9. Ponieważ zainicjowane agenci uwierzytelniania są trwale połączeni z kolejką Service Bus, jeden z dostępnych agentów uwierzytelniania pobiera żądanie weryfikacji hasła.
10. Agent uwierzytelniania lokalizuje wartość hasła szyfrowanego, która jest specyficzna dla swojego klucza publicznego, przy użyciu identyfikatora i odszyfrowuje ją przy użyciu klucza prywatnego.
11. Agent uwierzytelniania próbuje sprawdzić poprawność nazwy użytkownika i hasła do Active Directory lokalnego przy użyciu [interfejsu API Win32 funkcji LogonUser](/windows/win32/api/winbase/nf-winbase-logonusera) z parametrem **dwLogonType** ustawionym na **LOGON32_LOGON_NETWORK**. 
    - Ten interfejs API jest tym samym interfejsem API, który jest używany przez Active Directory Federation Services (AD FS) do logowania użytkowników w federacyjnym scenariuszu logowania.
    - Ten interfejs API opiera się na standardowym procesie rozpoznawania w systemie Windows Server w celu zlokalizowania kontrolera domeny.
12. Agent uwierzytelniania otrzymuje wynik z Active Directory, na przykład nieprawidłowe, nazwa użytkownika lub hasło, lub hasło wygasło.

   > [!NOTE]
   > Jeśli Agent uwierzytelniania zakończy się niepowodzeniem w trakcie procesu logowania, cała prośba o zalogowanie zostanie porzucona. Nie są dostępne żadne żądania logowania z jednego agenta uwierzytelniania do innego agenta uwierzytelniania w środowisku lokalnym. Ci agenci komunikują się tylko z chmurą, a nie ze sobą.
   
13. Agent uwierzytelniania przekazuje wynik z powrotem do usługi Azure AD STS za pośrednictwem wychodzącego wzajemnie uwierzytelnionego kanału HTTPS na porcie 443. Uwierzytelnianie wzajemne używa certyfikatu wystawionego wcześniej dla agenta uwierzytelniania podczas rejestracji.
14. Usługa Azure AD STS weryfikuje, czy ten wynik jest skorelowany z konkretnym żądaniem logowania w dzierżawie.
15. Usługa Azure AD STS kontynuuje działanie zgodnie z konfiguracją. Na przykład jeśli sprawdzanie poprawności hasła zakończyło się pomyślnie, użytkownik może zakwestionować Multi-Factor Authentication lub przekierować z powrotem do aplikacji.

## <a name="operational-security-of-the-authentication-agents"></a>Bezpieczeństwo operacyjne agentów uwierzytelniania

Aby zapewnić, że uwierzytelnianie przekazywane pozostaje bezpieczne, usługa Azure AD okresowo odnawia certyfikaty agentów uwierzytelniania. Usługa Azure AD wyzwala odnowienia. Odnowienia nie podlegają samym agentom uwierzytelniania.

![Bezpieczeństwo działania](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Aby odnowić zaufanie agenta uwierzytelniania za pomocą usługi Azure AD:

1. Agent uwierzytelniania okresowo wysyła polecenie ping do usługi Azure AD co kilka godzin, aby sprawdzić, czy czas odnawiania certyfikatu. Certyfikat jest odnawiany na 30 dni przed jego wygaśnięciem.
    - To sprawdzenie odbywa się za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS i używa tego samego certyfikatu, który został wystawiony podczas rejestracji.
2. Jeśli usługa wskazuje, że czas odnawiania, Agent uwierzytelniania generuje nową parę kluczy: klucz publiczny i klucz prywatny.
    - Klucze te są generowane przy użyciu standardowego RSA 2048-bitowego szyfrowania.
    - Klucz prywatny nigdy nie opuszcza serwera lokalnego.
3. Następnie Agent uwierzytelniania wysyła żądanie "odnowienia certyfikatu" do usługi Azure AD za pośrednictwem protokołu HTTPS z następującymi składnikami uwzględnionymi w żądaniu:
    - Istniejący certyfikat pobrany z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE w magazynie certyfikatów systemu Windows. W tej procedurze nie ma żadnego administratora globalnego, dlatego nie jest wymagany token dostępu w imieniu administratora globalnego.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). To żądanie dotyczy nowego certyfikatu tożsamości cyfrowej z usługą Azure AD jako urzędu certyfikacji.
4. Usługa Azure AD weryfikuje istniejący certyfikat w żądaniu odnowienia certyfikatu. Następnie sprawdza, czy żądanie pochodzi od agenta uwierzytelniania zarejestrowanego w dzierżawie.
5. Jeśli istniejący certyfikat jest nadal ważny, usługa Azure AD następnie podpisuje nowy certyfikat tożsamości cyfrowej i wystawia nowy certyfikat z powrotem do agenta uwierzytelniania. 
6. Jeśli istniejący certyfikat wygasł, usługa Azure AD usunie agenta uwierzytelniania z listy zarejestrowanych agentów uwierzytelniania. Następnie Administrator globalny musi ręcznie zainstalować i zarejestrować nowego agenta uwierzytelniania.
    - Aby podpisać certyfikat, użyj głównego urzędu certyfikacji usługi Azure AD.
    - Ustaw podmiot certyfikatu (nazwę wyróżniającą lub DN) na identyfikator dzierżawy, identyfikator GUID, który jednoznacznie identyfikuje dzierżawcę. Nazwa DN zakresy certyfikatu tylko dla dzierżawy.
6. Usługa Azure AD przechowuje nowy klucz publiczny agenta uwierzytelniania w bazie danych programu Azure SQL Database, do której ma dostęp tylko do programu. Unieważnia także stary klucz publiczny skojarzony z agentem uwierzytelniania.
7. Nowy certyfikat (wydany w kroku 5) jest następnie przechowywany na serwerze w magazynie certyfikatów systemu Windows (w zależności od lokalizacji [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) ).
    - Ponieważ procedura odnawiania zaufania nie jest interaktywna (bez obecności administratora globalnego), Agent uwierzytelniania nie ma już dostępu do aktualizowania istniejącego certyfikatu w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Ta procedura nie usuwa samego certyfikatu z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nowy certyfikat jest używany do uwierzytelniania z tego punktu w systemie. Każde kolejne odnowienie certyfikatu zastępuje certyfikat w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Autoaktualizacja agentów uwierzytelniania

Aplikacja Aktualizator automatycznie aktualizuje agenta uwierzytelniania, gdy zostanie wydana nowa wersja (z poprawkami błędów lub ulepszeniami wydajności). Aplikacja Aktualizator nie obsługuje żadnych żądań weryfikacji hasła dla Twojej dzierżawy.

Usługa Azure AD obsługuje nową wersję oprogramowania jako podpisany **Instalator Windows pakiet (msi)**. Plik MSI jest podpisany przy użyciu [technologii Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) z SHA256 jako algorytmem Digest. 

![Aktualizuj aktualizację](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Aby zaktualizować agenta uwierzytelniania:

1. Aplikacja Aktualizator wysyła polecenie ping do usługi Azure AD co godzinę, aby sprawdzić, czy jest dostępna nowa wersja agenta uwierzytelniania. 
    - To sprawdzenie odbywa się za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS za pomocą tego samego certyfikatu, który został wystawiony podczas rejestracji. Agent uwierzytelniania i Aktualizator korzystają z certyfikatu przechowywanego na serwerze.
2. Jeśli nowa wersja jest dostępna, usługa Azure AD zwraca podpisany plik MSI z powrotem do Aktualizator.
3. Aktualizator sprawdza, czy plik MSI jest podpisany przez firmę Microsoft.
4. Aktualizator uruchamia plik MSI. Ta akcja obejmuje następujące kroki:

   > [!NOTE]
   > Aktualizator jest uruchamiany z uprawnieniami [systemu lokalnego](/windows/win32/services/localsystem-account) .

    - Powoduje zatrzymanie usługi agenta uwierzytelniania
    - Instaluje nową wersję agenta uwierzytelniania na serwerze
    - Ponownie uruchamia usługę agenta uwierzytelniania.

>[!NOTE]
>Jeśli w dzierżawie zarejestrowano wielu agentów uwierzytelniania, usługa Azure AD nie odnawia certyfikatów ani nie aktualizuje ich w tym samym czasie. Zamiast tego usługa Azure AD robi to pojedynczo, aby zapewnić wysoką dostępność żądań logowania.
>


## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): informacje o scenariuszach, które są obsługiwane i które nie są.
- [Szybki Start](how-to-connect-pta-quick-start.md): Rozpoczynanie pracy przy użyciu uwierzytelniania przekazywanego usługi Azure AD.
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadpdownload) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md): Skonfiguruj funkcję inteligentnego blokowania w dzierżawie, aby chronić konta użytkowników.
- [Jak to działa](how-to-connect-pta-how-it-works.md): Poznaj podstawowe informacje na temat sposobu działania uwierzytelniania przekazywanego przez usługę Azure AD.
- [Często zadawane pytania](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): informacje na temat rozwiązywania typowych problemów z funkcją uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne w usłudze Azure AD](how-to-connect-sso.md): Dowiedz się więcej o tej funkcji uzupełniającej.