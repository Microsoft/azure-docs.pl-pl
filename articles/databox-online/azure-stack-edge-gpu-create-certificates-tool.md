---
title: Tworzenie certyfikatów przy użyciu Microsoft Azure sprawdzania gotowości usługi Stack Hub | Microsoft Docs
description: Opisuje sposób tworzenia żądań certyfikatów, a następnie uzyskiwanie i instalowanie certyfikatów na urządzeniu Azure Stack Edge Pro GPU przy użyciu Azure Stack Hub sprawdzania gotowości.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389336"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Tworzenie certyfikatów dla serwera Azure Stack Edge Pro za pomocą Azure Stack Hub sprawdzania gotowości 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób tworzenia certyfikatów dla Azure Stack Edge Pro przy użyciu Azure Stack Hub sprawdzania gotowości. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Korzystanie Azure Stack Hub sprawdzania gotowości

Użyj narzędzia Azure Stack Hub sprawdzania gotowości, aby utworzyć żądania podpisania certyfikatu (CPR) na Azure Stack Edge Pro wdrożenia urządzenia. Możesz utworzyć te żądania po zleceniu zakupu urządzenia Azure Stack Edge Pro i zaczekaj na jego przybycie.

> [!NOTE]
> Tego narzędzia należy używać tylko do celów testowych lub programistów, a nie dla urządzeń produkcyjnych. 

Możesz użyć narzędzia Azure Stack Hub sprawdzania gotowości (AzsReadinessChecker), aby zażądać następujących certyfikatów:

- Azure Resource Manager certyfikatu
- Certyfikat lokalnego interfejsu użytkownika
- Certyfikat węzła
- Certyfikat obiektu blob
- Certyfikat sieci VPN


## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć SRS na Azure Stack Edge Pro wdrożenia urządzenia, upewnij się, że: 

- Masz klienta z systemem Windows 10 lub Windows Server 2016 lub nowszym. 
- Narzędzie sprawdzania gotowości usługi Microsoft Azure Stack Hub została pobrana [z](https://aka.ms/AzsReadinessChecker) Galeria programu PowerShell w tym systemie.
- Masz następujące informacje dotyczące certyfikatów:
  - Nazwa urządzenia
  - Numer seryjny węzła
  - W pełni kwalifikowana zewnętrzna nazwa domeny (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generowanie żądań podpisania certyfikatu

Aby przygotować certyfikaty urządzeń Azure Stack Edge Pro, należy wykonać następujące czynności:

1. Uruchom program PowerShell jako administrator (5.1 lub nowszy).
2. Zainstaluj narzędzie Azure Stack Hub sprawdzania gotowości. W wierszu polecenia programu PowerShell wpisz: 

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
    
4. Aby utworzyć żądanie certyfikatu, podaj następujące informacje. W przypadku generowania certyfikatu sieci VPN niektóre z tych danych wejściowych nie mają zastosowania.
    
    |Dane wejściowe |Opis  |
    |---------|---------|
    |`OutputRequestPath`|Ścieżka pliku na kliencie lokalnym, w której mają zostać utworzone żądania certyfikatów.        |
    |`DeviceName`|Nazwa urządzenia na stronie  Urządzenia w lokalnym internetowym interfejsie użytkownika urządzenia. <br> To pole nie jest wymagane dla certyfikatu sieci VPN.         |
    |`NodeSerialNumber`|Numer seryjny węzła urządzenia na stronie **Sieć** w lokalnym internetowym interfejsie użytkownika urządzenia. <br> To pole nie jest wymagane dla certyfikatu sieci VPN.       |
    |`ExternalFQDN`|Wartość DNSDomain na stronie **Urządzenia** w lokalnym internetowym interfejsie użytkownika urządzenia.         |
    |`RequestType`|Typ żądania może być dla — różne certyfikaty dla różnych punktów końcowych `MultipleCSR` lub — pojedynczy certyfikat dla wszystkich punktów `SingleCSR` końcowych. <br> To pole nie jest wymagane dla certyfikatu sieci VPN.     |

    Dla wszystkich certyfikatów z wyjątkiem certyfikatu sieci VPN wpisz: 
    
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

    
5. Pliki żądania certyfikatu znajdziesz w katalogu określonym powyżej w parametrze OutputRequestPath. Podczas korzystania `MultipleCSR` z parametru zobaczysz następujące cztery pliki z `.req` rozszerzeniem :

    
    |Nazwy plików  |Typ żądania certyfikatu  |
    |---------|---------|
    |Począwszy od `DeviceName`     |Żądanie certyfikatu lokalnego internetowego interfejsu użytkownika      |
    |Począwszy od `NodeSerialNumber`     |Żądanie certyfikatu węzła         |
    |Począwszy od `login`     |Azure Resource Manager certyfikatu punktu końcowego       |
    |Począwszy od `wildcard`     |Żądanie certyfikatu magazynu obiektów blob. Zawiera symbol wieloznaczny, ponieważ obejmuje wszystkie konta magazynu, które można utworzyć na urządzeniu.          |
    |Począwszy od `AzureStackEdgeVPNCertificate`     |Żądanie certyfikatu klienta sieci VPN.         |

    Zostanie również wyświetlony folder INF. Zawiera on plik management.<edge-devicename> z informacjami w czytelnym tekście objaśniający szczegóły certyfikatu.  


6. Prześlij te pliki do urzędu certyfikacji (wewnętrznego lub publicznego). Upewnij się, że urząd certyfikacji generuje certyfikaty, korzystając z wygenerowanego żądania, które spełniają [](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)wymagania dotyczące certyfikatów Azure Stack Edge Pro dla certyfikatów węzłów, [](azure-stack-edge-gpu-manage-certificates.md#node-certificates)certyfikatów punktu końcowego i certyfikatów lokalnego [interfejsu użytkownika.](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Przygotowywanie certyfikatów do wdrożenia

Pliki certyfikatów, które można pobrać z urzędu certyfikacji (CA), należy zaimportować i wyeksportować z właściwościami, które spełniają wymagania certyfikatu urządzenia Azure Stack Edge Pro certyfikatu. Wykonaj następujące kroki w tym samym systemie, w którym wygenerowano żądania podpisania certyfikatu.

- Aby zaimportować certyfikaty, wykonaj kroki opisane w te [tematu Importowanie](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)certyfikatów na klientach, którzy mają dostęp do Azure Stack Edge Pro urządzenia.

- Aby wyeksportować certyfikaty, wykonaj kroki opisane w te [tematu Eksportowanie](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)certyfikatów od klienta mającego dostęp do Azure Stack Edge Pro urządzenia .


## <a name="validate-certificates"></a>Weryfikowanie certyfikatów

Najpierw wygenerujesz odpowiednią strukturę folderów i umieść certyfikaty w odpowiednich folderach. Dopiero wtedy zweryfikujesz certyfikaty przy użyciu narzędzia.

1. Uruchom program PowerShell jako administrator.

2. Aby wygenerować odpowiednią strukturę folderów, w wierszu polecenia wpisz:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Przekonwertuj hasło PFX na bezpieczny ciąg. Wpisz:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Następnie zweryfikuj certyfikaty. Wpisz:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Następne kroki

[Wdrażanie Azure Stack Edge Pro urządzenia](azure-stack-edge-gpu-deploy-prep.md)
