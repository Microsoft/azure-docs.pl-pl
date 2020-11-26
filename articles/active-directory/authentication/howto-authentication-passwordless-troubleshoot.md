---
title: Znane problemy i rozwiązywanie problemów z kluczami zabezpieczeń hybrydowych FIDO2 — Azure Active Directory
description: Informacje o znanych problemach i sposobach rozwiązywania problemów związanych z logowaniem przy Azure Active Directory użyciu klucza zabezpieczeń FIDO2 hybrydowego
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 690d4761657b8bf6e5ba63ddfbce7163584e64e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174038"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Rozwiązywanie problemów z wdrożeniami hybrydowymi kluczy zabezpieczeń FIDO2 w usłudze Azure AD (wersja zapoznawcza)

W tym artykule opisano często zadawane pytania dotyczące urządzeń przyłączonych do hybrydowej usługi Azure AD oraz logowania bezhasła do zasobów Premium. Dzięki tej funkcji bez hasła można włączyć uwierzytelnianie usługi Azure AD na urządzeniach z systemem Windows 10 dla urządzeń przyłączonych do hybrydowej usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2. Użytkownicy mogą logować się do systemu Windows na swoich urządzeniach przy użyciu nowoczesnych poświadczeń, takich jak klucze FIDO2 i dostęp do zasobów tradycyjnych Active Directory Domain Services (AD DS) za pomocą bezproblemowego logowania jednokrotnego (SSO) do zasobów Premium.

Obsługiwane są następujące scenariusze dla użytkowników w środowisku hybrydowym:

* Zaloguj się do urządzeń przyłączonych do hybrydowej usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 i uzyskaj dostęp do logowania jednokrotnego do zasobów Premium.
* Zaloguj się do urządzeń przyłączonych do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 i uzyskaj dostęp z logowaniem jednokrotnym do zasobów Premium.

Aby rozpocząć pracę z kluczami zabezpieczeń FIDO2 i dostępem hybrydowym do zasobów lokalnych, zobacz następujące artykuły:

* [Klucze zabezpieczeń bezhasło](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hasła](howto-authentication-passwordless-security-key-windows.md)
* [Lokalne hasła](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="known-issues"></a>Znane problemy

* [Użytkownicy nie mogą zalogować się przy użyciu kluczy zabezpieczeń FIDO2, ponieważ funkcja Windows Hello front jest zbyt szybka i jest domyślnym mechanizmem logowania](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Użytkownicy nie mogą używać kluczy zabezpieczeń FIDO2 natychmiast po utworzeniu hybrydowej maszyny połączonej z usługą Azure AD](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Użytkownicy nie mogą pobrać logowania jednokrotnego do zasobu sieciowego NTLM po zalogowaniu się przy użyciu klucza zabezpieczeń FIDO2 i otrzymaniu monitu o poświadczenia](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Użytkownicy nie mogą zalogować się przy użyciu kluczy zabezpieczeń FIDO2, ponieważ funkcja Windows Hello front jest zbyt szybka i jest domyślnym mechanizmem logowania

Funkcja Windows Hello front jest zamierzonym najlepszym doświadczeniem dla urządzenia, na którym użytkownik jest zarejestrowany. Klucze zabezpieczeń FIDO2 są przeznaczone do użycia na urządzeniach udostępnionych lub w przypadku, gdy rejestracja w usłudze Windows Hello dla firm jest barierą.

Jeśli funkcja Windows Hello Front uniemożliwia użytkownikom podejmowanie prób logowania za pomocą klucza zabezpieczeń FIDO2, użytkownicy mogą wyłączyć logowanie do usługi Hello, usuwając pozycję Rejestrowanie w obszarze **ustawienia > Sign-In opcje**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Użytkownicy nie mogą używać kluczy zabezpieczeń FIDO2 natychmiast po utworzeniu hybrydowej maszyny połączonej z usługą Azure AD

Po dołączeniu do domeny i ponownym uruchomieniu procesu w czystej instalacji hybrydowej maszyny połączonej z usługą Azure AD należy zalogować się przy użyciu hasła i poczekać na synchronizację zasad przed użyciem klucza zabezpieczeń FIDO2 do zalogowania się.

To zachowanie jest znanym ograniczeniem dla urządzeń przyłączonych do domeny i nie jest specyficzne dla kluczy zabezpieczeń FIDO2.

Aby sprawdzić bieżący stan, użyj `dsregcmd /status` polecenia. Sprawdź, czy zarówno *AzureAdJoined* , jak i *DomainJoined* Pokaż *tak*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Użytkownicy nie mogą pobrać logowania jednokrotnego do zasobu sieciowego NTLM po zalogowaniu się przy użyciu klucza zabezpieczeń FIDO2 i otrzymaniu monitu o poświadczenia

Upewnij się, że w czasie obsługi żądania zasobu jest dostępna wystarczająca liczba kontrolerów domeny. Aby sprawdzić, czy można zobaczyć serwer z uruchomioną funkcją, Przejrzyj dane wyjściowe `nltest /dsgetdc:<dc name> /keylist /kdc`

Jeśli widzisz kontroler domeny z tą funkcją, hasło użytkownika mogło ulec zmianie od momentu zalogowania się lub Wystąpił inny problem. Zbierz dzienniki zgodnie z opisem w poniższej sekcji dotyczącej zespołu pomocy technicznej firmy Microsoft w celu debugowania.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Istnieją dwa obszary rozwiązywania problemów z [klientami systemu Windows](#windows-client-issues)lub [problemów z wdrażaniem](#deployment-issues).

### <a name="windows-client-issues"></a>Problemy z klientami systemu Windows

Aby zbierać dane ułatwiające rozwiązywanie problemów z logowaniem się do systemu Windows lub uzyskiwanie dostępu do zasobów lokalnych z urządzeń z systemem Windows 10, wykonaj następujące czynności:

1. Otwórz aplikację **centrum opinii** . Upewnij się, że Twoja nazwa znajduje się w lewym dolnym rogu aplikacji, a następnie wybierz pozycję **Utwórz nowy element opinii**.

    W polu Typ elementu opinii wybierz pozycję *problem*.

1. Wybierz kategorię *zabezpieczenia i prywatność* , a następnie podkategorię *Fido* .
1. Przełącz pole wyboru *wysyłania dołączonych plików i diagnostyki do firmy Microsoft wraz z moją opinią*.
1. Wybierz pozycję *Utwórz ponownie moje problemy*, a następnie *Rozpocznij przechwytywanie*.
1. Zablokuj i Odblokuj maszynę z kluczem zabezpieczeń FIDO2. Jeśli wystąpi problem, spróbuj odblokować przy użyciu innych poświadczeń.
1. Wróć do **centrum opinii**, wybierz pozycję **Zatrzymaj przechwytywanie** i Prześlij swoją opinię.
1. Przejdź do strony *Opinie* , a następnie kartę *moje opinie* . Wybierz ostatnio przesłaną opinię.
1. Wybierz przycisk *Udostępnij* w prawym górnym rogu, aby uzyskać link do opinii. Dołącz ten link w przypadku otwarcia zgłoszenia do pomocy technicznej lub Odpowiedz inżynierowi przypisanemu do istniejącego przypadku pomocy technicznej.

Zbierane są następujące dzienniki zdarzeń i informacje o kluczu rejestru:

**Klucze rejestru**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Informacje diagnostyczne**

* Zrzut jądra na żywo
* Zbierz informacje o pakiecie AppX
* Pliki kontekstowe UIF

**Dzienniki zdarzeń**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Problemy z wdrażaniem

Aby rozwiązać problemy z wdrażaniem serwera usługi Azure AD Kerberos, Użyj nowego modułu programu PowerShell dołączonego do Azure AD Connect.

#### <a name="viewing-the-logs"></a>Wyświetlanie dzienników

Polecenia cmdlet programu PowerShell serwera Kerberos usługi Azure AD używają tego samego rejestrowania, co w Kreatorze standardowej Azure AD Connect. Aby wyświetlić informacje lub szczegóły błędu z poleceń cmdlet, wykonaj następujące czynności:

1. Na serwerze Azure AD Connect przejdź do `C:\ProgramData\AADConnect\` . Ten folder jest domyślnie ukryty.
1. Otwórz i Wyświetl najnowszy `trace-*.log` plik znajdujący się w katalogu.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Wyświetlanie obiektów serwera Kerberos w usłudze Azure AD

Aby wyświetlić obiekty serwera Kerberos w usłudze Azure AD i sprawdzić, czy są one w dobrym porządku, wykonaj następujące czynności:

1. Na serwerze Azure AD Connect Otwórz program PowerShell i przejdź do `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Uruchom następujące polecenia programu PowerShell, aby wyświetlić serwer Kerberos usługi Azure AD zarówno z usługi Azure AD, jak i do AD DS lokalnych.

    Zastąp *Corp.contoso.com* nazwą lokalnej domeny AD DS.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Polecenie wyprowadza właściwości serwera Kerberos usługi Azure AD z usługi Azure AD i AD DS lokalnych. Przejrzyj właściwości, aby upewnić się, że wszystko jest w dobrej kolejności. Użyj poniższej tabeli, aby sprawdzić właściwości.

Pierwszy zestaw właściwości pochodzi z obiektów w środowisku lokalnym AD DS. Druga połowa (właściwości zaczynające się od * Cloud * *) pochodzą z obiektu serwera Kerberos w usłudze Azure AD:

| Właściwość           | Opis  |
|--------------------|--------------|
| Id                 | Unikatowy *Identyfikator* obiektu AD DS kontrolera domeny. |
| DomainDnsName      | Nazwa domeny DNS AD DS domeny. |
| ComputerAccount    | Obiekt konta komputera serwera usługi Azure AD Kerberos (kontroler domeny). |
| UserAccount        | Obiekt wyłączonego konta użytkownika, który zawiera klucz szyfrowania TGT serwera Kerberos usługi Azure AD. Nazwa DN tego konta to *CN = krbtgt_AzureAD, CN = users, <domena-DN>* |
| Wersja programu         | Wersja klucza klucza szyfrowania biletu usługi Azure AD Kerberos Server. Wersja zostanie przypisana podczas tworzenia klucza. Wersja jest następnie zwiększana za każdym razem, gdy klucz zostanie obrócony. Przyrosty są oparte na metadanych replikacji i prawdopodobnie będą większe niż jeden.<br /><br /> Na przykład początkowa *wersja* może być *192272*. Gdy klucz zostanie obrócony po raz pierwszy, wersja może przejść do *212621*.<br /><br /> Ważne jest, aby sprawdzić, czy *wersja* usługi dla obiektu lokalnego i *CloudKeyVersion* dla obiektu w chmurze jest taka sama. |
| KeyUpdatedOn       | Data i godzina zaktualizowania lub utworzenia klucza szyfrowania TGT serwera Kerberos usługi Azure AD. |
| KeyUpdatedFrom     | Kontroler domeny, na którym został ostatnio zaktualizowany klucz szyfrowania TGT serwera Kerberos usługi Azure AD. |
| CloudId            | *Identyfikator* z obiektu usługi Azure AD. Musi być zgodna z powyższym *identyfikatorem* . |
| CloudDomainDnsName | *DomainDnsName* z obiektu usługi Azure AD. Musi być zgodna z *DomainDnsName* powyżej. |
| CloudKeyVersion    | *Wersja* z obiektu usługi Azure AD. Musi być zgodna z powyższą *wersją* . |
| CloudKeyUpdatedOn  | *KeyUpdatedOn* z obiektu usługi Azure AD. Musi być zgodna z *KeyUpdatedOn* powyżej. |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kluczami zabezpieczeń FIDO2 i dostępem hybrydowym do zasobów lokalnych, zobacz następujące artykuły:

* [Klucze zabezpieczeń FIDO2 bez hasła](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hasła](howto-authentication-passwordless-security-key-windows.md)
* [Środowisko lokalne bez hasła](howto-authentication-passwordless-security-key-on-premises.md)
