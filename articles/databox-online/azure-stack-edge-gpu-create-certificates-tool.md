---
title: Tworzenie certyfikatów przy użyciu narzędzia sprawdzania gotowości centrum Microsoft Azure Stack | Microsoft Docs
description: Opisuje sposób tworzenia żądań certyfikatów, a następnie pobierania i instalowania certyfikatów na urządzeniu z systemem Azure Stack EDGE Pro GPU przy użyciu narzędzia sprawdzania gotowości centrum Azure Stack.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: 2dc211b7564b08e9aa36b1e394a66a4c175922e2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549807"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Utwórz certyfikaty dla Azure Stack EDGE Pro przy użyciu narzędzia do sprawdzania gotowości centrum Azure Stack 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób tworzenia certyfikatów dla Azure Stack EDGE Pro przy użyciu narzędzia sprawdzania gotowości centrum Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Korzystanie z narzędzia sprawdzania gotowości centrum Azure Stack

Użyj narzędzia sprawdzania gotowości centrum Azure Stack, aby utworzyć żądania podpisania certyfikatu (przedstawiciele klienta) na potrzeby wdrożenia urządzenia Azure Stack EDGE Pro. Te żądania można utworzyć po umieszczeniu zamówienia na urządzenie Azure Stack EDGE Pro i poczekać na jego dostarczenie.

> [!NOTE]
> To narzędzie służy tylko do celów testowych i programistycznych, a nie dla urządzeń produkcyjnych. 

Aby zażądać następujących certyfikatów, można użyć narzędzia sprawdzania gotowości centrum Azure Stack (AzsReadinessChecker):

- Certyfikat Azure Resource Manager
- Certyfikat lokalnego interfejsu użytkownika
- Certyfikat węzła
- Certyfikat obiektu BLOB
- Certyfikat sieci VPN


## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć obsługę klienta na potrzeby wdrożenia urządzeń z systemem Azure Stack EDGE Pro, upewnij się, że: 

- Jesteś klientem z systemem Windows 10 lub Windows Server 2016 lub nowszym. 
- Pobrano narzędzie do sprawdzania gotowości centrum Microsoft Azure Stack [z Galeria programu PowerShell](https://aka.ms/AzsReadinessChecker) w tym systemie.
- Istnieją następujące informacje dotyczące certyfikatów:
  - Nazwa urządzenia
  - Numer seryjny węzła
  - Zewnętrzna w pełni kwalifikowana nazwa domeny (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generuj żądania podpisania certyfikatu

Wykonaj następujące kroki, aby przygotować certyfikaty urządzeń Azure Stack EDGE Pro:

1. Uruchom program PowerShell jako administrator (5,1 lub nowszy).
2. Zainstaluj narzędzie do sprawdzania gotowości centrum Azure Stack. W wierszu polecenia programu PowerShell wpisz: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Aby uzyskać zainstalowaną wersję, wpisz:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Utwórz katalog dla wszystkich certyfikatów, jeśli jeszcze go nie masz. Wpisz: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Aby utworzyć żądanie certyfikatu, podaj następujące informacje. W przypadku generowania certyfikatu sieci VPN niektóre z tych danych wejściowych nie są stosowane.
    
    |Dane wejściowe |Opis  |
    |---------|---------|
    |`OutputRequestPath`|Ścieżka pliku na kliencie lokalnym, w której mają zostać utworzone żądania certyfikatu.        |
    |`DeviceName`|Nazwa urządzenia na stronie **urządzenia** w lokalnym interfejsie użytkownika sieci Web urządzenia. <br> To pole nie jest wymagane w przypadku certyfikatu sieci VPN.         |
    |`NodeSerialNumber`|Numer seryjny węzła urządzenia na stronie **sieciowej** w lokalnym interfejsie użytkownika sieci Web urządzenia. <br> To pole nie jest wymagane w przypadku certyfikatu sieci VPN.       |
    |`ExternalFQDN`|Wartość DNSDomain na stronie **urządzenia** w lokalnym interfejsie użytkownika sieci Web urządzenia.         |
    |`RequestType`|Typem żądania mogą być `MultipleCSR` różne certyfikaty dla różnych punktów końcowych lub `SingleCSR` pojedynczy certyfikat dla wszystkich punktów końcowych. <br> To pole nie jest wymagane w przypadku certyfikatu sieci VPN.     |

    Dla wszystkich certyfikatów, z wyjątkiem certyfikatu sieci VPN, wpisz: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Jeśli tworzysz certyfikat sieci VPN, wpisz: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Pliki żądania certyfikatu znajdują się w katalogu określonym w powyższym parametrze OutputRequestPath. Przy użyciu parametru zobaczysz `MultipleCSR` następujące cztery pliki z `.req` rozszerzeniem:

    
    |Nazwy plików  |Typ żądania certyfikatu  |
    |---------|---------|
    |Począwszy od `DeviceName`     |Żądanie certyfikatu lokalnego interfejsu użytkownika sieci Web      |
    |Począwszy od `NodeSerialNumber`     |Żądanie certyfikatu węzła         |
    |Począwszy od `login`     |Azure Resource Manager żądanie certyfikatu punktu końcowego       |
    |Począwszy od `wildcard`     |Żądanie certyfikatu magazynu obiektów BLOB. Zawiera symbol wieloznaczny, ponieważ obejmuje wszystkie konta magazynu, które można utworzyć na urządzeniu.          |
    |Począwszy od `AzureStackEdgeVPNCertificate`     |Żądanie certyfikatu klienta sieci VPN.         |

    Zobaczysz również folder INF. Zawiera on element Management. <Edge-DeviceName> pliku informacji w postaci zwykłego tekstu opisującego szczegóły certyfikatu.  


6. Prześlij te pliki do urzędu certyfikacji (wewnętrzne lub publiczne). Upewnij się, że urząd certyfikacji generuje certyfikaty, przy użyciu wygenerowanego żądania, które spełnia wymagania certyfikatu Azure Stack EDGE Pro dla [certyfikatów węzłów](azure-stack-edge-gpu-manage-certificates.md#node-certificates), [certyfikatów punktów końcowych](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)i [lokalnych certyfikatów interfejsu użytkownika](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Przygotowywanie certyfikatów do wdrożenia

Pliki certyfikatów, które otrzymujesz od urzędu certyfikacji, muszą zostać zaimportowane i wyeksportowane z właściwościami zgodnymi z wymaganiami dotyczącymi certyfikatów na urządzeniu Azure Stack brzeg Pro. Wykonaj następujące kroki w tym samym systemie, w którym wygenerowałeś żądania podpisania certyfikatu.

- Aby zaimportować certyfikaty, postępuj zgodnie z instrukcjami w temacie [Importowanie certyfikatów na klientach uzyskujących dostęp do urządzenia z usługą Azure Stack EDGE Pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Aby wyeksportować certyfikaty, postępuj zgodnie z instrukcjami w [sekcji eksportowanie certyfikatów z klienta uzyskującego dostęp do urządzenia Azure Stack EDGE Pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Weryfikuj certyfikaty

Najpierw wygenerujesz odpowiednią strukturę folderów i umieścisz certyfikaty w odpowiednich folderach. Tylko wtedy będziesz sprawdzać poprawność certyfikatów przy użyciu narzędzia.

1. Uruchom program PowerShell jako administrator.

2. Aby wygenerować odpowiednią strukturę folderów, wpisz w wierszu polecenia:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Przekonwertuj hasło PFX na bezpieczny ciąg. Wpisz:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Następnie sprawdź poprawność certyfikatów. Wpisz:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia z Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md)
