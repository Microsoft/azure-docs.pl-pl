---
title: Microsoft Graph zestawu SDK programu PowerShell i Azure Active Directory Identity Protection
description: Dowiedz się, jak badać Microsoft Graph wykrywanie ryzyka i powiązane informacje z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880240"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection i zestaw SDK programu PowerShell dla Microsoft Graph

Microsoft Graph to punkt końcowy Microsoft Unified API i Strona główna [Azure Active Directory Identity Protection](./overview-identity-protection.md) interfejsów API. W tym artykule przedstawiono sposób użycia [zestawu SDK programu Microsoft Graph PowerShell](/graph/powershell/get-started) w celu uzyskania ryzykownych szczegółów użytkownika przy użyciu programu PowerShell. Organizacje, które chcą wykonywać zapytania dotyczące interfejsów API Microsoft Graph bezpośrednio, mogą korzystać z tego artykułu, [samouczka: Identyfikowanie i korygowanie zagrożeń przy użyciu interfejsów api Microsoft Graph](/graph/tutorial-riskdetection-api) do rozpoczęcia tej podróży.


## <a name="connect-to-microsoft-graph"></a>Połącz z Microsoft Graph

Aby uzyskać dostęp do danych ochrony tożsamości za poorednictwem Microsoft Graph, należy wykonać cztery kroki:

- [Utwórz certyfikat](#create-a-certificate)
- [Utwórz nową rejestrację aplikacji](#create-a-new-app-registration)
- [Konfigurowanie uprawnień interfejsu API](#configure-api-permissions)
- [Skonfiguruj prawidłowe poświadczenie](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Tworzenie certyfikatu

W środowisku produkcyjnym należy użyć certyfikatu z produkcyjnego urzędu certyfikacji, ale w tym przykładzie zostanie użyty certyfikat z podpisem własnym. Utwórz i wyeksportuj certyfikat przy użyciu następujących poleceń programu PowerShell.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Utwórz nową rejestrację aplikacji

1. W Azure Portal przejdź do **Azure Active Directory**  >  **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Na stronie **Tworzenie** wykonaj następujące czynności:
   1. W polu tekstowym **Nazwa** wpisz nazwę aplikacji (na przykład: interfejs API wykrywania ryzyka usługi Azure AD).
   1. W obszarze **obsługiwane typy kont** wybierz typ kont, które będą używać interfejsów API.
   1. Wybierz pozycję **Zarejestruj**.
1. Zanotuj identyfikatory **aplikacji (klienta)** i **Identyfikator katalogu (dzierżawy)** , ponieważ te elementy będą potrzebne później.

### <a name="configure-api-permissions"></a>Konfigurowanie uprawnień interfejsu API

W tym przykładzie skonfigurujemy uprawnienia aplikacji zezwalające na korzystanie z tego przykładu w trybie nienadzorowanym. W przypadku przyznawania uprawnień użytkownikowi, który zostanie zalogowany, wybierz opcję delegowane uprawnienia. Więcej informacji na temat różnych typów uprawnień można znaleźć w artykule, [uprawnienia i zgody na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md#permission-types).

1. Z utworzonej **aplikacji** wybierz pozycję **uprawnienia interfejsu API**.
1. Na stronie **skonfigurowane uprawnienia** na pasku narzędzi u góry kliknij pozycję **Dodaj uprawnienie**.
1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **Wybierz interfejs API**.
1. Na stronie **Wybierz interfejs API** wybierz opcję **Microsoft Graph**, a następnie kliknij przycisk **Wybierz**.
1. Na stronie **uprawnienia interfejsu API żądania** : 
   1. Wybierz pozycję **Uprawnienia aplikacji**.
   1. Zaznacz pola wyboru obok `IdentityRiskEvent.Read.All` i `IdentityRiskyUser.Read.All` .
   1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz pozycję **Udziel zgody administratora na domenę** 

### <a name="configure-a-valid-credential"></a>Skonfiguruj prawidłowe poświadczenie

1. Z utworzonej **aplikacji** wybierz pozycję **Certyfikaty & wpisy tajne**.
1. W obszarze **Certyfikaty** wybierz pozycję **Przekaż certyfikat**.
   1. Wybierz wcześniej wyeksportowany certyfikat z otwartego okna.
   1. Wybierz pozycję **Dodaj**.
1. Zanotuj **odcisk palca** certyfikatu, ponieważ będziesz potrzebować tych informacji w następnym kroku.

## <a name="list-risky-users-using-powershell"></a>Wyświetlanie listy ryzykownych użytkowników przy użyciu programu PowerShell

Aby umożliwić Microsoft Graph zapytań, należy zainstalować `Microsoft.Graph` moduł w naszym oknie programu PowerShell przy użyciu `Install-Module Microsoft.Graph` polecenia.

Zmodyfikuj następujące zmienne, aby uwzględnić informacje wygenerowane w poprzednich krokach, a następnie uruchom je jako całość, aby uzyskać ryzykowne szczegóły użytkownika przy użyciu programu PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do zestawu SDK programu Microsoft Graph PowerShell](/graph/powershell/get-started)
- [Samouczek: Identyfikowanie i korygowanie zagrożeń przy użyciu Microsoft Graph interfejsów API](/graph/tutorial-riskdetection-api)
- [Omówienie programu Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Uzyskaj dostęp bez użytkownika](/graph/auth-v2-service)
- [Katalog główny usługi Azure AD Identity Protection](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
