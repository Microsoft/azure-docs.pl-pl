---
title: Ustawianie Azure Resource Manager hasła na urządzeniu z systemem Azure Stack EDGE Pro GPU
description: Opisuje Ustawianie hasła Azure Resource Manager na procesorze GPU Azure Stack EDGE Pro przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 915146cd17b90272daea4ce57f5243baf1d49cb3
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578794"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Ustawianie hasła Azure Resource Manager na urządzeniu z systemem Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób ustawiania hasła Azure Resource Manager. Należy ustawić to hasło podczas łączenia z lokalnymi interfejsami API urządzenia za pośrednictwem Azure Resource Manager.

Procedura ustawiania hasła może się różnić w zależności od tego, czy używasz witryny Azure Portal, czy poleceń cmdlet programu PowerShell. Każda z tych procedur została opisana w poniższych sekcjach.


## <a name="reset-password-via-the-azure-portal"></a>Zresetuj hasło za pomocą Azure Portal

1. W Azure Portal przejdź do zasobu Azure Stack Edge utworzonego w celu zarządzania urządzeniem. Przejdź do pozycji **obliczenia graniczne > wprowadzenie**.

2. W okienku po prawej stronie na pasku poleceń wybierz pozycję **Resetuj hasło ARM usługi Edge**. 

    ![Resetowanie hasła użytkownika EdgeARM 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. W bloku **Resetowanie hasła użytkownika EdgeArm** Podaj hasło, aby połączyć się z lokalnymi interfejsami API urządzenia za pośrednictwem Azure Resource Manager. Potwierdź hasło i wybierz pozycję **Zresetuj**.

    ![Resetowanie hasła użytkownika EdgeARM 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Zresetuj hasło za pomocą programu PowerShell

1. W witrynie Azure Portal przejdź do zasobu Azure Stack Edge utworzonego w celu zarządzania urządzeniem. Zanotuj następujące parametry na stronie **Przegląd** .

    - Nazwa zasobu Azure Stack Edge
    - Identyfikator subskrypcji

2. Przejdź do pozycji **ustawienia > właściwości**. Zanotuj następujące parametry na stronie **Właściwości** .

    - Grupa zasobów
    - Klucz szyfrowania CIK: wybierz pozycję Widok, a następnie skopiuj **klucz szyfrowania**.

    ![Pobierz klucz szyfrowania CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Określ hasło, które będzie używane do nawiązywania połączenia z usługą Azure Resource Manager.

4. Uruchom powłokę usługi Cloud Shell. Wybierz pozycję na ikonie w prawym górnym rogu:

    ![Uruchom powłokę Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Po rozpoczęciu pracy z usługą Cloud Shell może zajść potrzeba przełączenia się do programu PowerShell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Ustaw kontekst. Wpisz:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Oto przykładowe dane wyjściowe:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Jeśli masz jakieś starsze moduły PS, należy je zainstalować.

    `Remove-Module  Az.DataBoxEdge -force`

    Oto przykładowe dane wyjściowe. W tym przykładzie nie ma żadnych starych modułów do zainstalowania.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Następny zestaw poleceń spowoduje pobranie i uruchomienie skryptu w celu zainstalowania modułów programu PowerShell.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. W następnym zestawie poleceń należy podać nazwę zasobu, nazwę grupy zasobów, klucz szyfrowania i hasło określone w poprzednim kroku.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    Parametry hasła i klucza szyfrowania muszą zostać przesłane jako bezpieczne ciągi. Użyj następujących poleceń cmdlet, aby przekonwertować hasło i klucz szyfrowania na zabezpieczone ciągi.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Użyj powyższych wygenerowanych zabezpieczonych ciągów jako parametrów w poleceniu cmdlet Set-AzDataBoxEdgeUser, aby zresetować hasło. Użyj tej samej grupy zasobów, która została użyta podczas tworzenia zasobu Azure Stack EDGE Pro/Data Box Gateway.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Oto przykładowe dane wyjściowe.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Użyj nowego hasła, aby nawiązać połączenie z usługą Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

[Połącz z Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)
