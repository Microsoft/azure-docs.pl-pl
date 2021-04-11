---
title: Odnawianie certyfikatów dla Microsoft 365 i użytkowników usługi Azure AD | Microsoft Docs
description: W tym artykule wyjaśniono, jak Microsoft 365 użytkowników, jak rozwiązywać problemy z wiadomościami e-mail powiadamiających o odnowieniu certyfikatu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e81cb9018d817fb206915a81fdc3bdd60f6b08c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611892"
---
# <a name="renew-federation-certificates-for-microsoft-365-and-azure-active-directory"></a>Odnawianie certyfikatów Federacji dla Microsoft 365 i Azure Active Directory
## <a name="overview"></a>Omówienie
W przypadku pomyślnej federacji między Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS) certyfikaty używane przez AD FS do podpisywania tokenów zabezpieczeń do usługi Azure AD powinny być zgodne z konfiguracją w usłudze Azure AD. Niezgodność może prowadzić do zerwania zaufania. Usługa Azure AD zapewnia synchronizację tych informacji podczas wdrażania AD FS i serwera proxy aplikacji sieci Web (dostęp do ekstranetu).

> [!NOTE]
> Ten artykuł zawiera informacje dotyczące manging cerficates Federacji.  Aby uzyskać instrukcje dotyczące rotacji awaryjnej [, zobacz rotacja awaryjna certyfikatów AD FS](how-to-connect-emergency-ad-fs-certificate-rotation.md)

Ten artykuł zawiera dodatkowe informacje umożliwiające zarządzanie certyfikatami podpisywania tokenu i synchronizowanie ich z usługą Azure AD w następujących przypadkach:

* Serwer proxy aplikacji sieci Web nie jest wdrażany i w związku z tym metadane federacji nie są dostępne w ekstranecie.
* Nie używasz domyślnej konfiguracji AD FS dla certyfikatów podpisywania tokenu.
* Używasz innego dostawcy tożsamości.

> [!IMPORTANT]
> Firma Microsoft zdecydowanie zaleca korzystanie z sprzętowego modułu zabezpieczeń (HSM) do ochrony i zabezpieczania certyfikatów.
> Aby uzyskać więcej informacji, zobacz [sprzętowy moduł zabezpieczeń](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) w obszarze najlepsze rozwiązania dotyczące zabezpieczania AD FS.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Domyślna konfiguracja AD FS dla certyfikatów podpisywania tokenu
Certyfikaty podpisywania tokenu i odszyfrowywania tokenów są zwykle certyfikatami z podpisem własnym i są dobre przez jeden rok. Domyślnie AD FS obejmuje proces autoodnawiania o nazwie **AutoCertificateRollover**. Jeśli używasz AD FS 2,0 lub nowszego, Microsoft 365 i usługa Azure AD automatycznie zaktualizuje certyfikat przed jego wygaśnięciem.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Powiadomienie o odnowieniu z centrum administracyjnego Microsoft 365 lub wiadomości e-mail
> [!NOTE]
> Jeśli otrzymasz wiadomość e-mail lub powiadomienie portalu z prośbą o odnowienie certyfikatu dla pakietu Office, zobacz [Zarządzanie zmianami certyfikatów podpisywania tokenów](#managecerts) w celu sprawdzenia, czy konieczne jest wykonanie jakiejkolwiek akcji. Firma Microsoft wie o możliwym problemie, który może prowadzić do otrzymywania powiadomień o odnowieniu certyfikatu, nawet jeśli nie jest wymagane żadne działanie.
>
>

Usługa Azure AD próbuje monitorować metadane federacji i aktualizować certyfikaty podpisywania tokenu zgodnie z opisem w tych metadanych. 30 dni przed wygaśnięciem tokenu certyfikaty podpisywania usługi Azure AD sprawdza, czy nowe certyfikaty są dostępne przez sondowanie metadanych Federacji.

* Jeśli może pomyślnie przeprowadzić sondowanie metadanych Federacji i pobrać nowe certyfikaty, w centrum administracyjnym Microsoft 365 nie zostanie wystawione powiadomienie e-mail ani ostrzeżenie.
* Jeśli nie można pobrać nowych tokenów certyfikatów podpisywania, ponieważ metadane federacji są nieosiągalne lub automatyczne Przerzucanie certyfikatów nie jest włączone, usługa Azure AD wystawia powiadomienie e-mail i ostrzeżenie w centrum administracyjnym Microsoft 365.

![Powiadomienie w portalu pakietu Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Jeśli używasz AD FS, aby zapewnić ciągłość działania, sprawdź, czy serwery mają następujące aktualizacje, aby nie nastąpiły błędy uwierzytelniania dla znanych problemów. Pozwala to wyeliminować znane AD FS problemy z serwerem proxy dla tego odnowienia i przyszłych okresów odnawiania:
>
> Serwer 2012 R2 — [pakiet zbiorczy aktualizacji systemu Windows Server 2014](https://support.microsoft.com/kb/2955164)
>
> Serwer 2008 R2 i 2012 — [uwierzytelnianie przy użyciu serwera proxy kończy się niepowodzeniem w systemie Windows Server 2012 lub windows 2008 R2 z dodatkiem SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Sprawdź, czy certyfikaty wymagają aktualizacji <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1. sprawdzenie stanu AutoCertificateRollover
Na serwerze AD FS Otwórz program PowerShell. Sprawdź, czy wartość AutoCertificateRollover jest ustawiona na wartość true.

```azurepowershell-interactive
Get-Adfsproperties
```

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Jeśli używasz AD FS 2,0, najpierw uruchom Add-Pssnapin Microsoft. ADFS. PowerShell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2. potwierdzenie, że AD FS i usługa Azure AD są zsynchronizowane
Na serwerze AD FS Otwórz wiersz polecenia programu PowerShell MSOnline i Połącz się z usługą Azure AD.

> [!NOTE]
> MSOL-Cmdlets są częścią modułu MSOnline PowerShell.
> Moduł MSOnline PowerShell można pobrać bezpośrednio z Galeria programu PowerShell.
> 
>

```azurepowershell-interactive
Install-Module MSOnline
```

Nawiązywanie połączenia z usługą Azure AD przy użyciu programu MSOnline PowerShell-module.

```azurepowershell-interactive
Import-Module MSOnline
Connect-MsolService
```

Sprawdź certyfikaty skonfigurowane w AD FS i właściwości zaufania usługi Azure AD dla określonej domeny.

```azurepowershell-interactive
Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate
```

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Jeśli odciski palców w obu danych wyjściowych są zgodne, certyfikaty są synchronizowane z usługą Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3. sprawdzenie, czy certyfikat wkrótce wygaśnie
W danych wyjściowych elementu Get-MsolFederationProperty lub Get-AdfsCertificate Sprawdź datę w obszarze "nie po". Jeśli data jest krótsza niż 30 dni, należy podjąć odpowiednie działania.

| AutoCertificateRollover | Certyfikaty zsynchronizowane z usługą Azure AD | Metadane federacji są publicznie dostępne | Ważności | Akcja |
|:---:|:---:|:---:|:---:|:---:|
| Tak |Tak |Tak |- |Nie jest wymagane żadne działanie. Zobacz [automatyczne odnawianie tokenu certyfikatu podpisywania](#autorenew). |
| Tak |Nie |- |Mniej niż 15 dni |Odnów natychmiast. Zobacz [ręcznie odnowienie certyfikatu podpisywania tokenu](#manualrenew). |
| Nie |- |- |Mniej niż 30 dni |Odnów natychmiast. Zobacz [ręcznie odnowienie certyfikatu podpisywania tokenu](#manualrenew). |

\[-] Nie ma znaczenia

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Odnawianie certyfikatu podpisywania tokenu automatycznie (zalecane) <a name="autorenew"></a>
Nie musisz wykonywać żadnych czynności ręcznych, jeśli są spełnione oba poniższe warunki:

* Wdrożono serwer proxy aplikacji sieci Web, który umożliwia dostęp do metadanych Federacji z ekstranetu.
* Używasz domyślnej konfiguracji AD FS (AutoCertificateRollover jest włączona).

Sprawdź następujące, aby upewnić się, że certyfikat można automatycznie zaktualizować.

**1. Właściwość AD FS AutoCertificateRollover musi mieć wartość true.** Oznacza to, że AD FS automatycznie generują nowe certyfikaty podpisywania tokenu i odszyfrowywania tokenu przed wygaśnięciem starych.

**2. metadane federacji AD FS są publicznie dostępne.** Sprawdź, czy metadane federacji są publicznie dostępne, przechodząc do następującego adresu URL z komputera w publicznej sieci Internet (poza siecią firmową):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

gdzie `(your_FS_name)` jest zastępowana nazwą hosta usługi federacyjnej używanej przez organizację, taką jak FS.contoso.com.  Jeśli można pomyślnie zweryfikować oba te ustawienia, nie trzeba wykonywać żadnych innych czynności.  

Przykład: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Ręcznie odnów certyfikat podpisywania tokenu <a name="manualrenew"></a>
Możesz zdecydować się na ręczne odnowienie certyfikatów podpisywania tokenu. Na przykład następujące scenariusze mogą usprawnić się przed ręcznym odnowieniem:

* Certyfikaty podpisywania tokenu nie są certyfikatami z podpisem własnym. Najbardziej typową przyczyną jest to, że organizacja zarządza certyfikatami AD FS zarejestrowanymi na podstawie organizacyjnego urzędu certyfikacji.
* Zabezpieczenia sieciowe nie umożliwiają publicznego udostępniania metadanych Federacji.

W tych scenariuszach za każdym razem, gdy aktualizujesz tokeny certyfikatów podpisywania, należy również zaktualizować domenę Microsoft 365 za pomocą polecenia programu PowerShell Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1. Upewnij się, że AD FS ma nowy token certyfikaty podpisywania
**Konfiguracja niedomyślna**

Jeśli używasz konfiguracji innej niż domyślna AD FS (gdzie **AutoCertificateRollover** ma wartość **false**), prawdopodobnie używasz certyfikatów niestandardowych (nie z podpisem własnym). Aby uzyskać więcej informacji na temat odnawiania tokenów AD FS certyfikatów podpisywania, zobacz [wymagania dotyczące certyfikatów dla serwerów federacyjnych](/windows-server/identity/ad-fs/design/certificate-requirements-for-federation-servers).

**Metadane federacji nie są publicznie dostępne**

Z drugiej strony, jeśli **AutoCertificateRollover** ma **wartość true**, ale metadane federacji nie są publicznie dostępne, najpierw upewnij się, że nowe certyfikaty podpisywania tokenu zostały wygenerowane przez AD FS. Upewnij się, że masz nowe certyfikaty podpisywania tokenu, wykonując następujące czynności:

1. Sprawdź, czy użytkownik jest zalogowany na podstawowym serwerze AD FS.
2. Sprawdź bieżące certyfikaty podpisywania w AD FS, otwierając okno poleceń programu PowerShell i uruchamiając następujące polecenie:

    PS C: \> Get-AdfsCertificate — podpisywania tokenu certyfikatu

   > [!NOTE]
   > Jeśli używasz AD FS 2,0, należy najpierw uruchomić Add-Pssnapin Microsoft. ADFS. PowerShell.
   >
   >
3. Spójrz na dane wyjściowe polecenia na liście wymienionych certyfikatów. Jeśli AD FS wygenerował nowy certyfikat, w danych wyjściowych powinny być widoczne dwa certyfikaty: jeden, dla którego wartość **isprimary** jest **równa true** , a Data **NotAfter** przypada w ciągu 5 dni, a dla którego właściwość **isprimary** ma wartość **false** , a **NotAfter** to rok w przyszłości.
4. Jeśli widzisz tylko jeden certyfikat, a Data **NotAfter** w ciągu 5 dni, musisz wygenerować nowy certyfikat.
5. Aby wygenerować nowy certyfikat, wykonaj następujące polecenie w wierszu polecenia programu PowerShell: `PS C:\Update-ADFSCertificate –CertificateType token-signing` .
6. Sprawdź aktualizację, ponownie uruchamiając następujące polecenie: PS C: \> Get-AdfsCertificate – CertificateType token-Signing

Dwa certyfikaty powinny być wymienione teraz, z których jedna ma datę **NotAfter** o około jeden rok w przyszłości, a dla której wartość **isprimary** jest **równa false**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-microsoft-365-trust"></a>Krok 2. aktualizowanie certyfikatów podpisywania nowego tokenu dla Microsoft 365 zaufania
Zaktualizuj Microsoft 365 przy użyciu nowych tokenów certyfikatów podpisywania, które mają być używane na potrzeby zaufania, w następujący sposób.

1. Otwórz Moduł Microsoft Azure Active Directory dla Windows PowerShell.
2. Uruchom $cred = Get-Credential. Gdy to polecenie cmdlet zostanie wyświetlony komunikat z prośbą o poświadczenia, wpisz poświadczenia konta administratora usługi w chmurze.
3. Uruchom Connect-MsolService — $cred poświadczeń. To polecenie cmdlet nawiązuje połączenie z usługą w chmurze. Utworzenie kontekstu, który nawiązuje połączenie z usługą w chmurze, jest wymagane przed uruchomieniem któregokolwiek z dodatkowych poleceń cmdlet zainstalowanych przez to narzędzie.
4. Jeśli te polecenia są uruchamiane na komputerze, który nie jest AD FS podstawowym serwerem federacyjnym, uruchom polecenie Set-MSOLAdfscontext-Computer &lt; AD FS Server Primary &gt; , gdzie &lt; AD FS serwer podstawowy &gt; to wewnętrzna nazwa FQDN serwera podstawowego AD FS. To polecenie cmdlet tworzy kontekst, który nawiązuje połączenie z AD FS.
5. Uruchom Update-MSOLFederatedDomain – Nazwa_domenyname &lt; domeny &gt; . To polecenie cmdlet aktualizuje ustawienia z AD FS w usłudze w chmurze i konfiguruje relacje zaufania między nimi.

> [!NOTE]
> Jeśli konieczne jest obsługę wielu domen najwyższego poziomu, takich jak contoso.com i fabrikam.com, należy użyć przełącznika **SupportMultipleDomain** z dowolnym poleceniem cmdlet. Aby uzyskać więcej informacji, zobacz [Obsługa wielu domen najwyższego poziomu](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Napraw relację zaufania usługi Azure AD za pomocą Azure AD Connect <a name="connectrenew"></a>
W przypadku skonfigurowania farmy AD FS i zaufania usługi Azure AD przy użyciu Azure AD Connect, można użyć Azure AD Connect do wykrycia, czy konieczne jest wykonanie jakiejkolwiek akcji dotyczącej certyfikatów podpisywania tokenu. Jeśli musisz odnowić certyfikaty, możesz użyć Azure AD Connect w tym celu.

Aby uzyskać więcej informacji, zobacz temat [Naprawa zaufania](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS i kroki aktualizacji certyfikatu usługi Azure AD
Certyfikaty podpisywania tokenu to standardowe certyfikaty x509, które są używane do bezpiecznego podpisywania wszystkich tokenów, których dotyczy serwer federacyjny. Certyfikaty odszyfrowywania tokenów są standardowymi certyfikatami x509 używanymi do odszyfrowywania wszelkich tokenów przychodzących. 

Domyślnie program AD FS jest skonfigurowany do automatycznego generowania certyfikatów podpisywania tokenu i odszyfrowywania tokenów, zarówno w czasie początkowej konfiguracji, jak i w przypadku, gdy certyfikaty zbliżają się do daty wygaśnięcia.

Usługa Azure AD próbuje pobrać nowy certyfikat z metadanych usługi federacyjnej 30 dni przed wygaśnięciem bieżącego certyfikatu. W przypadku gdy w tym momencie nowy certyfikat nie jest dostępny, usługa Azure AD będzie kontynuowała monitorowanie metadanych w regularnych odstępach czasu. Po udostępnieniu nowego certyfikatu w metadanych ustawienia Federacji dla domeny są aktualizowane przy użyciu nowych informacji o certyfikacie. Możesz użyć, `Get-MsolDomainFederationSettings` Aby sprawdzić, czy nowy certyfikat jest wyświetlany w NextSigningCertificate/SigningCertificate.

Aby uzyskać więcej informacji o tokenach podpisywania certyfikatów w AD FS zobacz [Uzyskaj i Konfiguruj certyfikaty podpisywania tokenu i odszyfrowywania tokenów dla AD FS](/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
