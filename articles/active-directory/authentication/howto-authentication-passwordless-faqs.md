---
title: Często zadawane pytania dotyczące wdrożenia klucza zabezpieczeń hybrydowej FIDO2 — Azure Active Directory
description: Dowiedz się więcej na temat niektórych często zadawanych pytań dotyczących logowania za pomocą klucza zabezpieczeń FIDO2 hybrydowego przy użyciu usługi Azure Active Directory (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98cb990ede7c4d6e261bba05b0b8c97d758e6c32
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743534"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Wdrożenie często zadawanych pytań (FAQ) dla kluczy zabezpieczeń hybrydowych FIDO2 w usłudze Azure AD (wersja zapoznawcza)

W tym artykule opisano często zadawane pytania dotyczące wdrażania w przypadku hybrydowych urządzeń z usługą Azure AD oraz logowania za pomocą hasła do zasobów Premium. Dzięki tej funkcji bez hasła można włączyć uwierzytelnianie usługi Azure AD na urządzeniach z systemem Windows 10 dla urządzeń przyłączonych do hybrydowej usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2. Użytkownicy mogą logować się do systemu Windows na swoich urządzeniach przy użyciu nowoczesnych poświadczeń, takich jak klucze FIDO2 i dostęp do zasobów tradycyjnych Active Directory Domain Services (AD DS) za pomocą bezproblemowego logowania jednokrotnego (SSO) do zasobów Premium.

Obsługiwane są następujące scenariusze dla użytkowników w środowisku hybrydowym:

* Zaloguj się do urządzeń przyłączonych do hybrydowej usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 i uzyskaj dostęp do logowania jednokrotnego do zasobów Premium.
* Zaloguj się do urządzeń przyłączonych do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 i uzyskaj dostęp z logowaniem jednokrotnym do zasobów Premium.

Aby rozpocząć pracę z kluczami zabezpieczeń FIDO2 i dostępem hybrydowym do zasobów lokalnych, zobacz następujące artykuły:

* [Klucze zabezpieczeń FIDO2 bez hasła](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hasła](howto-authentication-passwordless-security-key-windows.md)
* [Środowisko lokalne bez hasła](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="security-keys"></a>Klucze zabezpieczeń

* [Moja organizacja wymaga uwierzytelniania dwuskładnikowego w celu uzyskania dostępu do zasobów. Co mogę zrobić, aby obsłużyć to wymaganie?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Gdzie mogę znaleźć zgodne klucze zabezpieczeń FIDO2?](#where-can-i-find-compliant-fido2-security-keys)
* [Co mam zrobić, Jeśli utracisz mój klucz zabezpieczeń?](#what-if-i-lose-my-security-key)
* [Jak są chronione dane przy użyciu klucza zabezpieczeń FIDO2?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Jak działa rejestrowanie kluczy zabezpieczeń FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Czy istnieje sposób, aby administratorzy mogli bezpośrednio udostępnić klucze użytkownikom?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moja organizacja wymaga uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do zasobów. Co mogę zrobić, aby obsłużyć to wymaganie?

Klucze zabezpieczeń FIDO2 są dostępne w różnych rozmiarach. Skontaktuj się z producentem urządzenia, aby omówić, jak ich urządzenia mogą być włączone przy użyciu kodu PIN lub biometrycznego jako drugiego czynnika. Listę obsługiwanych dostawców można znaleźć w temacie [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Gdzie mogę znaleźć zgodne klucze zabezpieczeń FIDO2?

Listę obsługiwanych dostawców można znaleźć w temacie [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Co zrobić, Jeśli utracisz mój klucz zabezpieczeń?

Klucze w Azure Portal można usunąć, przechodząc do strony **informacje zabezpieczające** i usuwając klucz zabezpieczeń FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Jak są chronione dane przy użyciu klucza zabezpieczeń FIDO2?

Klucze zabezpieczeń FIDO2 mają bezpieczne enclaves, chroniące przechowywane w nich klucze prywatne. Klucz zabezpieczeń FIDO2 ma także wbudowane właściwości przeciwblokujące, takie jak funkcja Windows Hello, gdzie nie można wyodrębnić klucza prywatnego.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Jak działa rejestrowanie kluczy zabezpieczeń FIDO2?

Aby uzyskać więcej informacji na temat rejestrowania i używania kluczy zabezpieczeń FIDO2, zobacz [Włącz logowanie przy użyciu klucza zabezpieczeń bezhasłem](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Czy istnieje sposób, aby administratorzy mogli bezpośrednio udostępnić klucze użytkownikom?

Nie, nie jest w tej chwili.

## <a name="prerequisites"></a>Wymagania wstępne

* [Czy ta funkcja działa, jeśli nie ma połączenia z Internetem?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Jakie są określone punkty końcowe, które są wymagane do otwarcia w usłudze Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Jak mogę zidentyfikować typ przyłączenia do domeny (przyłączone do usługi Azure AD lub hybrydowej usługi Azure AD) dla urządzenia z systemem Windows 10?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Jakie jest zalecenie dotyczące liczby kontrolerów domen, które powinny zostać poprawione?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Czy mogę wdrożyć dostawcę poświadczeń FIDO2 na urządzeniu tylko lokalnym?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [Logowanie przy użyciu klucza zabezpieczeń FIDO2 nie działa dla administratora domeny ani innych kont o wysokim poziomie uprawnień. Zalet?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Czy ta funkcja działa, jeśli nie ma połączenia z Internetem?

Łączność z Internetem jest wymagana przed włączeniem tej funkcji. Gdy użytkownik loguje się po raz pierwszy przy użyciu kluczy zabezpieczeń FIDO2, musi mieć łączność z Internetem. W przypadku kolejnych zdarzeń logowania należy zalogować się w pamięci podręcznej i zezwolić użytkownikowi na uwierzytelnianie bez połączenia z Internetem.

W celu zapewnienia spójnego środowiska upewnij się, że urządzenia mają dostęp do Internetu i linię wglądu do kontrolerów domeny.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Jakie są określone punkty końcowe, które są wymagane do otwarcia w usłudze Azure AD?

Następujące punkty końcowe są związane z rejestracją i uwierzytelnianiem:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Aby uzyskać pełną listę punktów końcowych wymaganych do korzystania z produktów Microsoft Online, zobacz [adresy URL i zakresy adresów IP usługi Office 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Jak mogę zidentyfikować typ przyłączenia do domeny (przyłączone do usługi Azure AD lub hybrydowej usługi Azure AD) dla urządzenia z systemem Windows 10?

Aby sprawdzić, czy urządzenie klienckie systemu Windows 10 ma odpowiedni typ przyłączania do domeny, użyj następującego polecenia:

```console
Dsregcmd/status
```

Następujące przykładowe dane wyjściowe pokazują, że urządzenie jest połączone z usługą Azure AD, ponieważ *AzureADJoined* jest ustawiona na *wartość tak*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

Następujące przykładowe dane wyjściowe pokazują, że urządzenie jest przyłączone do hybrydowej usługi Azure AD, ponieważ *DomainedJoined* jest również ustawiona na *wartość Yes (tak*). Pokazana jest również wartość *nazwa_domeny* :

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Na kontrolerze domeny z systemem Windows Server 2016 lub 2019 Sprawdź, czy są stosowane następujące poprawki. W razie potrzeby Uruchom Windows Update, aby je zainstalować:

* Windows Server 2016 — [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 — [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Na urządzeniu klienckim uruchom następujące polecenie, aby sprawdzić łączność z odpowiednim kontrolerem domeny z zainstalowanymi poprawkami:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Jakie jest zalecenie dotyczące liczby kontrolerów domen, które powinny zostać poprawione?

Zalecamy stosowanie poprawek do większości kontrolerów domeny z systemem Windows Server 2016 lub 2019 przy użyciu poprawki, aby upewnić się, że mogą one obsługiwać ładowanie żądań uwierzytelniania w organizacji.

Na kontrolerze domeny z systemem Windows Server 2016 lub 2019 Sprawdź, czy są stosowane następujące poprawki. W razie potrzeby Uruchom Windows Update, aby je zainstalować:

* Windows Server 2016 — [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 — [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Czy mogę wdrożyć dostawcę poświadczeń FIDO2 na urządzeniu tylko lokalnym?

Nie, ta funkcja nie jest obsługiwana tylko w przypadku urządzenia lokalnego. Nie zostanie wyświetlony Dostawca poświadczeń FIDO2.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>Logowanie przy użyciu klucza zabezpieczeń FIDO2 nie działa dla administratora domeny ani innych kont o wysokim poziomie uprawnień. Dlaczego?

Domyślne zasady zabezpieczeń nie przyznają uprawnienia usługi Azure AD do podpisywania kont o wysokim poziomie uprawnień do zasobów lokalnych.

Aby odblokować konta, użyj **Active Directory użytkowników i komputerów** w celu zmodyfikowania właściwości *msDS-NeverRevealGroup* *obiektu komputera Kerberos usługi Azure AD (CN = AzureADKerberos, OU = Domain Controllers \<domain-DN> )*.

## <a name="under-the-hood"></a>Kulisy

* [Jak usługa Azure AD Kerberos jest połączona z lokalnym środowiskiem Active Directory Domain Services?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Gdzie można wyświetlić te obiekty serwera Kerberos, które są tworzone w usłudze AD i opublikowane w usłudze Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Dlaczego klucz publiczny nie jest zarejestrowany w AD DS lokalnym, dlatego nie ma zależności od Internetu?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [W jaki sposób klucze są obracane w obiekcie serwer Kerberos?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Dlaczego potrzebujemy Azure AD Connect? Czy zapisuje wszystkie informacje z powrotem do AD DS z usługi Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Co ma wyglądać żądanie HTTP/odpowiedź podczas żądania PRT + częściowego biletu TGT?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Jak usługa Azure AD Kerberos jest połączona z lokalnym środowiskiem Active Directory Domain Services?

Istnieją dwie części — lokalne środowisko AD DS i dzierżawa usługi Azure AD.

**Active Directory Domain Services (AD DS)**

Serwer Kerberos usługi Azure AD jest reprezentowany w lokalnym środowisku AD DS jako obiekt kontrolera domeny (DC). Ten obiekt kontrolera domeny składa się z wielu obiektów:

* *CN = AzureADKerberos, OU = kontrolery domeny,\<domain-DN>*
    
    Obiekt *komputera* , który reprezentuje Read-Only kontroler domeny (RODC) w AD DS. Brak komputera skojarzonego z tym obiektem. Zamiast tego jest logiczną reprezentacją kontrolera domeny.

* *CN = krbtgt_AzureAD, CN = users,\<domain-DN>*

    Obiekt *użytkownika* reprezentujący klucz szyfrowania biletu (TGT) protokołu Kerberos kontrolera RODC.

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = system,\<domain-DN>*

    Obiekt *serviceConnectionPoint* , który przechowuje metadane dotyczące obiektów serwera Kerberos usługi Azure AD. Narzędzia administracyjne używają tego obiektu do identyfikowania i lokalizowania obiektów serwera Kerberos usługi Azure AD.

**Azure Active Directory**

Serwer Kerberos usługi Azure AD jest reprezentowany w usłudze Azure AD jako obiekt *KerberosDomain* . Każde lokalne środowisko AD DS jest reprezentowane jako pojedynczy obiekt *KerberosDomain* w dzierżawie usługi Azure AD.

Na przykład może istnieć Las AD DS z dwiema domenami, takimi jak *contoso.com* i *fabrikam.com*. Jeśli zezwolisz usłudze Azure AD na wystawianie biletów uprawniających do przyznania biletu Kerberos (TGT) dla całego lasu, istnieją dwa obiekty *KerberosDomain* w usłudze Azure AD — jeden obiekt dla *contoso.com* i jeden dla *fabrikam.com*.

Jeśli masz wiele AD DS lasów, dla każdej domeny w każdym lesie istnieje jeden obiekt *KerberosDomain* .

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Gdzie można wyświetlić te obiekty serwera Kerberos, które są tworzone w AD DS i opublikowane w usłudze Azure AD?

Aby wyświetlić wszystkie obiekty, Użyj poleceń cmdlet programu PowerShell serwera Kerberos usługi Azure AD uwzględnionych w najnowszej wersji programu Azure AD Connect.

Aby uzyskać więcej informacji, w tym instrukcje dotyczące sposobu wyświetlania obiektów, zobacz [Tworzenie obiektów serwera Kerberos](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Dlaczego klucz publiczny nie jest zarejestrowany w AD DS lokalnym, dlatego nie ma zależności od Internetu?

Otrzymaliśmy Opinie na temat złożoności modelu wdrażania dla usługi Windows Hello dla firm, dlatego trzeba uprościć model wdrażania bez konieczności używania certyfikatów i infrastruktury PKI (FIDO2 nie używa certyfikatów).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>W jaki sposób klucze są obracane w obiekcie serwer Kerberos?

Podobnie jak każdy inny kontroler domeny, należy regularnie obrócić klucze *KRBTGT* szyfrowania serwera Kerberos usługi Azure AD. Zalecane jest przestrzeganie tego samego harmonogramu, który jest używany do obracania wszystkich innych AD DS kluczy *KRBTGT* .

> [!NOTE]
> Chociaż istnieją inne narzędzia do rotacji kluczy *KRBTGT* , należy [użyć poleceń cmdlet programu PowerShell, aby obrócić klucze *KRBTGT*](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) serwera Kerberos usługi Azure AD. Ta metoda zapewnia, że klucze są aktualizowane zarówno w lokalnym środowisku AD DS, jak i w usłudze Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Dlaczego potrzebujemy Azure AD Connect? Czy zapisuje wszystkie informacje z powrotem do AD DS z usługi Azure AD?

Azure AD Connect nie zapisuje informacji z powrotem z usługi Azure AD, aby AD DS. Narzędzie zawiera moduł programu PowerShell umożliwiający utworzenie obiektu serwera Kerberos w AD DS i opublikowanie go w usłudze Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Co ma wyglądać żądanie HTTP/odpowiedź podczas żądania PRT + częściowego biletu TGT?

Żądanie HTTP to standardowe żądanie podstawowego tokenu odświeżania (PRT). To żądanie PRT zawiera żądanie wskazujące bilet uprawniający do przyznania biletu protokołu Kerberos (TGT).

| Claim | Wartość | Opis                             |
|-------|-------|-----------------------------------------|
| biletu TGT   | true  | Element Claim wskazuje, że klient potrzebuje biletu TGT. |

Usługa Azure AD łączy zaszyfrowany klucz klienta i bufor komunikatów z odpowiedzią PRT jako dodatkowe właściwości. Ładunek jest szyfrowany przy użyciu klucza sesji urządzenia usługi Azure AD.

| Pole              | Typ   | Opis  |
|--------------------|--------|--------------|
| tgt_client_key     | ciąg | Klucz klienta szyfrowany algorytmem Base64 (tajny). Ten klucz jest kluczem tajnym klienta używanym do ochrony biletu TGT. W tym scenariuszu z hasłami klucz tajny klienta jest generowany przez serwer jako część każdego żądania TGT, a następnie zwracany do klienta w odpowiedzi. |
| tgt_key_type       | int    | Typ klucza AD DS lokalnego używany zarówno dla klucza klienta, jak i klucza sesji protokołu Kerberos zawartych w KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | ciąg | KERB_MESSAGE_BUFFER zakodowane w formacie base64. |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kluczami zabezpieczeń FIDO2 i dostępem hybrydowym do zasobów lokalnych, zobacz następujące artykuły:

* [Klucze zabezpieczeń FIDO2 bez hasła](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hasła](howto-authentication-passwordless-security-key-windows.md)
* [Środowisko lokalne bez hasła](howto-authentication-passwordless-security-key-on-premises.md)
