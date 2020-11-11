---
title: Uwierzytelnianie — Microsoft Threat Modeling Tool — Azure | Microsoft Docs
description: Informacje na temat łagodzenia uwierzytelniania w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: e9a1afd1d998fcb3ba715c890cc4deac1f0a7da5
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517720"
---
# <a name="security-frame-authentication--mitigations"></a>Ramka zabezpieczeń: uwierzytelnianie | Środki zaradcze

| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web**    | <ul><li>[Rozważ użycie standardowego mechanizmu uwierzytelniania do uwierzytelniania w aplikacji sieci Web](#standard-authn-web-app)</li><li>[Aplikacje muszą obsługiwać bezpieczne scenariusze dotyczące niepowodzenia uwierzytelniania](#handle-failed-authn)</li><li>[Włącz uwierzytelnianie krok po kroku lub adaptacyjne](#step-up-adaptive-authn)</li><li>[Upewnij się, że interfejsy administracyjne są odpowiednio zablokowane.](#admin-interface-lockdown)</li><li>[Bezpieczne Implementowanie funkcji zapomnianego hasła](#forgot-pword-fxn)</li><li>[Upewnij się, że zasady haseł i kont zostały zaimplementowane](#pword-account-policy)</li><li>[Implementowanie formantów w celu zapobiegania wyliczaniu nazw użytkowników](#controls-username-enum)</li></ul> |
| **Database** (Baza danych) | <ul><li>[Jeśli to możliwe, Użyj uwierzytelniania systemu Windows w celu nawiązania połączenia z usługą SQL Server](#win-authn-sql)</li><li>[Gdy jest to możliwe, Użyj uwierzytelniania Azure Active Directory w celu nawiązania połączenia z SQL Database](#aad-authn-sql)</li><li>[Gdy jest używany tryb uwierzytelniania SQL, upewnij się, że zasady konta i hasła są wymuszane w programie SQL Server](#authn-account-pword)</li><li>[Nie używaj uwierzytelniania SQL w zawartych bazach danych](#autn-contained-db)</li></ul> |
| **Centrum zdarzeń Azure** | <ul><li>[Użyj poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS](#authn-sas-tokens)</li></ul> |
| **Granica zaufania platformy Azure** | <ul><li>[Włączanie usługi Azure Multi-Factor Authentication dla administratorów platformy Azure](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric granic zaufania** | <ul><li>[Ograniczanie dostępu anonimowego do klastra Service Fabric](#anon-access-cluster)</li><li>[Upewnij się, że Service Fabric certyfikat klienta-węzeł różni się od certyfikatu węzła-węzeł](#fabric-cn-nn)</li><li>[Uwierzytelnianie klientów w klastrach usługi Service Fabric za pomocą usługi AAD](#aad-client-fabric)</li><li>[Upewnij się, że certyfikaty usługi Service Fabric są uzyskiwane z zatwierdzonego urzędu certyfikacji (CA)](#fabric-cert-ca)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Używaj standardowych scenariuszy uwierzytelniania obsługiwanych przez serwer tożsamości](#standard-authn-id)</li><li>[Zastąp domyślną pamięć podręczną tokenów serwera tożsamości z skalowalną alternatywą](#override-token)</li></ul> |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Włączanie uwierzytelniania podczas nawiązywania połączenia z kolejkami usługi MSMQ w programie WCF](#msmq-queues)</li><li>[WCF — nie ustawiaj komunikatu clientCredentialtype na none](#message-none)</li><li>[WCF — nie ustawiaj transportowej wartości clientCredentialtype na none](#transport-none)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że standardowe techniki uwierzytelniania są używane do zabezpieczania interfejsów API sieci Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Używaj standardowych scenariuszy uwierzytelniania obsługiwanych przez Azure Active Directory](#authn-aad)</li><li>[Zastąp domyślną pamięć podręczną tokenów ADAL skalowalną alternatywą](#adal-scalable)</li><li>[Upewnij się, że TokenReplayCache jest używany do zapobiegania powtarzaniu tokenów uwierzytelniania ADAL](#tokenreplaycache-adal)</li><li>[Korzystanie z bibliotek ADAL do zarządzania żądaniami tokenów od klientów OAuth2 do usługi AAD (lub w lokalnej usłudze AD)](#adal-oauth2)</li></ul> |
| **Brama pola IoT** | <ul><li>[Uwierzytelnianie urządzeń łączących się z bramą pola](#authn-devices-field)</li></ul> |
| **Brama usługi IoT Cloud** | <ul><li>[Upewnij się, że urządzenia łączące się z bramą w chmurze są uwierzytelniane](#authn-devices-cloud)</li><li>[Użyj poświadczeń uwierzytelniania dla urządzenia](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że tylko wymagane kontenery i obiekty blob mają anonimowy dostęp do odczytu](#req-containers-anon)</li><li>[Udzielanie ograniczonego dostępu do obiektów w usłudze Azure Storage przy użyciu SAS lub SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Rozważ użycie standardowego mechanizmu uwierzytelniania do uwierzytelniania w aplikacji sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Uwierzytelnianie to proces, w którym jednostka udowadnia swoją tożsamość, zwykle za pomocą poświadczeń, takich jak nazwa użytkownika i hasło. Istnieje wiele dostępnych protokołów uwierzytelniania, które mogą być brane pod uwagę. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na podstawie systemu Windows</li><li>Oparte na formularzach</li><li>Federacja — ADFS</li><li>Federacja — Azure AD</li><li>Federacyjny serwer tożsamości</li></ul><p>Rozważ użycie standardowego mechanizmu uwierzytelniania do identyfikowania procesu źródłowego</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Aplikacje muszą obsługiwać bezpieczne scenariusze dotyczące niepowodzenia uwierzytelniania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Aplikacje, które jawnie uwierzytelniają użytkowników, muszą bezpiecznie obsłużyć scenariusze niepowodzenia uwierzytelniania. Mechanizm uwierzytelniania musi:</p><ul><li>Odmowa dostępu do zasobów uprzywilejowanych, gdy uwierzytelnianie nie powiedzie się</li><li>Wyświetl ogólny komunikat o błędzie po nieudanym uwierzytelnieniu i wystąpieniu odmowy dostępu</li></ul><p>Test dla:</p><ul><li>Ochrona uprzywilejowanych zasobów po nieudanych próbach logowania</li><li>Ogólny komunikat o błędzie jest wyświetlany w przypadku niepowodzenia uwierzytelniania i zdarzeń odmowy dostępu</li><li>Konta są wyłączone po nadmiernej liczbie nieudanych prób</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Włącz uwierzytelnianie krok po kroku lub adaptacyjne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Sprawdź, czy aplikacja ma dodatkowe uprawnienia (takie jak krokowe lub adaptacyjne uwierzytelnianie, za pomocą uwierzytelniania wieloskładnikowego, takiego jak wysyłanie OTP w wiadomościach SMS, wiadomości e-mail itp. lub monitowanie o ponowne uwierzytelnienie), aby użytkownik był wzywany przed udzieleniem dostępu do poufnych informacji. Ta reguła ma zastosowanie również do wprowadzania zmian krytycznych do konta lub akcji</p><p>Oznacza to również, że dostosowanie uwierzytelniania musi być zaimplementowane w taki sposób, że aplikacja poprawnie wymusza autoryzację kontekstową, tak aby nie umożliwiała nieautoryzowanego manipulowania przy użyciu na przykład naruszenia parametrów</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Upewnij się, że interfejsy administracyjne są odpowiednio zablokowane.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | Pierwszym rozwiązaniem jest przyznanie dostępu tylko z określonego źródłowego zakresu adresów IP do interfejsu administracyjnego. Jeśli to rozwiązanie nie będzie możliwe, niż jest zawsze zalecane, aby wymusić podwyższenie lub adaptacyjne uwierzytelnianie do logowania w interfejsie administracyjnym |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Bezpieczne Implementowanie funkcji zapomnianego hasła

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Pierwszym krokiem jest zweryfikowanie, że zapomniane hasło i inne ścieżki odzyskiwania wysyłają łącze, w tym token aktywacji ograniczony czas zamiast samego hasła. Dodatkowe uwierzytelnianie oparte na tokenach miękkich (np. token programu SMS, natywne aplikacje mobilne itp.) może być również wymagane przed wysłaniem linku. Po drugie nie należy blokować konta użytkowników, gdy trwa proces uzyskiwania nowego hasła.</p><p>Może to prowadzić do ataku typu "odmowa usługi", gdy osoba atakująca zdecyduje się celowo zablokowaniu użytkowników przy użyciu automatycznego ataku. Trzeci, za każdym razem, gdy nowe żądanie hasła zostało ustawione w toku, wyświetlany komunikat powinien zostać uogólniony, aby zapobiec wyliczaniu nazw użytkowników. Czwarty, zawsze nie Zezwalaj na używanie starych haseł i Implementuj zasady silnego hasła.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Upewnij się, że zasady haseł i kont zostały zaimplementowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Należy zaimplementować zasady dotyczące haseł i kont zgodnie z zasadami organizacji i najlepszymi rozwiązaniami.</p><p>Aby chronić przed rozpuszczeniem i odzgadywaniem słownika: należy zaimplementować zasady silnego hasła, aby zapewnić użytkownikom możliwość tworzenia złożonych haseł (np. 12 znaków, znaków alfanumerycznych i specjalnych).</p><p>Zasady blokady konta mogą być implementowane w następujący sposób:</p><ul><li>**Niewygładzone Blokowanie:** Może to być dobra opcja w celu ochrony użytkowników przed atakami polegającymi na rozdzieleniu. Na przykład za każdym razem, gdy użytkownik wprowadzi nieprawidłowe hasło trzykrotnie, aplikacja może zablokować konto przez minutę w celu spowolnienia procesu rozrzucania hasła, co zmniejsza opłacalność działania osoby atakującej. Jeśli w tym przykładzie zaimplementowano środki zaradcze dla trwałego blokowania, można uzyskać "system DoS", trwale blokując konta. Alternatywnie aplikacja może generować uwierzytelnianie OTP (jednorazowe hasło) i wysyłać je poza pasmem (za pośrednictwem poczty e-mail, wiadomości SMS itp.) do użytkownika. Innym rozwiązaniem może być wdrożenie CAPTCHA po osiągnięciu progu liczby nieudanych prób.</li><li>**Twarda blokada:** Ten typ blokady powinien zostać zastosowany za każdym razem, gdy wykryjesz użytkownika, który atakuje aplikację, a następnie przeniesiesz je przez trwałe zablokowanie swojego konta do momentu, gdy zespół odpowiedzi zakończył dowodowych. Po wykonaniu tego procesu możesz zdecydować, aby przekazać użytkownikowi kopię zapasową swojego konta, lub podjąć w nim dalsze działania prawne. Ten typ podejścia uniemożliwia osobie atakującej dalsze przechodzą do aplikacji i infrastruktury.</li></ul><p>Aby chronić przed atakami na konta domyślne i przewidywalne, sprawdź, czy wszystkie klucze i hasła są wymienne i są generowane lub zastępowane po upływie czasu instalacji.</p><p>Jeśli aplikacja ma generować automatyczne generowanie haseł, należy się upewnić, że wygenerowane hasła są losowo i mają wysoką entropię.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementowanie formantów w celu zapobiegania wyliczaniu nazw użytkowników

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wszystkie komunikaty o błędach powinny być uogólnione w celu uniknięcia wyliczenia nazw użytkowników. Czasami nie można uniknąć wycieku informacji w funkcjach, takich jak Strona rejestracji. W tym miejscu należy użyć metod ograniczania szybkości, takich jak CAPTCHA, aby zapobiec automatycznemu atakowi osoby atakującej. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Jeśli to możliwe, Użyj uwierzytelniania systemu Windows w celu nawiązania połączenia z usługą SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Lokalny |
| **Atrybuty**              | Wersja SQL — wszystkie |
| **Odwołania**              | [SQL Server — wybierz tryb uwierzytelniania](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Kroki** | Uwierzytelnianie systemu Windows używa protokołu zabezpieczeń Kerberos, zapewnia wymuszanie zasad haseł w odniesieniu do sprawdzania złożoności silnych haseł, zapewnia obsługę blokady konta oraz obsługuje wygasanie haseł.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Gdy jest to możliwe, Użyj uwierzytelniania Azure Active Directory w celu nawiązania połączenia z SQL Database

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL — V12 |
| **Odwołania**              | [Nawiązywanie połączenia z SQL Database przy użyciu uwierzytelniania Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md) |
| **Kroki** | **Minimalna wersja:** Azure SQL Database V12 wymagany do zezwalania Azure SQL Database na używanie uwierzytelniania usługi AAD w odniesieniu do katalogu Microsoft |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Gdy jest używany tryb uwierzytelniania SQL, upewnij się, że zasady konta i hasła są wymuszane w programie SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zasady haseł SQL Server](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Kroki** | W przypadku korzystania z uwierzytelniania SQL Server nazwy logowania są tworzone w SQL Server, które nie są oparte na kontach użytkowników systemu Windows. Zarówno nazwa użytkownika, jak i hasło są tworzone przy użyciu SQL Server i przechowywane w SQL Server. SQL Server mogą korzystać z mechanizmów zasad haseł systemu Windows. Może stosować te same zasady złożoności i wygasania używane w systemie Windows do haseł używanych wewnątrz SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Nie używaj uwierzytelniania SQL w zawartych bazach danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Lokalnego, SQL Azure |
| **Atrybuty**              | SQL Version-MSSQL2012, SQL Version-V12 |
| **Odwołania**              | [Najlepsze rozwiązania w zakresie zabezpieczeń z zawartymi bazami danych](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Kroki** | Brak wymuszanych zasad haseł może zwiększyć prawdopodobieństwo ustanowienia słabego poświadczenia w zawartej bazie danych. Skorzystaj z uwierzytelniania systemu Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Użyj poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie modelu uwierzytelniania i zabezpieczeń Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroki** | <p>Model zabezpieczeń Event Hubs jest oparty na kombinacji tokenów sygnatury dostępu współdzielonego (SAS) i wydawców zdarzeń. Nazwa wydawcy reprezentuje identyfikator DeviceID, który odbiera token. Pomoże to skojarzyć tokeny wygenerowane z odpowiednimi urządzeniami.</p><p>Wszystkie komunikaty są otagowane przy użyciu programu inicjującego po stronie usługi, umożliwiając wykrywanie prób podania źródła w ładunku. Podczas uwierzytelniania urządzeń Wygeneruj token sygnatury dostępu współdzielonego na urządzenie w zakresie unikatowego wydawcy.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Włączanie usługi Azure Multi-Factor Authentication dla administratorów platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania platformy Azure |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Co to jest usługa Azure Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Kroki** | <p>Uwierzytelnianie wieloskładnikowe (MFA) to metoda uwierzytelniania wymagająca więcej niż jednej metody weryfikacji i zwiększania krytycznej drugiej warstwy zabezpieczeń do logowania i transakcji użytkownika. Działa ono, wymagając co najmniej dwóch następujących metod weryfikacji:</p><ul><li>Coś, co wiesz (zazwyczaj hasło)</li><li>Coś, co masz (zaufane urządzenie, które nie jest łatwo duplikowane, takie jak telefon)</li><li>Coś, co masz (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Ograniczanie dostępu anonimowego do klastra Service Fabric

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure  |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra Service Fabric](../../service-fabric/service-fabric-cluster-security.md) |
| **Kroki** | <p>Klaster powinien zawsze być zabezpieczony, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się z klastrem, zwłaszcza gdy ma uruchomione na nim obciążenia produkcyjne.</p><p>Podczas tworzenia klastra usługi Service Fabric upewnij się, że tryb zabezpieczeń jest ustawiony na wartość "Secure" i skonfiguruj wymagany certyfikat serwera X. 509. Utworzenie klastra "niezabezpieczony" zezwoli każdemu użytkownikowi Anonimowemu na nawiązanie z nim połączenia, jeśli ujawnia on punkty końcowe zarządzania do publicznego Internetu.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Upewnij się, że Service Fabric certyfikat klienta-węzeł różni się od certyfikatu węzła-węzeł

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — platforma Azure, środowisko autonomiczne |
| **Odwołania**              | [Service Fabric zabezpieczenia certyfikatu klienta do węzła](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security), Połącz się [z bezpiecznym klastrem przy użyciu certyfikatu klienta](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Kroki** | <p>Zabezpieczenia certyfikatów klient-węzeł są konfigurowane podczas tworzenia klastra za pomocą Azure Portal, Menedżer zasobów szablonów lub autonomicznego szablonu JSON, określając certyfikat klienta i/lub certyfikat klienta użytkownika.</p><p>Określone certyfikaty klienta administracyjnego i klienta użytkownika powinny być inne niż certyfikaty podstawowe i pomocnicze określone dla zabezpieczeń między węzłami.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Uwierzytelnianie klientów w klastrach usługi Service Fabric za pomocą usługi AAD

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra — zalecenia dotyczące zabezpieczeń](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Kroki** | Klastry działające na platformie Azure mogą również zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (AAD), a nie od certyfikatów klienta. W przypadku klastrów platformy Azure zaleca się używanie zabezpieczeń usługi AAD do uwierzytelniania klientów i certyfikatów na potrzeby zabezpieczeń między węzłami.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Upewnij się, że certyfikaty usługi Service Fabric są uzyskiwane z zatwierdzonego urzędu certyfikacji (CA)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Certyfikaty X. 509 i Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Kroki** | <p>Service Fabric używa certyfikatów serwera X. 509 do uwierzytelniania węzłów i klientów.</p><p>Niektóre ważne zagadnienia, które należy wziąć pod uwagę podczas korzystania z certyfikatów w sieciach szkieletowych usług:</p><ul><li>Certyfikaty używane w klastrach z uruchomionym obciążeniem produkcyjnym powinny być tworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskanej z zatwierdzonego urzędu certyfikacji (CA). Urząd certyfikacji może być zatwierdzonym zewnętrznym urzędem certyfikacji lub prawidłowo zarządzaną infrastrukturą kluczy publicznych (PKI)</li><li>Nigdy nie używaj żadnych certyfikatów tymczasowych lub testowych w środowisku produkcyjnym, które są tworzone za pomocą narzędzi takich jak MakeCert.exe</li><li>Można użyć certyfikatu z podpisem własnym, ale należy to zrobić tylko w przypadku klastrów testowych, a nie w środowisku produkcyjnym.</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Używaj standardowych scenariuszy uwierzytelniania obsługiwanych przez serwer tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [IdentityServer3 — Big Picture](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroki** | <p>Poniżej przedstawiono typowe interakcje obsługiwane przez serwer tożsamości:</p><ul><li>Przeglądarki komunikują się z aplikacjami sieci Web</li><li>Aplikacje sieci Web komunikują się z internetowymi interfejsami API (czasami w imieniu użytkownika)</li><li>Aplikacje oparte na przeglądarce komunikują się z interfejsami API sieci Web</li><li>Aplikacje natywne komunikują się z interfejsami API sieci Web</li><li>Aplikacje oparte na serwerze komunikują się z interfejsami API sieci Web</li><li>Interfejsy API sieci Web komunikują się z internetowymi interfejsami API (czasami w imieniu użytkownika)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Zastąp domyślną pamięć podręczną tokenów serwera tożsamości z skalowalną alternatywą

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wdrożenie serwera tożsamości — buforowanie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | <p>IdentityServer ma prostą wbudowaną pamięć podręczną w pamięci. Chociaż jest to dobre w przypadku aplikacji w małych skali, nie jest ona skalowana w przypadku aplikacji warstwy środkowej i zaplecza z następujących powodów:</p><ul><li>Te aplikacje są dostępne dla wielu użytkowników jednocześnie. Zapisanie wszystkich tokenów dostępu w tym samym magazynie powoduje problemy z izolacją i przedstawia wyzwania podczas działania w odpowiedniej skali: wielu użytkowników, z których każda zawiera dowolną liczbę tokenów jako zasoby, do których uzyskuje dostęp aplikacja w ich imieniu, może oznaczać ogromną liczbę i bardzo kosztowne operacje wyszukiwania</li><li>Te aplikacje są zwykle wdrażane w topologiach rozproszonych, w których wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny buforowane muszą przetrwać odtwarzanie procesów i aktywacje</li><li>Ze względu na to, że podczas implementowania usługi Web Apps zaleca się przesłonięcie pamięci podręcznej tokeny domyślnego serwera tożsamości z skalowalną alternatywą, taką jak usługa Azure cache for Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny |
| **Faza SDL**               | Wdrożenie |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że pliki binarne wdrożonych aplikacji są podpisane cyfrowo, aby można było zweryfikować integralność plików binarnych|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Włączanie uwierzytelniania podczas nawiązywania połączenia z kolejkami usługi MSMQ w programie WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Kroki** | Program nie może włączyć uwierzytelniania podczas nawiązywania połączenia z kolejkami usługi MSMQ, osoba atakująca może anonimowo przesyłać komunikaty do kolejki w celu przetworzenia. Jeśli uwierzytelnianie nie jest używane do nawiązywania połączenia z kolejką usługi MSMQ służącą do dostarczania wiadomości do innego programu, osoba atakująca może przesłać anonimowy komunikat, który jest złośliwy.|

### <a name="example"></a>Przykład
`<netMsmqBinding/>`Poniższy element pliku konfiguracji WCF instruuje program WCF, aby wyłączył uwierzytelnianie podczas nawiązywania połączenia z kolejką usługi MSMQ na potrzeby dostarczania komunikatów.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurowanie usługi MSMQ do żądania uwierzytelniania domeny systemu Windows lub certyfikatów dla wszystkich wiadomości przychodzących lub wychodzących.

### <a name="example"></a>Przykład
`<netMsmqBinding/>`Poniższy element pliku konfiguracji WCF instruuje program WCF, aby włączył uwierzytelnianie certyfikatu podczas nawiązywania połączenia z kolejką usługi MSMQ. Klient jest uwierzytelniany przy użyciu certyfikatów X. 509. Certyfikat klienta musi znajdować się w magazynie certyfikatów serwera programu.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF — nie ustawiaj komunikatu clientCredentialtype na none

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta — brak |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy użytkownicy mogą uzyskać dostęp do tej usługi. Usługa, która nie uwierzytelnia swoich klientów, zezwala na dostęp wszystkim użytkownikom. Skonfiguruj aplikację do uwierzytelniania przy użyciu poświadczeń klienta. Można to zrobić, ustawiając komunikat clientCredentialtype na Windows lub Certificate. |

### <a name="example"></a>Przykład
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF — nie ustawiaj transportowej wartości clientCredentialtype na none

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólne, .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta — brak |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy użytkownicy mogą uzyskać dostęp do tej usługi. Usługa, która nie uwierzytelnia swoich klientów, umożliwia wszystkim użytkownikom dostęp do jej funkcjonalności. Skonfiguruj aplikację do uwierzytelniania przy użyciu poświadczeń klienta. Można to zrobić, ustawiając wartość clientCredentialtype transportu na Windows lub Certificate. |

### <a name="example"></a>Przykład
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Upewnij się, że standardowe techniki uwierzytelniania są używane do zabezpieczania interfejsów API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w usłudze ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [zewnętrzne usługi uwierzytelniania z interfejsem api sieci Web ASP.NET (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroki** | <p>Uwierzytelnianie to proces, w którym jednostka udowadnia swoją tożsamość, zwykle za pomocą poświadczeń, takich jak nazwa użytkownika i hasło. Istnieje wiele dostępnych protokołów uwierzytelniania, które mogą być brane pod uwagę. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na podstawie systemu Windows</li><li>Oparte na formularzach</li><li>Federacja — ADFS</li><li>Federacja — Azure AD</li><li>Federacyjny serwer tożsamości</li></ul><p>Linki w sekcji References zawierają szczegółowe informacje o tym, jak można zaimplementować poszczególne schematy uwierzytelniania w celu zabezpieczenia internetowego interfejsu API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Używaj standardowych scenariuszy uwierzytelniania obsługiwanych przez Azure Active Directory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Scenariusze uwierzytelniania dla usługi Azure AD](../../active-directory/develop/authentication-vs-authorization.md), [Azure Active Directory przykłady kodu](../../active-directory/azuread-dev/sample-v1-code.md), [przewodnik dewelopera Azure Active Directory](../../active-directory/develop/index.yml) |
| **Kroki** | <p>Azure Active Directory (Azure AD) upraszcza uwierzytelnianie dla deweloperów, dostarczając tożsamość jako usługę, z obsługą protokołów standardowych branżowych, takich jak OAuth 2,0 i OpenID Connect Connect. Poniżej przedstawiono pięć scenariuszy aplikacji podstawowych obsługiwanych przez usługę Azure AD:</p><ul><li>Przeglądarka sieci Web do aplikacji sieci Web: użytkownik musi zalogować się do aplikacji sieci Web, która jest zabezpieczona przez usługę Azure AD</li><li>Aplikacja jednostronicowa (SPA): użytkownik musi zalogować się do aplikacji jednostronicowej zabezpieczonej przez usługę Azure AD</li><li>Natywna aplikacja do interfejsu API sieci Web: Natywna aplikacja działająca na telefonie, tablecie lub komputerze musi uwierzytelniać użytkownika w celu pobierania zasobów z internetowego interfejsu API, który jest zabezpieczony przez usługę Azure AD</li><li>Aplikacja sieci Web do interfejsu API sieci Web: aplikacja sieci Web musi pobrać zasoby z internetowego interfejsu API chronionego przez usługę Azure AD</li><li>Demon lub aplikacja serwera do interfejsu API sieci Web: aplikacja demona lub aplikacja serwera bez interfejsu użytkownika sieci Web musi pobierać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD</li></ul><p>Zapoznaj się z linkami w sekcji References, aby zapoznać się ze szczegółami dotyczącymi implementacji niskiego poziomu.</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Zastąp domyślną pamięć podręczną tokenów ADAL skalowalną alternatywą

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie z Azure Active Directory aplikacji sieci Web](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) [przy użyciu Redis jako pamięci podręcznej tokenów ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroki** | <p>Domyślną pamięcią podręczną używaną w ramach biblioteki ADAL (Active Directory Authentication Library) jest pamięć podręczna w pamięci, która opiera się na magazynie statycznym, dostępnym dla całego procesu. Chociaż działa to w przypadku aplikacji natywnych, nie jest ona skalowana w przypadku aplikacji warstwy środkowej i zaplecza z następujących powodów:</p><ul><li>Te aplikacje są dostępne dla wielu użytkowników jednocześnie. Zapisanie wszystkich tokenów dostępu w tym samym magazynie powoduje problemy z izolacją i przedstawia wyzwania podczas działania w odpowiedniej skali: wielu użytkowników, z których każda zawiera dowolną liczbę tokenów jako zasoby, do których uzyskuje dostęp aplikacja w ich imieniu, może oznaczać ogromną liczbę i bardzo kosztowne operacje wyszukiwania</li><li>Te aplikacje są zwykle wdrażane w topologiach rozproszonych, w których wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny buforowane muszą przetrwać odtwarzanie procesów i aktywacje</li></ul><p>Ze względu na to, że podczas implementowania usługi Web Apps zaleca się przesłonięcie domyślnej pamięci podręcznej tokenów biblioteki ADAL z skalowalną alternatywą, taką jak usługa Azure cache for Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Upewnij się, że TokenReplayCache jest używany do zapobiegania powtarzaniu tokenów uwierzytelniania ADAL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie za pomocą Azure Active Directory dla aplikacji sieci Web](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **Kroki** | <p>Właściwość TokenReplayCache umożliwia deweloperom zdefiniowanie pamięci podręcznej powtarzania tokenów, magazynu, który może być używany do zapisywania tokenów w celu sprawdzenia, czy nie można użyć żadnego tokenu więcej niż raz.</p><p>Jest to miara w odniesieniu do typowego ataku, aptly wywołane atakiem typu "token". osoba atakująca przechwytuje token wysłany podczas logowania może próbować ponownie wysłać do aplikacji ("Powtórz" ją) w celu ustanowienia nowej sesji. Na przykład w przypadku przepływu przydzielenia kodu OIDC po pomyślnym uwierzytelnieniu użytkownika żądanie do punktu końcowego "/SignIn-OIDC" jednostki uzależnionej jest nawiązywane za pomocą parametrów "id_token", "Code" i "State".</p><p>Jednostka uzależniona sprawdza poprawność tego żądania i ustanawia nową sesję. Jeśli atakującej przechwytuje to żądanie i odtwarza je, może nawiązać pomyślną sesję i sfałszować użytkownika. Obecność identyfikatora jednorazowego w programie OpenID Connect Connect może ograniczyć, ale nie całkowicie wyeliminować sytuacje, w których atak może zostać pomyślnie wdrożony. Aby chronić swoje aplikacje, deweloperzy mogą zapewnić implementację ITokenReplayCache i przypisać wystąpienie do TokenReplayCache.</p>|

### <a name="example"></a>Przykład
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Przykład
Oto Przykładowa implementacja interfejsu ITokenReplayCache. (Dostosuj i zaimplementuj strukturę buforowania specyficzną dla projektu)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Zaimplementowana pamięć podręczna musi być przywoływana w opcjach OIDC za pomocą właściwości "TokenValidationParameters" w następujący sposób.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Należy pamiętać, że w celu przetestowania skuteczności tej konfiguracji zaloguj się do lokalnej aplikacji chronionej przez OIDC i Przechwyć żądanie do `"/signin-oidc"` punktu końcowego w programu Fiddler. Gdy ochrona nie jest na miejscu, odtwarzanie tego żądania w programu Fiddler ustawi nowy plik cookie sesji. Gdy żądanie jest ponownie odtwarzane po dodaniu ochrony TokenReplayCache, aplikacja zgłosi wyjątek w następujący sposób: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Korzystanie z bibliotek ADAL do zarządzania żądaniami tokenów od klientów OAuth2 do usługi AAD (lub w lokalnej usłudze AD)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Kroki** | <p>Biblioteka Azure AD Authentication Library (ADAL) umożliwia deweloperom aplikacji klienckich łatwe uwierzytelnianie użytkowników w chmurze lub Active Directory lokalnych (AD), a następnie uzyskanie tokenów dostępu w celu zabezpieczania wywołań interfejsu API.</p><p>Biblioteka ADAL ma wiele funkcji, które ułatwiają uwierzytelnianie dla deweloperów, takich jak obsługa asynchroniczna, konfigurowalna pamięć podręczna tokenów, która przechowuje tokeny dostępu i odświeżanie tokenów, automatyczne odświeżanie tokenów, gdy token dostępu wygasa i dostępny jest token odświeżania.</p><p>Dzięki obsłudze większości złożoności Biblioteka ADAL może ułatwić deweloperom skoncentrowanie się na logice biznesowej w swojej aplikacji i łatwe Zabezpieczanie zasobów bez konieczności eksperta w zakresie zabezpieczeń. Dostępne są osobne biblioteki dla platform .NET, JavaScript (Client i Node.js), Python, iOS, Android i Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Uwierzytelnianie urządzeń łączących się z bramą pola

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że każde urządzenie jest uwierzytelniane przez bramę pola przed zaakceptowaniem z nich danych i przed ułatwieniem komunikacji z bramą w chmurze. Należy również upewnić się, że urządzenia łączą się z poświadczeniem urządzenia, aby umożliwić jednoznaczne zidentyfikowanie poszczególnych urządzeń.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Upewnij się, że urządzenia łączące się z bramą w chmurze są uwierzytelniane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólne, C#, Node.JS,  |
| **Atrybuty**              | Nie dotyczy, wybór bramy — Azure IoT Hub |
| **Odwołania**              | Nie dotyczy [platformy Azure IoT Hub z platformą .NET](../../iot-hub/quickstart-send-telemetry-dotnet.md), [wprowadzenie z usługami IoT Hub i Node js](../../iot-hub/quickstart-send-telemetry-node.md), [Zabezpieczanie IoT za pomocą sygnatury dostępu współdzielonego i certyfikatów](../../iot-hub/iot-hub-devguide-security.md), [repozytorium git](https://github.com/Azure/azure-iot-sdks/) |
| **Kroki** | <ul><li>**Ogólne:** Uwierzytelnij urządzenie przy użyciu protokołu Transport Layer Security (TLS) lub IPSec. Infrastruktura powinna obsługiwać korzystanie z klucza wstępnego (PSK) na tych urządzeniach, które nie mogą obsługiwać pełnego asymetrycznego kryptografii. Korzystanie z usługi Azure AD i uwierzytelniania OAuth.</li><li>**C#:** Podczas tworzenia wystąpienia DeviceClient domyślnie Metoda Create Tworzy wystąpienie DeviceClient, które używa protokołu AMQP do komunikowania się z IoT Hub. Aby użyć protokołu HTTPS, użyj zastępowania metody Utwórz, które pozwala na określenie protokołu. Jeśli używasz protokołu HTTPS, należy również dodać `Microsoft.AspNet.WebApi.Client` pakiet NuGet do projektu w celu uwzględnienia `System.Net.Http.Formatting` przestrzeni nazw.</li></ul>|

### <a name="example"></a>Przykład
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Przykład
**Node.JS: uwierzytelnianie**
#### <a name="symmetric-key"></a>Klucz symetryczny
* Tworzenie Centrum IoT Hub na platformie Azure
* Tworzenie wpisu w rejestrze tożsamości urządzeń
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Tworzenie symulowanego urządzenia
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>Token SAS
* Pobiera wewnętrznie, gdy jest używany klucz symetryczny, ale można go również wygenerować i użyć jawnie
* Zdefiniuj protokół: `var Http = require('azure-iot-device-http').Http;`
* Utwórz token sygnatury dostępu współdzielonego:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Połącz przy użyciu tokenu SAS:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Certyfikaty
* Generuj certyfikat x509 z podpisem własnym przy użyciu dowolnego narzędzia, takiego jak OpenSSL, aby wygenerować plik. Certificate i. Key do przechowywania certyfikatu i klucza odpowiednio
* Zapewnij urządzeniu, które akceptuje bezpieczne połączenie przy użyciu certyfikatów.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Łączenie urządzenia przy użyciu certyfikatu
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Użyj poświadczeń uwierzytelniania dla urządzenia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud  |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — IoT Hub platformy Azure |
| **Odwołania**              | [Tokeny zabezpieczające IoT Hub platformy Azure](../../iot-hub/iot-hub-devguide-security.md) |
| **Kroki** | Użyj poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS opartych na kluczu urządzenia lub certyfikacie klienta, a nie na poziomie IoT Hub zasad dostępu współdzielonego. Zapobiega to ponownemu używaniu tokenów uwierzytelniania jednego urządzenia lub bramy pola przez inną |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Upewnij się, że tylko wymagane kontenery i obiekty blob mają anonimowy dostęp do odczytu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | StorageType — obiekt BLOB |
| **Odwołania**              | [Zarządzanie dostępem anonimowym do odczytu do kontenerów i obiektów BLOB](../../storage/blobs/anonymous-read-access-configure.md), [sygnatury dostępu współdzielonego, część 1: Omówienie modelu SAS](../../storage/common/storage-sas-overview.md) |
| **Kroki** | <p>Domyślnie kontener i wszystkie obiekty blob w ramach niego mogą być dostępne tylko dla właściciela konta magazynu. Aby przyznać anonimowym użytkownikom uprawnienia do odczytu do kontenera i jego obiektów blob, jeden może ustawić uprawnienia kontenera, aby zezwolić na dostęp publiczny. Anonimowi użytkownicy mogą odczytywać obiekty blob w publicznie dostępnym kontenerze bez uwierzytelniania żądania.</p><p>Kontenery oferują następujące opcje zarządzania dostępem do kontenera:</p><ul><li>Pełny publiczny dostęp do odczytu: kontener i dane obiektu BLOB mogą być odczytywane za pośrednictwem anonimowego żądania. Klienci mogą wyliczać obiekty blob w kontenerze za pośrednictwem żądania anonimowego, ale nie mogą wyliczać kontenerów na koncie magazynu.</li><li>Publiczny dostęp do odczytu tylko dla obiektów blob: dane obiektów BLOB w tym kontenerze można odczytać za pośrednictwem anonimowego żądania, ale dane kontenera nie są dostępne. Klienci nie mogą wyliczyć obiektów BLOB w kontenerze za pośrednictwem żądania anonimowego</li><li>Brak publicznego dostępu do odczytu: kontener i dane obiektu BLOB mogą być odczytane tylko przez właściciela konta</li></ul><p>Dostęp anonimowy jest najlepszy dla scenariuszy, w których niektóre obiekty blob powinny zawsze być dostępne do anonimowego dostępu do odczytu. W celu zapewnienia precyzyjnej kontroli, jeden może utworzyć sygnaturę dostępu współdzielonego, która umożliwia delegowanie ograniczonego dostępu przy użyciu różnych uprawnień i w określonym przedziale czasu. Upewnij się, że kontenery i obiekty blob, które mogą potencjalnie zawierać dane poufne, nie mają przypadkowego dostępu anonimowego</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Udzielanie ograniczonego dostępu do obiektów w usłudze Azure Storage przy użyciu SAS lub SAP

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage |
| **Faza SDL**               | Kompilacja |
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [Sygnatury dostępu współdzielonego, część 1: zrozumienie modelu SAS](../../storage/common/storage-sas-overview.md), [sygnatury dostępu współdzielonego, część 2: Tworzenie i używanie SAS z magazynem obiektów BLOB](../../storage/common/storage-sas-overview.md), [jak delegować dostęp do obiektów na koncie przy użyciu sygnatur dostępu współdzielonego i przechowywanych zasad dostępu](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Kroki** | <p>Używanie sygnatury dostępu współdzielonego (SAS) jest zaawansowanym sposobem przyznawania ograniczonego dostępu do obiektów na koncie magazynu innym klientom bez konieczności ujawniania klucza dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania, wszystkie informacje niezbędne do uwierzytelnienia dostęp do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu z sygnaturą dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody.</p><p>Sygnatury dostępu współdzielonego można użyć, jeśli chcesz zapewnić dostęp do zasobów na koncie magazynu do klienta, którego nie można zaufać przy użyciu klucza konta. Klucze konta magazynu obejmują zarówno klucz podstawowy, jak i pomocniczy, które mają dostęp administracyjny do Twojego konta i wszystkich znajdujących się w nim zasobów. Udostępnienie jednego z kluczy konta spowoduje otwarcie konta użytkownika w celu uzyskania złośliwego lub zaniedbanego użycia. Sygnatury dostępu współdzielonego zapewniają bezpieczną alternatywę, która umożliwia innym klientom odczytywanie, zapisywanie i usuwanie danych na koncie magazynu zgodnie z przyznanymi uprawnieniami i bez konieczności stosowania klucza konta.</p><p>Jeśli masz logiczny zestaw parametrów, które są podobne za każdym razem, użycie zasad dostępu przechowywanego (SAP) jest lepszym pomysłem. Ze względu na to, że używanie sygnatury dostępu współdzielonego pochodzącego z przechowywanych zasad jest możliwe, można natychmiast odwołać te sygnatury dostępu współdzielonego.</p>|