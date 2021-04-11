---
title: Rotacja awaryjna AD FS certyfikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak natychmiast odwołać i zaktualizować AD FS certyfikaty.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613126"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Rotacja awaryjna AD FS certyfikatów
W przypadku konieczności natychmiastowego obrócenia AD FS certyfikatów możesz wykonać czynności opisane poniżej w tej sekcji.

> [!IMPORTANT]
> Wykonanie poniższych kroków w środowisku AD FS spowoduje natychmiastowe odwołanie starych certyfikatów.  Ponieważ ta czynność jest wykonywana natychmiast, normalny czas, który zwykle może być używany przez partnerów federacyjnych do korzystania z nowego certyfikatu, zostanie zakończony. Może to spowodować awarię usługi, która będzie ufać aktualizacji do korzystania z nowych certyfikatów.  Należy rozwiązać ten problem, gdy wszyscy partnerzy federacyjny mają nowe certyfikaty.

> [!NOTE]
> Firma Microsoft zdecydowanie zaleca korzystanie z sprzętowego modułu zabezpieczeń (HSM) do ochrony i zabezpieczania certyfikatów.
> Aby uzyskać więcej informacji, zobacz [sprzętowy moduł zabezpieczeń](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) w obszarze najlepsze rozwiązania dotyczące zabezpieczania AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Określanie odcisku palca certyfikatu podpisywania tokenu
Aby można było odwołać certyfikat podpisywania starego tokenu, który jest obecnie używany AD FS, należy określić odcisk palca certyfikatu token-sigining.  Aby to zrobić, wykonaj następujące czynności:

 1. Nawiązywanie połączenia z usługą online firmy Microsoft `PS C:\>Connect-MsolService`
 2. Udokumentowanie odcisku palca i daty wygaśnięcia certyfikatu podpisywania tokenu lokalnego i w chmurze.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Skopiuj odcisk palca.  Będzie później używany do usuwania istniejących certyfikatów.

Odcisk palca można również uzyskać, korzystając z funkcji zarządzania AD FS, przechodząc do usługi/certyfikaty, klikając prawym przyciskiem myszy certyfikat, wybierając pozycję Wyświetl certyfikat, a następnie wybierając pozycję Szczegóły. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Określ, czy AD FS automatycznie odnawiać certyfikaty
Domyślnie program AD FS jest skonfigurowany do automatycznego generowania certyfikatów podpisywania tokenu i odszyfrowywania tokenów, zarówno w czasie początkowej konfiguracji, jak i w przypadku, gdy certyfikaty zbliżają się do daty wygaśnięcia.

Można uruchomić następujące polecenie programu Windows PowerShell: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

Właściwość AutoCertificateRollover opisuje, czy AD FS jest skonfigurowany do automatycznego odnawiania podpisywania tokenu i odszyfrowywania tokenów.  Jeśli AutoCertificateRollover ma wartość TRUE, postępuj zgodnie z instrukcjami przedstawionymi poniżej w temacie [generowanie nowego certyfikatu z podpisem własnym, jeśli AutoCertificateRollover jest ustawiona na TRUE].  Jeśli parametr AutoCertificateRollover ma wartość FALSE, postępuj zgodnie z instrukcjami przedstawionymi poniżej w temacie [generowanie nowych certyfikatów ręcznie, jeśli AutoCertificateRollover jest ustawiona na FALSE]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Generowanie nowego certyfikatu z podpisem własnym, jeśli AutoCertificateRollover jest ustawiona na wartość TRUE
W tej sekcji utworzysz **dwa** certyfikaty podpisywania tokenu.  Pierwsza z nich będzie używać flagi **-pilnej** , która spowoduje natychmiastowe zastąpienie bieżącego certyfikatu podstawowego.  Druga zostanie użyta dla certyfikatu pomocniczego.  

>[!IMPORTANT]
>Przyczyną tworzenia dwóch certyfikatów jest fakt, że platforma Azure zawiera informacje dotyczące poprzedniego certyfikatu.  Tworząc drugą, wymuszamy na platformie Azure wydanie informacji o starym certyfikacie i zamienimy ją na informacje o drugim certyfikacie.
>
>Jeśli drugi certyfikat nie zostanie utworzony i zaktualizujesz go na platformie Azure, może być możliwe, aby stary certyfikat podpisywania tokenu mógł uwierzytelniać użytkowników.

Aby wygenerować nowe certyfikaty podpisywania tokenu, można wykonać poniższe czynności.

 1. Upewnij się, że zalogowano się na głównym serwerze AD FS.
 2. Uruchom program Windows PowerShell jako administrator. 
 3. Upewnij się, że AutoCertificateRollover jest ustawiona na true (prawda).
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Aby wygenerować nowy token certyfikatu podpisywania: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Sprawdź aktualizację, uruchamiając następujące polecenie: `Get-ADFSCertificate –CertificateType token-signing`
 6. Teraz Wygeneruj drugi token certyfikat podpisywania: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Aby sprawdzić aktualizację, należy ponownie uruchomić następujące polecenie: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Ręczne generowanie nowych certyfikatów, jeśli AutoCertificateRollover ma wartość FAŁSZ
Jeśli nie korzystasz z domyślnego automatycznie generowanego certyfikatu tokenu podpisywania i odszyfrowywania tokenów, musisz odnowić te certyfikaty i skonfigurować je ręcznie.  Obejmuje to utworzenie dwóch nowych certyfikatów podpisywania tokenu i ich zaimportowanie.  Następnie podnieś poziom jednego do podstawowego, odwołaj stary certyfikat i skonfiguruj drugi certyfikat jako certyfikat pomocniczy.

Najpierw należy uzyskać dwa nowe certyfikaty z urzędu certyfikacji i zaimportować je do osobistego magazynu certyfikatów komputera lokalnego na każdym serwerze federacyjnym. Aby uzyskać instrukcje, zobacz artykuł [Importowanie certyfikatu](https://technet.microsoft.com/library/cc754489.aspx) .

>[!IMPORTANT]
>Przyczyną tworzenia dwóch certyfikatów jest fakt, że platforma Azure zawiera informacje dotyczące poprzedniego certyfikatu.  Tworząc drugą, wymuszamy na platformie Azure wydanie informacji o starym certyfikacie i zamienimy ją na informacje o drugim certyfikacie.
>
>Jeśli drugi certyfikat nie zostanie utworzony i zaktualizujesz go na platformie Azure, może być możliwe, aby stary certyfikat podpisywania tokenu mógł uwierzytelniać użytkowników.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Aby skonfigurować nowy certyfikat jako certyfikat pomocniczy
Następnie należy skonfigurować jeden certyfikat jako pomocniczy certyfikat podpisywania lub odszyfrowywania tokenu AD FS, a następnie podnieść go do podstawowej

1. Po zaimportowaniu certyfikatu. Otwórz konsolę **zarządzania AD FS** .
2. Rozwiń węzeł **Usługa** , a następnie wybierz pozycję **Certyfikaty**.
3. W okienku Akcje kliknij pozycję **Dodaj certyfikat Token-Signing**.
4. Wybierz nowy certyfikat z listy wyświetlanych certyfikatów, a następnie kliknij przycisk OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Aby podwyższyć poziom nowego certyfikatu od pomocniczego do podstawowego
Teraz, gdy nowy certyfikat został zaimportowany i skonfigurowany w AD FS, należy ustawić jako certyfikat podstawowy.
1. Otwórz konsolę **zarządzania AD FS** .
2. Rozwiń węzeł **Usługa** , a następnie wybierz pozycję **Certyfikaty**.
3. Kliknij token pomocniczy certyfikat podpisywania.
4. W okienku **Akcje** kliknij pozycję **Ustaw jako podstawową**. Kliknij przycisk tak w monicie o potwierdzenie.
5. Po podwyższeniu poziomu nowego certyfikatu jako certyfikatu podstawowego należy usunąć stary certyfikat, ponieważ nadal można go użyć. Zobacz sekcję [usuwanie starych certyfikatów](#remove-your-old-certificates) poniżej.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Aby skonfigurować drugi certyfikat jako certyfikat pomocniczy
Teraz, gdy został dodany pierwszy certyfikat i został on utworzony jako podstawowy i został usunięty stary, zaimportuj drugi certyfikat.  Następnie należy skonfigurować certyfikat jako pomocniczy token AD FS certyfikat podpisywania

1. Po zaimportowaniu certyfikatu. Otwórz konsolę **zarządzania AD FS** .
2. Rozwiń węzeł **Usługa** , a następnie wybierz pozycję **Certyfikaty**.
3. W okienku Akcje kliknij pozycję **Dodaj certyfikat Token-Signing**.
4. Wybierz nowy certyfikat z listy wyświetlanych certyfikatów, a następnie kliknij przycisk OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Aktualizowanie usługi Azure AD przy użyciu nowego certyfikatu podpisywania tokenu
Otwórz Moduł Microsoft Azure Active Directory dla Windows PowerShell. Alternatywnie Otwórz program Windows PowerShell, a następnie uruchom polecenie. `Import-Module msonline`

Aby nawiązać połączenie z usługą Azure AD, uruchom następujące polecenie: `Connect-MsolService` , a następnie wprowadź swoje poświadczenia administratora globalnego.

>[!Note]
> Jeśli te polecenia są uruchamiane na komputerze, który nie jest podstawowym serwerem federacyjnym, wprowadź najpierw następujące polecenie: `Set-MsolADFSContext –Computer <servername>` . Zamień na <servername> nazwę serwera AD FS. Po wyświetleniu monitu wprowadź poświadczenia administratora dla serwera AD FS.

Opcjonalnie Sprawdź, czy aktualizacja jest wymagana, sprawdzając bieżące informacje o certyfikacie w usłudze Azure AD. Aby to zrobić, uruchom następujące polecenie: `Get-MsolFederationProperty` . Po wyświetleniu monitu wprowadź nazwę domeny federacyjnej.

Aby zaktualizować informacje o certyfikacie w usłudze Azure AD, uruchom następujące polecenie: `Update-MsolFederatedDomain` a następnie wprowadź nazwę domeny po wyświetleniu monitu.

>[!Note]
> Jeśli podczas uruchamiania tego polecenia zostanie wyświetlony komunikat o błędzie, uruchom następujące polecenie: `Update-MsolFederatedDomain –SupportMultipleDomain` , a następnie wprowadź nazwę domeny po wyświetleniu monitu.

## <a name="replace-ssl-certificates"></a>Zastąp certyfikaty SSL
W przypadku konieczności zastąpienia certyfikatu podpisywania tokenu z powodu naruszenia bezpieczeństwa należy również odwołać i zastąpić certyfikaty SSL dla AD FS i serwerów WAP.  

Odwoływanie certyfikatów SSL należy przeprowadzić przy użyciu urzędu certyfikacji, który wystawił certyfikat.  Te certyfikaty często są wystawiane przez dostawców innych firm, takich jak GoDaddy.  Aby zapoznać się z przykładem, zobacz (odwoływanie certyfikatu | Certyfikaty SSL — GoDaddy help US).  Aby uzyskać więcej informacji, zobacz [jak działa Odwoływanie certyfikatów](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Po odwołaniu starego certyfikatu SSL i otrzymaniu nowego, można zastąpić certyfikaty SSL. Aby uzyskać więcej informacji [, zobacz zastępowanie certyfikatu SSL dla AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Usuń stare certyfikaty
Po zastąpieniu starych certyfikatów należy usunąć stary certyfikat, ponieważ nadal można go użyć. . W tym celu wykonaj poniższe kroki:.  W tym celu wykonaj poniższe kroki:

1. Upewnij się, że zalogowano się na głównym serwerze AD FS.
2. Uruchom program Windows PowerShell jako administrator. 
4. Aby usunąć stary certyfikat podpisywania certyfikatu: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Aktualizowanie partnerów federacyjnych, którzy mogą korzystać z metadanych Federacji
Jeśli odnowisz i skonfigurujesz nowy certyfikat podpisywania tokenu lub odszyfrowywania tokenów, musisz upewnić się, że wszyscy partnerzy Federacji (organizacja zasobów lub partnerzy organizacji kont, którzy są reprezentowani w AD FS według zaufania jednostki uzależnionej i zaufania dostawcy oświadczeń), wybiorę nowe certyfikaty.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Aktualizowanie partnerów federacyjnych, którzy nie mogą korzystać z metadanych Federacji
Jeśli partnerzy Federacji nie mogą korzystać z metadanych Federacji, należy ręcznie wysłać im klucz publiczny nowego certyfikatu podpisywania tokenu/tokenu. Wyślij nowy klucz publiczny certyfikatu (plik. cer lub. p7b, jeśli chcesz dołączyć cały łańcuch) do wszystkich organizacji zasobów lub partnerów organizacji kont (reprezentowanych w AD FS przez jednostki uzależnione zaufania i zaufania dostawcy oświadczeń). Czy partnerzy zaimplementują zmiany po stronie, aby ufać nowym certyfikatom.



## <a name="revoke-refresh-tokens-via-powershell"></a>Odwołaj tokeny odświeżania za pomocą programu PowerShell
Teraz chcemy odwołać tokeny odświeżania dla użytkowników, którzy je mogą, i wymusić ponowne zalogowanie się i uzyskać nowe tokeny.  Spowoduje to wylogowanie użytkowników z telefonu, bieżących sesji webmail oraz innych elementów, które używają tokenów i tokenów odświeżania.  Informacje można znaleźć [tutaj](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) i można także odwoływać się do [odwoływania dostępu użytkowników w Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie certyfikatami SSL w AD FS i WAP w systemie Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Uzyskaj i skonfiguruj certyfikaty podpisywania tokenu i odszyfrowywania tokenów dla AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Odnawianie certyfikatów Federacji dla Microsoft 365 i Azure Active Directory](how-to-connect-fed-o365-certs.md)



















