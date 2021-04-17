---
title: Samouczek dotyczący interfejsu API raportowania usługi AD z certyfikatami | Microsoft Docs
description: W tym samouczku wyjaśniono, jak używać interfejsu API raportowania usługi Azure AD z poświadczeniami certyfikatu w celu uzyskania danych z katalogów bez interwencji użytkownika.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c4f4ac6ab3825c82ac0cb8ef5c31f9396ef7b41f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533727"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Samouczek: uzyskiwanie danych przy użyciu interfejsu API raportowania Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](concept-reporting-api.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. Jeśli chcesz uzyskać dostęp do interfejsu API raportowania usługi Azure AD bez interwencji użytkownika, musisz skonfigurować dostęp do używania certyfikatów.

Z tego samouczka dowiesz się, jak używać certyfikatu testowego w celu uzyskania dostępu do konta interfejs Graph API na użytek raportowania. Nie zalecamy używania certyfikatów testowych w środowisku produkcyjnym. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Aby uzyskać dostęp do danych logowania, upewnij się, że Azure Active Directory dzierżawy z licencją Premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach. 

2. Utwórz lub przełącz się na konto użytkownika w roli **administratora** globalnego, **administratora zabezpieczeń,** **czytelnika** **zabezpieczeń** lub czytelnika raportów dla dzierżawy. 

3. Wykonaj wymagania [wstępne dotyczące uzyskiwania dostępu do interfejsu API Azure Active Directory raportowania.](howto-configure-prerequisites-for-reporting-api.md) 

4. Pobierz i zainstaluj [program Azure AD PowerShell w wersji 2.](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)

5. Zainstaluj [pakiet MSCloudIdUtils.](https://www.powershellgallery.com/packages/MSCloudIdUtils/) Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Biblioteki ADAL potrzebne do uwierzytelniania
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

6. Jeśli używasz modułu po raz pierwszy, uruchom polecenie **Install-MSCloudIdUtilsModule,** a w przeciwnym razie zaimportuj go przy użyciu polecenia **Import-Module** programu PowerShell. Sesja powinna wyglądać podobnie do tego ekranu: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Użyj polecenia **polecenia polecenia New-SelfSignedCertificate** programu PowerShell, aby utworzyć certyfikat testowy.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Aby **wyeksportować certyfikat do** pliku certyfikatu, użyj polecenia polecenia Eksportuj certyfikat.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

1. Przejdź do [Azure Portal](https://portal.azure.com), wybierz pozycję **Azure Active Directory**, Rejestracje aplikacji **wybierz** pozycję i wybierz aplikację z listy. 

2. Wybierz **pozycję Certyfikaty & wpisów tajnych** w sekcji **Zarządzanie** w bloku Rejestracja aplikacji i wybierz pozycję **Przekaż certyfikat.**

3. Wybierz plik certyfikatu z poprzedniego kroku, a następnie wybierz pozycję **Dodaj**. 

4. Zanotuj identyfikator aplikacji i odcisk palca certyfikatu, który został właśnie zarejestrowany w aplikacji. Aby znaleźć odcisk palca, na stronie aplikacji w portalu przejdź do sekcji Certyfikaty i & **wpisów tajnych** w **sekcji** Zarządzanie. Odcisk palca będzie dostępny na **liście** Certyfikaty.

5. Otwórz manifest aplikacji w edytorze manifestu wbudowanego i sprawdź, czy właściwość *keyCredentials* została zaktualizowana przy użyciu nowych informacji o certyfikacie, jak pokazano poniżej — 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. Teraz możesz uzyskać token dostępu dla konta usługi interfejs Graph API użyciu tego certyfikatu. Użyj polecenia cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** z modułu MSCloudIdUtils programu PowerShell, przekazując identyfikator aplikacji i odcisk palca uzyskany w poprzednim kroku. 

   ![Zrzut ekranu przedstawia okno programu PowerShell z poleceniem, które tworzy token dostępu.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Użyj tokenu dostępu w skrypcie programu PowerShell, aby interfejs Graph API. Użyj polecenia cmdlet **Invoke-MSCloudIdMSGraphQuery** z modułu MSCloudIDUtils, aby wyliczyć punkty końcowe logowania i katalogAudits. To polecenie cmdlet obsługuje wyniki wielostronicowe i wysyła je do potoku programu PowerShell.

8. Odpytuj punkt końcowy directoryAudits, aby pobrać dzienniki inspekcji. 

   ![Zrzut ekranu przedstawiający okno programu PowerShell z poleceniem do wykonywania zapytań w punkcie końcowym directoryAudits przy użyciu tokenu dostępu z wcześniejszej wersji tej procedury.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Odpytują punkt końcowy logowania, aby pobrać dzienniki logowania.

    ![Zrzut ekranu przedstawiający okno programu PowerShell z poleceniem do wykonywania zapytań do punktu końcowego logowania przy użyciu tokenu dostępu z wcześniejszej wersji tej procedury.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Teraz możesz wyeksportować te dane do pliku CSV i zapisać je w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 

## <a name="next-steps"></a>Następne kroki

* [Pierwsze wrażenie dotyczące interfejsów API raportowania](concept-reporting-api.md)
* [Dokumentacja interfejsu API inspekcji](/graph/api/resources/directoryaudit) 
* [Dokumentacja interfejsu API raportu aktywności logowania](/graph/api/resources/signin)