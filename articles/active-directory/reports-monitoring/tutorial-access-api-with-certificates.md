---
title: Samouczek dotyczący interfejsu API raportowania usługi AD z certyfikatami | Microsoft Docs
description: W tym samouczku wyjaśniono, jak używać interfejsu API raportowania usługi Azure AD przy użyciu poświadczeń certyfikatu w celu pobrania danych z katalogów bez interwencji użytkownika.
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
ms.openlocfilehash: bc763a99c945925b80171738f4076e6305d92df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89229463"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Samouczek: pobieranie danych przy użyciu interfejsu API raportowania Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](concept-reporting-api.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. Jeśli chcesz uzyskać dostęp do interfejsu API raportowania usługi Azure AD bez interwencji użytkownika, musisz skonfigurować dostęp do korzystania z certyfikatów.

W ramach tego samouczka nauczysz się używać certyfikatu testowego w celu uzyskania dostępu do usługi MS interfejs API programu Graph for Reporting. Nie zalecamy używania certyfikatów testowych w środowisku produkcyjnym. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Aby uzyskać dostęp do danych logowania, upewnij się, że masz dzierżawę Azure Active Directory z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach. 

2. Utwórz lub przejdź do konta użytkownika w roli **administrator globalny**, **administrator zabezpieczeń**, **czytelnik zabezpieczeń** lub **czytelnik raportu** dla dzierżawy. 

3. Wykonaj [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Pobierz i zainstaluj program [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Zainstaluj [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Biblioteki ADAL wymagające uwierzytelniania
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

6. Jeśli po raz pierwszy używasz modułu **Install-MSCloudIdUtilsModule**, w przeciwnym razie zaimportuj go za pomocą polecenia **Import-Module** programu PowerShell. Twoja sesja powinna wyglądać podobnie do tego ekranu: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Użyj polecenia **New-SelfSignedCertificate** programu PowerShell polecenia cmdlet, aby utworzyć certyfikat testowy.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Aby wyeksportować certyfikat do pliku certyfikatu, użyj polecenia cmdlet **Export-Certificate** .

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

1. Przejdź do [Azure Portal](https://portal.azure.com), wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji** i wybierz aplikację z listy. 

2. Wybierz pozycję **certyfikaty & wpisy tajne** w sekcji **Zarządzanie** w bloku Rejestracja aplikacji i wybierz pozycję **Przekaż certyfikat**.

3. Wybierz plik certyfikatu z poprzedniego kroku, a następnie wybierz pozycję **Dodaj**. 

4. Zanotuj identyfikator aplikacji i odcisk palca certyfikatu, który właśnie został zarejestrowany w aplikacji. Aby znaleźć odcisk palca, na stronie aplikacji w portalu przejdź do pozycji **certyfikaty & wpisy tajne** w sekcji **Zarządzanie** . Odcisk palca zostanie objęty listą **certyfikatów** .

5. Otwórz manifest aplikacji w edytorze manifestu wbudowane i sprawdź, czy właściwość *poświadczenia* są aktualizowane przy użyciu nowych informacji o certyfikacie, jak pokazano poniżej — 

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
6. Teraz możesz uzyskać token dostępu dla usługi MS interfejs API programu Graph przy użyciu tego certyfikatu. Użyj polecenia cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** w module MSCloudIdUtils PowerShell, przekazując identyfikator aplikacji i odcisk palca uzyskany w poprzednim kroku. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Użyj tokenu dostępu w skrypcie programu PowerShell, aby wykonać zapytanie dotyczące interfejs API programu Graph. Użyj polecenia cmdlet **Invoke-MSCloudIdMSGraphQuery** z MSCloudIDUtils, aby wyliczyć punkt końcowy Signins i directoryAudits. To polecenie cmdlet obsługuje wyniki wielostronicowe i wysyła te wyniki do potoku programu PowerShell.

8. Wykonaj zapytanie do punktu końcowego directoryAudits, aby pobrać dzienniki inspekcji. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Wykonaj zapytanie do punktu końcowego signins, aby pobrać dzienniki logowania.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Teraz możesz wyeksportować te dane do pliku CSV i zapisać je w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 

## <a name="next-steps"></a>Następne kroki

* [Pierwsze wrażenie dotyczące interfejsów API raportowania](concept-reporting-api.md)
* [Dokumentacja interfejsu API inspekcji](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Dokumentacja interfejsu API raportów działań związanych z logowaniem](/graph/api/resources/signin?view=graph-rest-beta)