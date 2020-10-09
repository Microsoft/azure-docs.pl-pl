---
title: Samouczek zbiorczy zapraszania użytkowników współpracy B2B — Azure Active Directory | Microsoft Docs
description: W ramach tego samouczka nauczysz się używać programu PowerShell i pliku CSV do wysyłania zaproszeń zbiorczych do użytkowników-Gości współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e24d9ff76184c36aee5c14f15f9713b30f6f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905956"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Samouczek: używanie programu PowerShell do zbiorczego zapraszania użytkowników współpracy B2B usługi Azure AD

Jeśli używasz funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD) do pracy z partnerami zewnętrznymi, możesz równocześnie zapraszać wielu użytkowników-gości do swojej organizacji. Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do użytkowników zewnętrznych za pomocą programu PowerShell. W szczególności należy wykonać następujące czynności:

> [!div class="checklist"]
> * Przygotowanie pliku wartości rozdzielanych przecinkami (CSV) z informacjami o użytkownikach
> * Uruchomienie skryptu programu PowerShell w celu wysłania zaproszeń
> * Sprawdzenie, czy użytkownicy zostali dodani do katalogu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-the-latest-azureadpreview-module"></a>Zainstalowanie najnowszego modułu AzureADPreview

Upewnij się, że masz zainstalowaną najnowszą wersję modułu Azure AD PowerShell dla programu Graph (AzureADPreview). 

Najpierw sprawdź, które moduły zostały zainstalowane. Otwórz program PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchom następujące polecenie:

```powershell
Get-Module -ListAvailable AzureAD*
```

W zależności od rezultatu wykonaj jedną z następujących czynności:

- Jeśli żadne wyniki nie zostaną zwrócone, uruchom następujące polecenie, aby zainstalować moduł AzureADPreview:
  
   ```powershell
   Install-Module AzureADPreview
   ```

- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell
   Uninstall-Module AzureAD
   Install-Module AzureADPreview
   ```

- Jeśli w wynikach jest wyświetlany tylko moduł AzureADPreview, ale pojawia się komunikat informujący o tym, że istnieje nowsza wersja, uruchom następujące polecenia, aby zaktualizować moduł:

   ```powershell
   Uninstall-Module AzureADPreview
   Install-Module AzureADPreview
   ```

Może zostać wyświetlony monit z informacją, że instalujesz moduł z niezaufanego repozytorium. Dzieje się tak w sytuacji, gdy repozytorium PSGallery nie zostało ustawione wcześniej jako zaufane. Naciśnij klawisz **Y**, aby zainstalować moduł.

### <a name="get-test-email-accounts"></a>Uzyskiwanie testowych kont e-mail

Potrzebujesz co najmniej dwóch testowych kont e-mail, na które będzie można wysłać zaproszenia. Konta muszą być spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym kont społecznościowych z adresem w domenie gmail.com lub outlook.com.

## <a name="prepare-the-csv-file"></a>Przygotowywanie pliku CSV

W programie Microsoft Excel utwórz plik CSV zawierający listę nazw użytkownika i adresów e-mail zapraszanych osób. Upewnij się, że nagłówki kolumn **Name** i **InvitedUserEmailAddress** zostały uwzględnione.

Na przykład utwórz arkusz w następującym formacie:

![Rezultat w programie PowerShell przedstawiający oczekiwanie na zaakceptowanie przez użytkownika](media/tutorial-bulk-invite/AddUsersExcel.png)

Zapisz plik jako **C:\BulkInvite\Invitations.csv**. 

Jeśli nie masz programu Excel, możesz utworzyć plik CSV w dowolnym edytorze tekstów, takim jak Notatnik. Oddziel poszczególne wartości przecinkami, a poszczególne wiersze znakami nowego wiersza. 

## <a name="sign-in-to-your-tenant"></a>Logowanie się do dzierżawy

Uruchom następujące polecenie, aby połączyć się z domeną dzierżawy:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Na przykład `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po wyświetleniu monitu wprowadź poświadczenia.

## <a name="send-bulk-invitations"></a>Zbiorcze wysyłanie zaproszeń

Aby wysłać zaproszenia, uruchom następujący skrypt programu PowerShell (gdzie **c:\bulkinvite\invitations.csv** to ścieżka do pliku CSV):

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

Skrypt wysyła zaproszenie na adresy e-mail podane w pliku Invitations.csv. Dla każdego użytkownika powinny zostać wyświetlone dane wyjściowe podobne do następujących:

![Rezultat w programie PowerShell przedstawiający oczekiwanie na zaakceptowanie przez użytkownika](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Sprawdzanie, czy użytkownicy istnieją w katalogu

Aby sprawdzić, czy zaproszeni użytkownicy zostali dodani do usługi Azure AD, uruchom następujące polecenie:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Zobaczysz użytkowników, którzy zaprosił, z główną nazwą użytkownika (UPN) w formacie *emailaddress*#EXT # \@ *Domain*. Na przykład *lstokes_fabrikam. com # EXT # \@ contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacją, z której wysłano zaproszenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Testowe konta użytkowników możesz usunąć z katalogu, gdy nie będą już potrzebne. Uruchom następujące polecenie, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Na przykład: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano zaproszenia zbiorcze do użytkowników-gości spoza organizacji. Następnie dowiesz się, jak działa proces realizacji zaproszenia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat procesu realizacji zaproszenia do współpracy B2B w usłudze Azure AD](redemption-experience.md)
