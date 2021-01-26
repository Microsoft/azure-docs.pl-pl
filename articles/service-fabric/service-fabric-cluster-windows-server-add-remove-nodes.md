---
title: Dodawanie lub usuwanie węzłów w autonomicznym klastrze Service Fabric
description: Dowiedz się, jak dodawać lub usuwać węzły do klastra Service Fabric platformy Azure na maszynie fizycznej lub wirtualnej z systemem Windows Server, która może być lokalna lub w dowolnej chmurze.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790600"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Dodawanie węzłów do autonomicznego klastra usługi Service Fabric uruchomionego na serwerze Windows Server lub usuwanie go
Po [utworzeniu autonomicznego klastra Service Fabric na maszynach z systemem Windows Server](service-fabric-cluster-creation-for-windows-server.md)potrzeby (biznesowe) mogą ulec zmianie i trzeba będzie dodać lub usunąć węzły do klastra zgodnie z opisem w tym artykule.

> [!NOTE]
> Funkcje dodawania i usuwania węzłów nie są obsługiwane w lokalnych klastrach programistycznych.

## <a name="add-nodes-to-your-cluster"></a>Dodawanie węzłów do klastra

1. Przygotuj maszynę wirtualną/maszynę, którą chcesz dodać do klastra, wykonując czynności opisane w temacie [Planowanie i przygotowywanie wdrożenia klastra Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md).

2. Zidentyfikuj domenę błędów i domenę uaktualnienia, do której chcesz dodać tę maszynę wirtualną/maszynę.

   W przypadku użycia certyfikatów do zabezpieczenia klastra należy zainstalować certyfikaty w lokalnych magazynach certyfikatów w ramach przygotowania do przyłączenia węzła do klastra. Wartość analogowa ma zastosowanie w przypadku korzystania z innych form zabezpieczeń.

3. Pulpitu zdalnego (RDP) do maszyny wirtualnej/maszyny, którą chcesz dodać do klastra.

4. Skopiuj lub [Pobierz pakiet autonomiczny dla Service Fabric dla systemu Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) do maszyny wirtualnej/maszyny i Rozpakuj pakiet.

5. Uruchom program PowerShell z podwyższonym poziomem uprawnień i przejdź do lokalizacji rozpakowanego pakietu.

6. Uruchom skrypt *AddNode.ps1* z parametrami opisującymi nowy węzeł do dodania. Poniższy przykład dodaje nowy węzeł o nazwie VM5, z typem NodeType0 i adresem IP 182.17.34.52, do UD1 i FD:/DC1/R0. `ExistingClusterConnectionEndPoint` jest punktem końcowym połączenia dla węzła znajdującego się już w istniejącym klastrze, który może być adresem IP *dowolnego* węzła w klastrze. 

   Niezabezpieczone (prototypowanie):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Bezpieczne (oparte na certyfikatach):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Po zakończeniu działania skryptu można sprawdzić, czy nowy węzeł został dodany, uruchamiając polecenie cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) .

7. Aby zapewnić spójność w różnych węzłach w klastrze, należy zainicjować uaktualnienie konfiguracji. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) , aby pobrać najnowszy plik konfiguracji i dodać nowo dodany węzeł do sekcji "nodes" (węzły). Zalecane jest również, aby zawsze była dostępna Najnowsza Konfiguracja klastra, jeśli trzeba ponownie wdrożyć klaster, który ma taką samą konfigurację.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Uruchom [menu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) , aby rozpocząć uaktualnianie.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Postęp uaktualniania można monitorować na Service Fabric Explorer. Alternatywnie można uruchomić polecenie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Dodawanie węzłów do klastrów skonfigurowanych z zabezpieczeniami systemu Windows przy użyciu gMSA
W przypadku klastrów skonfigurowanych za pomocą konta usługi zarządzanego przez grupę (gMSA) ( https://technet.microsoft.com/library/hh831782.aspx) nowy węzeł można dodać przy użyciu uaktualnienia konfiguracji:
1. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) na dowolnym z istniejących węzłów, aby uzyskać najnowszy plik konfiguracji, i Dodaj szczegółowe informacje o nowym węźle, który chcesz dodać do sekcji "węzły". Upewnij się, że nowy węzeł jest częścią tego samego konta zarządzanego przez grupę. To konto powinno być kontem administratora na wszystkich komputerach.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Uruchom [menu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) , aby rozpocząć uaktualnianie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Postęp uaktualniania można monitorować na Service Fabric Explorer. Alternatywnie można uruchomić polecenie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)

### <a name="add-node-types-to-your-cluster"></a>Dodawanie typów węzłów do klastra
Aby dodać nowy typ węzła, należy zmodyfikować konfigurację w celu uwzględnienia nowego typu węzła w sekcji "elementów NodeType" w obszarze "właściwości" i rozpocząć uaktualnianie konfiguracji przy użyciu polecenia [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Po zakończeniu uaktualniania można dodać do klastra nowe węzły z tym typem węzła.

## <a name="remove-nodes-from-your-cluster"></a>Usuwanie węzłów z klastra
Węzeł może zostać usunięty z klastra przy użyciu uaktualnienia konfiguracji w następujący sposób:

1. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) , aby pobrać najnowszy plik konfiguracji i *usunąć* węzeł z sekcji "nodes" (węzły).
Dodaj parametr "NodesToBeRemoved" do sekcji "Setup" w sekcji "FabricSettings". "Wartość" powinna być rozdzielaną przecinkami listą nazw węzłów węzłów, które należy usunąć.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Uruchom [menu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) , aby rozpocząć uaktualnianie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Postęp uaktualniania można monitorować na Service Fabric Explorer. Alternatywnie można uruchomić polecenie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

> [!NOTE]
> Usunięcie węzłów może inicjować wiele uaktualnień. Niektóre węzły są oznaczone `IsSeedNode=”true”` tagami i mogą być identyfikowane przez przeszukiwanie manifestu klastra przy użyciu `Get-ServiceFabricClusterManifest` . Usunięcie takich węzłów może potrwać dłużej niż inne, ponieważ węzły inicjatora będą musiały zostać przeniesione w taki sposób. Klaster musi obsługiwać co najmniej 3 węzły podstawowego typu węzła.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Usuwanie typów węzłów z klastra
Przed usunięciem typu węzła Sprawdź, czy istnieją węzły odwołujące się do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu odpowiednich węzłów można usunąć NodeType z konfiguracji klastra i rozpocząć uaktualnianie konfiguracji przy użyciu polecenia [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).


### <a name="replace-primary-nodes-of-your-cluster"></a>Zastąp podstawowe węzły klastra
Zastąpienie węzłów podstawowych powinno odbywać się po jednym węźle, zamiast usuwać, a następnie dodawać w partiach.


## <a name="next-steps"></a>Następne kroki
* [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu certyfikatów x509](service-fabric-windows-cluster-x509-security.md)
* [Tworzenie autonomicznego klastra Service Fabric z maszynami wirtualnymi platformy Azure z systemem Windows](./service-fabric-cluster-creation-via-arm.md)
