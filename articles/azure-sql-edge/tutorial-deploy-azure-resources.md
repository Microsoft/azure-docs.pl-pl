---
title: Konfigurowanie zasobów na potrzeby wdrażania modelu ML w usłudze Azure SQL Edge
description: W pierwszej części tego samouczka dotyczącego usługi Azure SQL Edge na potrzeby przewidywania zanieczyszczeń żelaza należy zainstalować wstępnie wymagane oprogramowanie i skonfigurować wymagane zasoby platformy Azure na potrzeby wdrażania modelu uczenia maszynowego w usłudze Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 76c45e586ea7101015cb878d198cab73ed32498e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018250"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>Instalowanie oprogramowania i Konfigurowanie zasobów dla samouczka

W tym samouczku z trzema częściami utworzysz model uczenia maszynowego służący do przewidywania zanieczyszczeń żelaza jako procent krzemionki, a następnie wdrożenie modelu w usłudze Azure SQL Edge. W części pierwszej należy zainstalować wymagane oprogramowanie i wdrożyć zasoby platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. Zainstaluj środowisko [Python 3.6.8](https://www.python.org/downloads/release/python-368/).
      * Użyj Instalatora plików wykonywalnych systemu Windows x86-x64
      * Dodaj `python.exe` do zmiennej środowiskowej PATH pobiera/). Pobieranie można znaleźć w sekcji "Tools for Visual Studio 2019".
3. Zainstaluj [Sterownik Microsoft ODBC Driver 17 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=56567).
4. Zainstaluj [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/)
5. Otwórz Azure Data Studio i skonfiguruj Język Python dla notesów.Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie języka Python dla notesów](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks). Ten krok może potrwać kilka minut.
6. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020). Następujące skrypty wymagają, aby AZ PowerShell był najnowszą wersją (3.5.0, luty 2020).
7. Pobierz [pliki obrazów platformy Docker dacpac i AMD/ARM](https://www.docker.com/blog/multi-arch-images/) , które zostaną wykorzystane w samouczku. [DACPAC](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/DACPAC)

## <a name="deploy-azure-resources-using-powershell-script"></a>Wdrażanie zasobów platformy Azure przy użyciu skryptu programu PowerShell

Wdróż zasoby platformy Azure wymagane przez ten samouczek usługi Azure SQL Edge. Można je wdrożyć przy użyciu skryptu programu PowerShell lub za pomocą Azure Portal. Ten samouczek używa skryptu programu PowerShell.

1. Zaimportuj moduły, które są konieczne do uruchomienia skryptu programu PowerShell w tym samouczku.

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. Zadeklaruj zmienne wymagane przez skrypt programu PowerShell.

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. Zadeklaruj resztę zmiennych.

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. Aby rozpocząć tworzenie zasobów, zaloguj się do platformy Azure.

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. Ustaw identyfikator subskrypcji platformy Azure.

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. Utwórz grupę zasobów, jeśli jeszcze nie istnieje.

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. Utwórz konto magazynu i kontener konta magazynu w grupie zasobów.

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. Przekaż plik bazy danych `dacpac` na konto magazynu i Wygeneruj adres URL sygnatury dostępu współdzielonego dla obiektu BLOB. **Zanotuj adres URL sygnatury dostępu współdzielonego dla `dacpac` obiektu BLOB bazy danych.**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. Utwórz usługę Azure Container Registry w ramach tej grupy zasobów.

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. Wypychanie obrazów platformy Docker ARM/AMD do rejestru kontenerów.

    ```powershell
    $containerRegistryCredentials = Get-AzContainerRegistryCredential -ResourceGroupName $ResourceGroup -Name $containerRegistryName
    
    $amddockerimageFile = Read-Host "Please Enter the location to the amd docker tar file:"
    $armdockerimageFile = Read-Host "Please Enter the location to the arm docker tar file:"
    $amddockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":amd64"
    $armdockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":arm64"
    
    docker login $containerRegistry.LoginServer --username $containerRegistryCredentials.Username --password $containerRegistryCredentials.Password
    
    docker import $amddockerimageFile $amddockertag
    docker push $amddockertag
    
    docker import $armdockerimageFile $armdockertag
    docker push $armdockertag
    ```

11. Utwórz sieciową grupę zabezpieczeń w grupie zasobów.

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

12. Utwórz maszynę wirtualną platformy Azure z włączoną funkcją SQL Edge. Ta maszyna wirtualna będzie pełnić funkcję urządzenia brzegowego.

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

13. Utwórz Centrum IoT Hub w grupie zasobów.

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

14. Dodaj urządzenie brzegowe do centrum IoT Hub. Ten krok powoduje utworzenie tylko tożsamości cyfrowej urządzenia.

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

15. Pobierz podstawowe parametry połączenia urządzenia. Będzie on potrzebny później dla maszyny wirtualnej. Następujące polecenie używa interfejsu wiersza polecenia platformy Azure dla wdrożeń.

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

16. Zaktualizuj parametry połączenia w pliku konfiguracji IoT Edge na urządzeniu brzegowym. Poniższe polecenia używają interfejsu wiersza polecenia platformy Azure do wdrożeń.

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

17. Utwórz obszar roboczy Azure Machine Learning w grupie zasobów.

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie modułów IoT Edge i połączeń](tutorial-set-up-iot-edge-modules.md)
