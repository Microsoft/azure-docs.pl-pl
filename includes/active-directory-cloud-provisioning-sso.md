---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559205"
---
## <a name="steps-to-enable-single-sign-on"></a>Procedura włączania logowania jednokrotnego
Inicjowanie obsługi administracyjnej w chmurze współpracuje z logowaniem jednokrotnym.  Obecnie nie jest dostępna opcja włączania logowania jednokrotnego w przypadku zainstalowania agenta. można jednak wykonać poniższe kroki, aby włączyć logowanie jednokrotne i użyć go. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Krok 1. Pobieranie i wyodrębnianie plików Azure AD Connect
1.  Najpierw pobierz najnowszą wersję programu [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Otwórz wiersz polecenia przy użyciu uprawnień administracyjnych i przejdź do pobranego pliku msi.
3.  Uruchom następujące czynności:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Zmień ścieżki FilePath i extractfolder, aby odpowiadały ścieżce pliku i nazwie folderu wyodrębniania.  Zawartość powinna teraz znajdować się w folderze wyodrębniania.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Krok 2. zaimportowanie bezproblemowego modułu programu PowerShell logowania jednokrotnego

1. Pobierz i zainstaluj program [Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowy moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 3. Pobieranie listy Active Directory lasów, w których włączono bezproblemowe logowanie jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Wywołanie `Get-AzureADSSOStatus` . To polecenie umożliwia wyświetlenie listy Active Directory lasów (Zobacz listę "domeny"), na której włączono tę funkcję.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4. Włączanie bezproblemowego logowania jednokrotnego dla każdego lasu Active Directory

1. Wywołanie `Enable-AzureADSSOForest` . Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu Active Directory.

   > [!NOTE]
   >Należy podać nazwę użytkownika poświadczeń administratora domeny w formacie nazwy konta SAM (contoso\johndoe lub contoso. com\johndoe). Używamy części nazwy użytkownika do lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Użyte konto administratora domeny nie może być członkiem grupy Chronieni użytkownicy. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Powtórz poprzedni krok dla każdego lasu Active Directory, w którym chcesz skonfigurować funkcję.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włączanie funkcji w dzierżawie

Aby włączyć funkcję w dzierżawie, wywołaj polecenie `Enable-AzureADSSO -Enable $true` .