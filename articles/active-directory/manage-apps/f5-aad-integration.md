---
title: Bezpieczny dostęp do hybrydowej usługi Azure AD za pomocą klawisza F5 | Microsoft Docs
description: F5 BIG-IP Access Manager and Azure Active Directory Integration dla bezpiecznego dostępu hybrydowego
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499818"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Manager and Azure Active Directory Integration dla bezpiecznego dostępu hybrydowego

Rozprzestrzenianie się rozwoju mobilności i rozwojem zagrożeń polega na umieszczeniu dodatkowej [kontroli nad dostępem](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) do zasobów i jej nadzorem, dzięki czemu nie ma możliwości założenia i wyśrodkowania wszystkich programów modernizacji.
W firmie Microsoft i F5 zdajemy sobie tę transformację cyfrową zwykle w wieloletniej podróży dla każdej firmy, co może spowodować, że krytyczne zasoby są narażone do nowoczesnego wykorzystania. Genesis za F5 BIG-IP i Azure Active Directory bezpieczny dostęp hybrydowy (SHA) nie tylko przy ulepszaniu dostępu zdalnego do aplikacji lokalnych, ale również przy wzmocnieniu ogólnej stan zabezpieczeń tych usług.

Dla kontekstu badania szacunkowe, że 60-80% aplikacji lokalnych są starsze, lub innymi słowy, które nie mogą być zintegrowane bezpośrednio z usługą Azure Active Directory (AD). Takie samo badanie wskazuje również duże proporcje tych systemów, które są uruchamiane w wersjach niskiego poziomu oprogramowania SAP, Oracle, SAGE i innych dobrze znanych obciążeń, które zapewniają krytyczne usługi.

Algorytm SHA eliminuje ten niewidomy punkt, umożliwiając organizacjom dalsze korzystanie z ich inwestycji w inne funkcje dostarczania sieci i aplikacji. W połączeniu z usługą Azure AD IT mostkuje aplikację heterogeniczną z warstwą kontroli tożsamości nowoczesnej.

Posiadanie wstępnego uwierzytelniania dostępu usługi Azure AD do usług opublikowanych w ramach dużych adresów IP zapewnia wiele korzyści:

- Uwierzytelnianie bez hasła za pośrednictwem funkcji [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), usługi [Fast Identity online (Fido)](../authentication/howto-authentication-passwordless-security-key.md)i [uwierzytelniania opartego na certyfikatach](../authentication/active-directory-certificate-based-authentication-get-started.md)

- Zastępujący [dostęp warunkowy](../conditional-access/overview.md) i [uwierzytelnianie wieloskładnikowe (MFA)](../authentication/concept-mfa-howitworks.md)

- [Ochrona tożsamości](../identity-protection/overview-identity-protection.md) — kontrola adaptacyjna poprzez profilowanie ryzyka użytkowników i sesji


- [Wykrywanie przecieków poświadczeń](../identity-protection/concept-identity-protection-risks.md)

- [Samoobsługowe resetowanie hasła (SSPR)](../authentication/tutorial-enable-sspr.md)

- [Współpraca z partnerami](../governance/entitlement-management-external-users.md) — Zarządzanie prawami dla dostępu gościa

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) — na potrzeby pełnego odnajdywania i kontroli aplikacji

- Monitorowanie zagrożeń — [wskaźnik platformy Azure dla usługi](https://azure.microsoft.com/services/azure-sentinel/) Advanced Threat Analytics

- [Portal usługi Azure AD](https://azure.microsoft.com/features/azure-portal/) — pojedyncza płaszczyzna kontroli na potrzeby zarządzania tożsamościami i dostępem

## <a name="scenario-description"></a>Opis scenariusza

Jako kontroler dostarczania aplikacji (ADC) i SSL-VPN system BIG-IP zapewnia dostęp lokalny i zdalny do wszystkich typów usług, takich jak:

- Nowoczesne i starsze aplikacje sieci Web

- Aplikacje niekorzystające z sieci Web

- Usługi REST i interfejsy API sieci Web protokołu SOAP

Jego lokalny Traffic Manager (LTM) umożliwia bezpieczne publikowanie usług za poorednictwem odwrotnej funkcjonalności serwera proxy. W tym samym czasie zaawansowanego Menedżera zasad dostępu (APM) rozszerza pełny adres IP o bogatszy zestaw funkcji, co umożliwia federacyjnego i logowanie jednokrotne (SSO).

Integracja jest oparta na standardowym zaufaniu federacji między APM i Azure AD, najczęściej w większości przypadków użycia algorytmu SHA, które obejmują [scenariusz SSL-VPN](f5-aad-password-less-vpn.md). Usługi SAML (SAML), OAuth i Open ID Connect (OIDC) nie są wyjątkiem, ponieważ są one zbyt bezpieczne dla dostępu zdalnego. Mogą również wystąpić scenariusze, w których BIG-IP stał się punktem podlewka dla dostępu bez zaufania do wszystkich usług, w tym aplikacji SaaS.

Możliwość integracji z usługą Azure AD przez wiele adresów IP polega na tym, że przechodzenie protokołu wymagane do zabezpieczania starszych lub niezintegrowanych usług Azure AD z nowoczesnymi kontrolkami, takimi jak [uwierzytelnianie bez hasła](https://www.microsoft.com/security/business/identity/passwordless) i [dostęp warunkowy](../conditional-access/overview.md). W tym scenariuszu duży adres IP będzie nadal spełniał rolę jako zwrotny serwer proxy, podczas gdy w ramach uwierzytelniania wstępnego i autoryzacji do usługi Azure AD odbywa się na podstawie poszczególnych usług.

Kroki 1-4 na diagramie ilustrują wymianę przed uwierzytelnianiem wstępnym między użytkownikiem, BIG-IP i usługą Azure AD w przepływie zainicjowanym przez dostawcę usług. Kroki 5-6 pokazują kolejne wzbogacanie sesji APM i logowanie jednokrotne do poszczególnych usług zaplecza.

![Obraz przedstawia architekturę wysokiego poziomu](./media/f5-aad-integration/integration-flow-diagram.png)

| Krok | Opis |
|:------|:-----------|
| 1. | Użytkownik wybiera ikonę aplikacji w portalu, rozwiązując adres URL do protokołu SAML SP (BIG-IP) |
| 2. | Usługa BIG-IP przekierowuje użytkownika do usługi SAML dostawcy tożsamości (Azure AD) w celu wstępnego uwierzytelniania|
| 3. | Usługa Azure AD przetwarza zasady dostępu warunkowego i [kontrolki sesji](../conditional-access/concept-conditional-access-session.md) na potrzeby autoryzacji|
| 4. | Użytkownik przekierowuje do usługi BIG-IP prezentowanie oświadczeń SAML wystawionych przez usługę Azure AD |
| 5. | BIG-IP żąda wszelkich dodatkowych informacji o sesji do uwzględnienia w [rejestracji jednokrotnej](../hybrid/how-to-connect-sso.md) i [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md) do opublikowanej usługi |
| 6. | BIG-IP przekazuje żądanie klienta do usługi wewnętrznej bazy danych

## <a name="user-experience"></a>Środowisko użytkownika

Niezależnie od tego, czy jest to pracownik bezpośredni, stowarzyszony czy konsument, większość użytkowników zna już środowisko logowania do pakietu Office 365, dlatego dostęp do usług BIG-IP za pośrednictwem algorytmu SHA jest w dużym stopniu znany.

Użytkownicy mogą teraz znaleźć swoje usługi opublikowane przez duże adresy IP, które są skonsolidowane w programie  [webapps](../user-help/my-apps-portal-end-user-access.md) lub usłudze [O365](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) , a także funkcje samoobsługowe w szerszym zestawie usług, niezależnie od typu urządzenia lub lokalizacji. Użytkownicy mogą nawet nadal uzyskiwać dostęp do opublikowanych usług bezpośrednio za pośrednictwem BIG-IPs własnościowego portalu Webtop, jeśli jest preferowany. Po wylogowaniu usługa SHA gwarantuje, że sesja użytkowników zostanie zakończona w obu końcach — BIG-IP i Azure AD, zapewniając usługi w pełni chronione przed nieautoryzowanym dostępem.  

Udostępniane zrzuty ekranu pochodzą z portalu aplikacji usługi Azure AD, dzięki któremu użytkownicy uzyskują bezpieczny dostęp do usług opublikowanych przez duże adresy IP oraz do zarządzania ich właściwościami konta.  

![Zrzut ekranu przedstawia galerię Woodgrove Apps](media/f5-aad-integration/woodgrove-app-gallery.png)

![Zrzut ekranu przedstawia stronę samoobsługi usługi Woodgrove webaccounts](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Szczegółowe informacje i analiza

Rola BIG-IP ma kluczowe znaczenie dla każdej firmy, dlatego wdrożone wystąpienia BIG-IP powinny być monitorowane w celu zapewnienia wysokiej dostępności usług publikowanych, zarówno na poziomie SHA, jak i w tym samym czasie.

Istnieje kilka opcji rejestrowania zdarzeń lokalnie lub zdalnie za pomocą rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), co umożliwia przechowywanie danych telemetrycznych i przetwarzanie danych telemetrii. Wysoce wydajne rozwiązanie do monitorowania działań związanych z usługą Azure AD i określonym przez algorytm SHA ma na celu korzystanie z [Azure monitor](../../azure-monitor/overview.md) i [platformy Azure ze wskaźnikami](../../sentinel/overview.md):

- Szczegółowy przegląd organizacji, potencjalnie w wielu chmurach i lokalizacji lokalnych, w tym infrastruktury BIG-IP

- Pojedyncza płaszczyzna kontroli dostarczająca połączony widok wszystkich sygnałów, unikając zależności od złożonych i różnorodnych narzędzi

![Obraz przedstawia przepływ monitorowania](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Wymagania wstępne

Integracja F5 BIG-IP z usługą Azure AD dla algorytmu SHA ma następujące wymagania wstępne:

- Wystąpienie F5 BIG-IP uruchomione na jednej z następujących platform:

  - Urządzenie fizyczne

  - Funkcja hypervisor Virtual Edition, taka jak Microsoft Hyper-V, VMware ESXi, KVM Linux i Citrix hypervisor

  - Wirtualne wersje chmurowe, takie jak Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack i Google Cloud

    Rzeczywista lokalizacja wystąpienia BIG-IP może być lokalna lub dowolna obsługiwana platforma w chmurze, w tym platforma Azure, pod warunkiem, że ma łączność z Internetem, publikowane zasoby oraz inne wymagane usługi, takie jak Active Directory.  

- Aktywna licencja F5 BIG-IP APM za pomocą jednej z następujących opcji:

   - F5 BIG-IP® najlepszym pakietem (lub)

   - F5 BIG-IP Menedżer zasad dostępu™ licencja autonomiczna

   - Protokół F5 BIG-IP Access Manager — licencja dodatku™ (APM) na istniejącym standardzie BIG-IP F5® lokalnym Traffic Manager™ (LTM)

   - [Licencja na wersję próbną](https://www.f5.com/trial/big-ip-trial.php) programu™ (APM) z 90-dniowym dostępem Big-IP

- Licencjonowanie usługi Azure AD za pomocą jednej z następujących opcji:

   - [Bezpłatna subskrypcja](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) usługi Azure AD zapewnia minimalne wymagania podstawowe dotyczące IMPLEMENTOWANIA algorytmu SHA z uwierzytelnianiem bez hasła

   - [Subskrypcja Premium](https://azure.microsoft.com/pricing/details/active-directory/) zapewnia wszystkie dodatkowe wartości, które są opisane w przedniej, w tym [dostęp warunkowy](../conditional-access/overview.md), uwierzytelnianie [wieloskładnikowe](../authentication/concept-mfa-howitworks.md)i [Ochrona tożsamości](../identity-protection/overview-identity-protection.md)

Do wdrożenia algorytmu SHA nie są wymagane żadne poprzednie doświadczenia ani informacje o użyciu BIG-IP, ale zalecamy zapoznanie się z terminologią dotyczącą protokołu F5 BIG-IP. F5's zaawansowana [Baza wiedzy](https://www.f5.com/services/resources/glossary) jest również dobrym miejscem umożliwiającym rozpoczęcie tworzenia wiedzy o Big-IP.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Poniższe samouczki zawierają szczegółowe wskazówki dotyczące wdrażania niektórych popularnych wzorców dla dużych adresów IP i SHA usługi Azure AD:

- [F5 BIG-IP w instruktażu wdrażania platformy Azure](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM i logowanie jednokrotne usługi Azure AD do aplikacji Kerberos](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM i logowanie jednokrotne usługi Azure AD do aplikacji opartych na nagłówku](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Zabezpieczanie BIG-Połączenie SSL z adresu IP-VPN za pomocą usługi Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Koniec hasła, przejdź bezhasło](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory bezpiecznego dostępu hybrydowego](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Platforma zaufania zero firmy Microsoft, która umożliwia zdalne działanie](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Wprowadzenie do platformy Azure — wskaźnik](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Następne kroki

Rozważ uruchomienie weryfikacji koncepcji SHA (ZK) za pomocą istniejącej infrastruktury BIG-IP lub wdrożenie wystąpienia próbnego. [Wdrażanie maszyny wirtualnej z systemem Big-IP Virtual Edition (VE) na platformie Azure](f5-bigip-deployment-guide.md) trwa około 30 minut, po czym będzie można:

- W pełni zabezpieczona platforma do modelowania weryfikacji koncepcji SHA

- Wystąpienie środowiska przedprodukcyjnego, w pełni zabezpieczonej platformy do testowania nowych aktualizacji i poprawek systemu BIG-IP

W tym samym czasie należy zidentyfikować jedną lub dwie aplikacje, które mogą być przeznaczone do publikowania za pośrednictwem BIG-IP i ochrony przy użyciu algorytmu SHA.  

Zalecamy rozpoczęcie pracy z aplikacją, która nie została jeszcze opublikowana za pośrednictwem BIG-IP, dlatego w celu uniknięcia potencjalnych przerw w świadczeniu usług produkcyjnych. Wskazówki wymienione w tym artykule ułatwią zapoznanie się z ogólną procedurą tworzenia różnych obiektów konfiguracji BIG-IP i konfigurowania algorytmu SHA. Po zakończeniu powinna być możliwa taka sama w przypadku innych nowych usług, a ponadto ma wystarczającą wiedzę na potrzeby konwertowania istniejących usług opublikowanych przez duże adresy IP na algorytm SHA z minimalnym nakładem pracy.

Poniższy Przewodnik interaktywny zawiera szczegółowe procedury dotyczące implementowania algorytmu SHA i wyświetlania środowiska użytkownika końcowego.

[![Obraz pokazuje interaktywną wskazówkę przewodnika](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
