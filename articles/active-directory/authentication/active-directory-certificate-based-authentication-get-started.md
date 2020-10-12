---
title: Uwierzytelnianie oparte na certyfikatach — Azure Active Directory
description: Dowiedz się, jak skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 94955e27dcadb3acbea03926d6d1ed73e9c5c9ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87051348"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelnienie za pomocą Azure Active Directory z certyfikatem klienta na urządzeniu z systemem Windows, Android lub iOS podczas łączenia konta usługi Exchange Online z:

- Aplikacje mobilne firmy Microsoft, takie jak Microsoft Outlook i Microsoft Word
- Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzenia kombinacji nazwy użytkownika i hasła do określonych aplikacji poczty i Microsoft Office na urządzeniu przenośnym.

Ten temat:

- Zawiera instrukcje dotyczące konfigurowania i używania uwierzytelniania opartego na certyfikatach dla użytkowników dzierżaw w planach Office 365 Enterprise, Business, Education i USA. Ta funkcja jest dostępna w wersji zapoznawczej w pakiecie Office 365 Chiny, obrony rządowe USA i plany federalne dla instytucji rządowych USA.
- Przyjęto założenie, że masz już skonfigurowaną [infrastrukturę kluczy publicznych (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) i [AD FS](../hybrid/how-to-connect-fed-whatis.md) .

## <a name="requirements"></a>Wymagania

Aby skonfigurować uwierzytelnianie oparte na certyfikatach, należy wykonać następujące instrukcje:

- Uwierzytelnianie oparte na certyfikatach (OBSŁUGIWANIE) jest obsługiwane tylko dla środowisk federacyjnych dla aplikacji przeglądarki, natywnych klientów korzystających z nowoczesnego uwierzytelniania (ADAL) lub bibliotek MSAL. Jedynym wyjątkiem jest Exchange Active Sync (EAS) dla usługi Exchange Online (EXO), który może być używany w przypadku kont federacyjnych i zarządzanych.
- Główny urząd certyfikacji i wszystkie pośrednie urzędy certyfikacji muszą być skonfigurowane w Azure Active Directory.
- Każdy urząd certyfikacji musi mieć listę odwołania certyfikatów (CRL), do której można odwoływać się za pośrednictwem internetowego adresu URL.
- Musisz mieć co najmniej jeden skonfigurowany urząd certyfikacji w Azure Active Directory. Powiązane kroki można znaleźć w sekcji [Konfigurowanie urzędów certyfikacji](#step-2-configure-the-certificate-authorities) .
- W przypadku klientów programu Exchange ActiveSync certyfikat klienta musi mieć adres e-mail z obsługą routingu użytkownika w usłudze Exchange Online w nazwie głównej lub RFC822 wartość pola Alternatywna nazwa podmiotu. Azure Active Directory mapuje wartość RFC822 na atrybut adresu serwera proxy w katalogu.
- Urządzenie klienckie musi mieć dostęp do co najmniej jednego urzędu certyfikacji, który wystawia certyfikaty klienta.
- Certyfikat klienta na potrzeby uwierzytelniania klienta musi zostać wystawiony dla klienta.

>[!IMPORTANT]
>Maksymalny rozmiar listy CRL dla Azure Active Directory do pomyślnego pobrania i pamięci podręcznej to baza, a czas wymagany do pobrania listy CRL nie może przekroczyć 10 sekund.  Jeśli Azure Active Directory nie może pobrać listy CRL, uwierzytelnianie oparte na certyfikatach używające certyfikatów wystawionych przez odpowiedni urząd certyfikacji zakończy się niepowodzeniem. Najlepsze rozwiązania mające na celu zagwarantowanie, że w ramach ograniczeń rozmiaru pliki list CRL mają na celu zachowanie okresów ważności certyfikatów w ramach rozsądnych limitów i oczyszczenie wygasłych certyfikatów.

## <a name="step-1-select-your-device-platform"></a>Krok 1. Wybieranie platformy urządzenia

Najpierw należy zapoznać się z następującymi krokami, aby poznać platformę urządzenia:

- Obsługa aplikacji mobilnych pakietu Office
- Określone wymagania dotyczące implementacji

Powiązane informacje istnieją dla następujących platform urządzeń:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2. Konfigurowanie urzędów certyfikacji

Aby skonfigurować urzędy certyfikacji w Azure Active Directory, dla każdego urzędu certyfikacji należy przekazać następujące elementy:

* Publiczna część certyfikatu w formacie *CER*
* Adresy URL dostępne z Internetu, w których znajdują się listy odwołania certyfikatów (CRL)

Schemat urzędu certyfikacji wygląda następująco:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

Konfiguracja programu umożliwia korzystanie z programu [Azure Active Directory PowerShell w wersji 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0):

1. Uruchom program Windows PowerShell z uprawnieniami administratora.
2. Zainstaluj moduł usługi Azure AD w wersji [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) lub nowszej.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Jako pierwszy krok konfiguracji należy nawiązać połączenie z dzierżawcą. Gdy tylko połączenie z dzierżawcą istnieje, możesz przejrzeć, dodać, usunąć i zmodyfikować zaufane urzędy certyfikacji, które są zdefiniowane w Twoim katalogu.

### <a name="connect"></a>Connect

Aby nawiązać połączenie z dzierżawcą, użyj polecenia cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) :

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Odczytać

Aby pobrać zaufane urzędy certyfikacji zdefiniowane w katalogu, należy użyć polecenia cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) .

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Dodaj

Aby utworzyć zaufany urząd certyfikacji, użyj polecenia cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) i ustaw poprawną wartość atrybutu **crlDistributionPoint** :

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Usuń

Aby usunąć zaufany urząd certyfikacji, użyj polecenia cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Modyfikowanie

Aby zmodyfikować zaufany urząd certyfikacji, użyj polecenia cmdlet [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Krok 3. Konfigurowanie odwołania

Aby odwołać certyfikat klienta, program Azure Active Directory pobiera listę odwołania certyfikatów (CRL) z adresów URL przekazanych w ramach informacji o urzędzie certyfikacji i buforuje ją. Ostatnia sygnatura czasowa publikacji (Właściwość**Data wprowadzenia** ) w liście CRL jest używana, aby upewnić się, że lista CRL jest nadal ważna. Lista CRL jest okresowo przywoływana, aby odwołać dostęp do certyfikatów, które są częścią listy.

Jeśli jest wymagane bardziej natychmiastowe odwołanie (na przykład jeśli użytkownik utraci urządzenie), token autoryzacji użytkownika może być unieważniony. Aby unieważnić Token autoryzacji, należy ustawić pole **StsRefreshTokenValidFrom** dla tego określonego użytkownika przy użyciu programu Windows PowerShell. Należy zaktualizować pole **StsRefreshTokenValidFrom** dla każdego użytkownika, dla którego ma zostać odwołany dostęp.

Aby upewnić się, że odwołanie będzie nadal występować, należy ustawić datę **obowiązywania** listy CRL na datę późniejszą od wartości ustawionej przez **StsRefreshTokenValidFrom** i upewnić się, że dany certyfikat znajduje się na liście CRL.

Poniższe kroki przedstawiają proces aktualizowania i unieważniania tokenu autoryzacji przez ustawienie pola **StsRefreshTokenValidFrom** .

**Aby skonfigurować odwołanie:**

1. Połącz się z poświadczeniami administratora usługi MSOL:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Pobierz bieżącą wartość StsRefreshTokensValidFrom dla użytkownika:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Skonfiguruj nową wartość StsRefreshTokensValidFrom dla użytkownika równą bieżącej sygnatury czasowej:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

Ustawiona data musi przypadać w przyszłości. Jeśli Data nie przypada w przyszłości, właściwość **StsRefreshTokensValidFrom** nie jest ustawiona. Jeśli data przypada w przyszłości, **StsRefreshTokensValidFrom** jest ustawiona na bieżącą godzinę (nie datę wskazywaną przez Set-MsolUser polecenie).

## <a name="step-4-test-your-configuration"></a>Krok 4. Testowanie konfiguracji

### <a name="testing-your-certificate"></a>Testowanie certyfikatu

Jako pierwszy test konfiguracji należy podjąć próbę zalogowania się do usługi [Outlook dostęp w sieci Web](https://outlook.office365.com) lub [SharePoint Online](https://microsoft.sharepoint.com) przy użyciu **przeglądarki na urządzeniu**.

Jeśli logowanie powiedzie się, wiadomo, że:

- Certyfikat użytkownika został zainicjowany na urządzeniu testowym
- AD FS jest prawidłowo skonfigurowany

### <a name="testing-office-mobile-applications"></a>Testowanie aplikacji mobilnych pakietu Office

**Aby przetestować uwierzytelnianie oparte na certyfikacie w aplikacji pakietu Office dla urządzeń przenośnych:**

1. Na urządzeniu testowym Zainstaluj aplikację mobilną pakietu Office (na przykład OneDrive).
3. Uruchom aplikację.
4. Wprowadź nazwę użytkownika, a następnie wybierz certyfikat użytkownika, którego chcesz użyć.

Należy pomyślnie zalogować się.

### <a name="testing-exchange-activesync-client-applications"></a>Testowanie aplikacji klienckich programu Exchange ActiveSync

Aby uzyskać dostęp do programu Exchange ActiveSync (EAS) za pośrednictwem uwierzytelniania opartego na certyfikatach, dla aplikacji musi być dostępny profil EAS zawierający certyfikat klienta.

Profil EAS musi zawierać następujące informacje:

- Certyfikat użytkownika, który ma być używany do uwierzytelniania

- Punkt końcowy EAS (na przykład outlook.office365.com)

Profil EAS można skonfigurować i umieścić na urządzeniu za pomocą zarządzania urządzeniami przenośnymi (MDM), takiego jak usługa Intune, lub ręcznie umieszczając certyfikat w profilu EAS na urządzeniu.

### <a name="testing-eas-client-applications-on-android"></a>Testowanie aplikacji klienckich EAS w systemie Android

**Aby przetestować uwierzytelnianie certyfikatu:**

1. Skonfiguruj w aplikacji profil EAS, który spełnia wymagania zawarte w poprzedniej sekcji.
2. Otwórz aplikację i sprawdź, czy poczta jest synchronizowana.

## <a name="next-steps"></a>Następne kroki

[Dodatkowe informacje na temat uwierzytelniania opartego na certyfikatach na urządzeniach z systemem Android.](active-directory-certificate-based-authentication-android.md)

[Dodatkowe informacje na temat uwierzytelniania opartego na certyfikatach na urządzeniach z systemem iOS.](active-directory-certificate-based-authentication-ios.md)
