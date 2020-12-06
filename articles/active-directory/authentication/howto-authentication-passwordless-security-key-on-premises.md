---
title: Logowanie przy użyciu klucza zabezpieczeń z hasłem do zasobów lokalnych (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak włączyć logowanie za pomocą klucza zabezpieczeń bezhasło do zasobów lokalnych przy użyciu Azure Active Directory (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1459442f7d99d1de88a685eed34493da530c1a4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743483"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Włącz logowanie za pomocą klucza zabezpieczeń bez hasła do zasobów lokalnych przy użyciu Azure Active Directory (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła do zasobów lokalnych w środowiskach z **dołączonymi usługami Azure AD** i **hybrydowymi** urządzeniami z systemem Windows 10 przyłączonymi do usługi Azure AD. Ta funkcja zapewnia bezproblemowe logowanie jednokrotne do zasobów lokalnych przy użyciu kluczy zabezpieczeń zgodnych z firmą Microsoft.

> [!NOTE]
> Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Logowanie jednokrotne do zasobów lokalnych przy użyciu kluczy FIDO2

Azure Active Directory (AD) może wystawić bilety uprawniające do przyznania biletu Kerberos (TGT) dla co najmniej jednej domeny Active Directory. Ta funkcja umożliwia użytkownikom logowanie się do systemu Windows przy użyciu nowoczesnych poświadczeń, takich jak FIDO2 Security Keys i dostęp do tradycyjnych zasobów opartych na Active Directory. Bilety i autoryzacje usług Kerberos nadal są kontrolowane przez lokalne Active Directory kontrolery domeny.

Obiekt serwera Kerberos usługi Azure AD jest tworzony w Active Directory lokalnym, a następnie bezpiecznie publikowany w Azure Active Directory. Obiekt nie jest skojarzony z żadnym serwerem fizycznym. Jest to po prostu zasób, który może być używany przez Azure Active Directory do generowania TGT Kerberos dla domena usługi Active Directory.

![Uzyskiwanie biletu uprawniającego do przyznania biletu (TGT) z usługi Azure AD i AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Użytkownik loguje się na urządzeniu z systemem Windows 10 przy użyciu klucza zabezpieczeń FIDO2 i uwierzytelnia się w usłudze Azure AD.
1. Usługa Azure AD sprawdza katalog pod kątem klucza serwera Kerberos zgodnego z lokalną domeną usługi AD użytkownika.
   1. Usługa Azure AD generuje bilet TGT protokołu Kerberos dla lokalnej domeny usługi AD użytkownika. Bilet TGT zawiera tylko identyfikator SID użytkownika. Bilet TGT nie zawiera żadnych danych autoryzacji.
1. Bilet TGT jest zwracany do klienta wraz z tokenem podstawowego odświeżania usługi Azure AD (PRT).
1. Komputer kliencki kontaktuje się z lokalnym kontrolerem domeny usługi AD i wymienia częściowy bilet TGT dla w pełni uformowanego biletu TGT.
1. Komputer kliencki ma teraz usługę Azure AD PRT i pełną Active Directory bilet TGT i mogą uzyskać dostęp do zasobów w chmurze i lokalnych.

## <a name="requirements"></a>Wymagania

Przed wykonaniem kroków opisanych w tym artykule organizacje muszą wykonać kroki, aby [włączyć klucz zabezpieczeń bezhasła do urządzeń z systemem Windows 10 (wersja zapoznawcza)](howto-authentication-passwordless-security-key.md) .

Organizacje muszą również spełniać następujące wymagania dotyczące oprogramowania.

- Na urządzeniach musi działać system Windows 10 w wersji 2004 lub nowszej.
- Musisz mieć wersję 1.4.32.0 lub nowszą [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Aby uzyskać więcej informacji na temat dostępnych opcji uwierzytelniania hybrydowego usługi Azure AD, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../hybrid/choose-ad-authn.md) i [Wybierz typ instalacji, który ma być używany przez Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Na kontrolerach domeny systemu Windows Server muszą być zainstalowane następujące poprawki:
    - W przypadku systemu Windows Server 2016 — https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - W przypadku systemu Windows Server 2019 — https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Obsługiwane scenariusze

Scenariusz obsługuje logowanie jednokrotne (SSO) w obu następujących scenariuszach:

- Dla zasobów w chmurze, takich jak Microsoft 365 i inne aplikacje z obsługą protokołu SAML.
- W przypadku zasobów lokalnych oraz Windows-Integrated uwierzytelniania w witrynach sieci Web. Zasoby mogą obejmować witryny sieci Web i witryny programu SharePoint, które wymagają uwierzytelniania usług IIS i/lub zasoby korzystające z uwierzytelniania NTLM.

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane:

- Wdrożenie domeny Active Directory Domain Services systemu Windows Server (AD DS) (urządzenia tylko lokalne).
- Scenariusze dotyczące protokołu RDP, infrastruktury VDI i Citrix przy użyciu klucza zabezpieczeń.
- S/MIME przy użyciu klucza zabezpieczeń.
- "Uruchom jako" przy użyciu klucza zabezpieczeń.
- Zaloguj się do serwera przy użyciu klucza zabezpieczeń.

## <a name="create-kerberos-server-object"></a>Utwórz obiekt serwera Kerberos

Administratorzy używają narzędzi programu PowerShell z serwera Azure AD Connect do tworzenia obiektu serwera Kerberos usługi Azure AD w katalogu lokalnym. Uruchom następujące kroki w każdej domenie i lesie w organizacji zawierającej użytkowników usługi Azure AD:

1. Uaktualnij do najnowszej wersji Azure AD Connect. W instrukcjach przyjęto założenie, że już skonfigurowano Azure AD Connect do obsługi środowiska hybrydowego.
1. Na serwerze Azure AD Connect Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień i przejdź do `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć nowy obiekt serwera usługi Azure AD Kerberos w lokalnej domenie Active Directory i Azure Active Directory dzierżawie.

> [!NOTE]
> Zastąp `contoso.corp.com` ciąg w poniższym przykładzie nazwą domeny Active Directory lokalnej.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Przeglądanie i weryfikowanie serwera usługi Azure AD Kerberos

Można wyświetlić i sprawdzić nowo utworzony serwer Kerberos usługi Azure AD za pomocą następującego polecenia:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

To polecenie wyświetla właściwości serwera Kerberos usługi Azure AD. Możesz przejrzeć właściwości, aby upewnić się, że wszystko jest w dobrej kolejności.

| Właściwość | Opis |
| --- | --- |
| ID (Identyfikator) | Unikatowy identyfikator obiektu kontrolera domeny AD DS. Ten identyfikator jest czasami określany jako "gniazdo" lub "Identyfikator rozgałęzienia". |
| DomainDnsName | Nazwa domeny DNS domena usługi Active Directory. |
| ComputerAccount | Obiekt konta komputera serwera usługi Azure AD Kerberos (kontroler domeny). |
| UserAccount | Obiekt wyłączonego konta użytkownika, który zawiera klucz szyfrowania TGT serwera Kerberos usługi Azure AD. Nazwa wyróżniająca tego konta to `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Wersja programu | Wersja klucza klucza szyfrowania biletu usługi Azure AD Kerberos Server. Wersja zostanie przypisana podczas tworzenia klucza. Wersja jest następnie zwiększana za każdym razem, gdy klucz zostanie obrócony. Przyrosty są oparte na metadanych replikacji i największej niż jeden. Na przykład początkowa *wersja* może być *192272*. Gdy klucz zostanie obrócony po raz pierwszy, wersja może przejść do *212621*. Ważne jest, aby sprawdzić, czy *wersja* usługi dla obiektu lokalnego i *CloudKeyVersion* dla obiektu w chmurze jest taka sama. |
| KeyUpdatedOn | Data i godzina zaktualizowania lub utworzenia klucza szyfrowania TGT serwera Kerberos usługi Azure AD. |
| KeyUpdatedFrom | Kontroler domeny, na którym został ostatnio zaktualizowany klucz szyfrowania TGT serwera Kerberos usługi Azure AD. |
| CloudId | Identyfikator z obiektu usługi Azure AD. Musi być zgodna z powyższym IDENTYFIKATORem. |
| CloudDomainDnsName | *DomainDnsName* z obiektu usługi Azure AD. Musi być zgodna z *DomainDnsName* powyżej. |
| CloudKeyVersion | *Wersja* z obiektu usługi Azure AD. Musi być zgodna z powyższą *wersją* . |
| CloudKeyUpdatedOn | *KeyUpdatedOn* z obiektu usługi Azure AD. Musi być zgodna z *KeyUpdatedOn* powyżej. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Obracanie klucza serwera Kerberos usługi Azure AD

Należy regularnie obrócić klucze KRBTGT szyfrowania serwera Kerberos usługi Azure AD. Zalecane jest przestrzeganie tego samego harmonogramu, który służy do rotacji wszystkich innych kluczy KRBTGT kontrolera domena usługi Active Directory.

> [!WARNING]
> Istnieją inne narzędzia, które mogą obrócić klucze KRBTGT, jednak należy użyć narzędzi wymienionych w tym dokumencie, aby obrócić klucze KRBTGT serwera Kerberos usługi Azure AD. Dzięki temu klucze są aktualizowane zarówno w lokalnej usłudze AD, jak i w usłudze Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Usuwanie serwera usługi Azure AD Kerberos

Jeśli chcesz przywrócić scenariusz i usunąć serwer Kerberos usługi Azure AD zarówno z Active Directory lokalnych, jak i Azure Active Directory, uruchom następujące polecenie:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenariusze obejmujące wiele lasów i wiele domen

Obiekt serwera Kerberos usługi Azure AD jest reprezentowany w usłudze Azure AD jako obiekt *KerberosDomain* . Każda lokalna domena Active Directory jest reprezentowana jako pojedynczy obiekt *KerberosDomain* w usłudze Azure AD.

Na przykład organizacja ma Las Active Directory z dwiema domenami `contoso.com` i `fabrikam.com` . Jeśli zdecydujesz się zezwolić usłudze Azure AD na wystawienie protokołu Kerberos TGT dla całego lasu, istnieją dwa obiekty *KerberosDomain* w usłudze Azure AD. Jeden obiekt *KerberosDomain* dla `contoso.com` , i jeden dla `fabrikam.com` . Jeśli masz wiele Active Directory lasów, dla każdej domeny w każdym lesie istnieje jeden obiekt *KerberosDomain* .

Należy wykonać kroki, aby [utworzyć obiekt serwera Kerberos](#create-kerberos-server-object) w każdej domenie i lesie w organizacji zawierającej użytkowników usługi Azure AD.

## <a name="known-behavior"></a>Znane zachowanie

Logowanie za pomocą FIDO jest blokowane, jeśli hasło wygasło. Oczekiwana jest możliwość resetowania hasła przez użytkownika przed zalogowaniem się przy użyciu FIDO.

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkać problemy podczas wyświetlania podglądu tej funkcji, Udostępnij za pośrednictwem aplikacji centrum opinii o systemie Windows, wykonując następujące czynności:

1. Uruchom **centrum opinii** i upewnij się, że użytkownik jest zalogowany.
1. Prześlij opinię poniżej następującej kategoryzacji:
   - Kategoria: zabezpieczenia i prywatność
   - Podkategoria: FIDO
1. Aby przechwytywać dzienniki, użyj opcji, aby **ponownie utworzyć mój problem**

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-work-in-my-on-premises-environment"></a>Czy ta usługa działa w środowisku lokalnym?

Ta funkcja nie działa w przypadku czystego środowiska lokalnego Active Directory Domain Services (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moja organizacja wymaga uwierzytelniania dwuskładnikowego w celu uzyskania dostępu do zasobów. Co mogę zrobić, aby obsłużyć to wymaganie?

Klucze zabezpieczeń są dostępne w różnych aspektach. Skontaktuj się z producentem urządzenia, aby omówić, jak ich urządzenia mogą być włączone przy użyciu kodu PIN lub biometrycznego jako drugiego czynnika.

### <a name="can-admins-set-up-security-keys"></a>Czy Administratorzy mogą konfigurować klucze zabezpieczeń?

Pracujemy nad tą funkcją, aby uzyskać ogólną dostępność tej funkcji.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Gdzie mogę znaleźć zgodne klucze zabezpieczeń?

[FIDO2 klucze zabezpieczeń](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Co mam zrobić, Jeśli utracisz mój klucz zabezpieczeń?

Klucze z Azure Portal można usunąć, przechodząc do strony **informacje zabezpieczające** i usuwając klucz zabezpieczeń.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nie mogę używać FIDO natychmiast po utworzeniu hybrydowej maszyny połączonej z usługą Azure AD

W przypadku czystej instalacji hybrydowej maszyny połączonej z usługą Azure AD po przyłączeniu do domeny i ponownym uruchomieniu należy zalogować się przy użyciu hasła i poczekać na synchronizację zasad, zanim będzie możliwe zalogowanie się przy użyciu usługi FIDO.

- Sprawdź bieżący stan, wpisując `dsregcmd /status` w oknie wiersza polecenia i sprawdź, czy są wyświetlane zarówno *AzureAdJoined* , jak *YES* i *DomainJoined* .
- To opóźnienie jest znanym ograniczeniem dla urządzeń przyłączonych do domeny i nie jest specyficzne dla FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nie mogę pobrać logowania jednokrotnego do zasobu sieciowego NTLM po zalogowaniu się za pomocą programu FIDO i wyświetleniu monitu o poświadczenia

Upewnij się, że jest wystarczająca liczba kontrolerów domeny w celu uzyskania odpowiedzi na czas na obsługę żądania zasobu. Aby sprawdzić, czy widzisz kontroler domeny z uruchomioną funkcją, Przejrzyj dane wyjściowe `nltest /dsgetdc:contoso /keylist /kdc` .

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o bezhaseł](concept-authentication-passwordless.md)
