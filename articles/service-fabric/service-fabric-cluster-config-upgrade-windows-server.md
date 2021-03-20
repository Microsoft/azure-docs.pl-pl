---
title: Uaktualnianie konfiguracji klastra autonomicznego
description: Dowiedz się, jak uaktualnić konfigurację, w której działa autonomiczny klaster Service Fabric.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842618"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uaktualnianie konfiguracji klastra autonomicznego 

W przypadku każdego nowoczesnego systemu możliwość uaktualnienia jest kluczem do długoterminowej sukcesu produktu. Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem. W tym artykule opisano sposób uaktualniania ustawień konfiguracji autonomicznego klastra Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Dostosuj ustawienia klastra w ClusterConfig.jspliku
Klastry autonomiczne są konfigurowane za pomocą *ClusterConfig.js* pliku. Aby dowiedzieć się więcej na temat różnych ustawień, zobacz [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md).

Ustawienia można dodawać, aktualizować i usuwać w `fabricSettings` sekcji [właściwości klastra](./service-fabric-cluster-manifest.md#cluster-properties) w *ClusterConfig.jsna*. 

Na przykład poniższy kod JSON dodaje nowe ustawienie *MaxDiskQuotaInMB* do sekcji *Diagnostyka* w obszarze `fabricSettings` :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Po zmodyfikowaniu ustawień w ClusterConfig.jsw pliku [Przetestuj konfigurację klastra](#test-the-cluster-configuration) , a następnie [Uaktualnij konfigurację klastra](#upgrade-the-cluster-configuration) , aby zastosować ustawienia do klastra. 

## <a name="test-the-cluster-configuration"></a>Testowanie konfiguracji klastra
Przed rozpoczęciem uaktualniania konfiguracji można przetestować nowy plik JSON konfiguracji klastra, uruchamiając następujący skrypt programu PowerShell w pakiecie autonomicznym:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Lub Użyj tego skryptu:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Niektórych konfiguracji nie można uaktualnić, takich jak punkty końcowe, nazwa klastra, adres IP węzła itd. Plik JSON nowej konfiguracji klastra jest testowany pod kątem starego i zgłasza błędy w oknie programu PowerShell, jeśli wystąpił problem.

## <a name="upgrade-the-cluster-configuration"></a>Uaktualnij konfigurację klastra
Aby uaktualnić uaktualnienie konfiguracji klastra, uruchom polecenie [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Uaktualnienie konfiguracji jest przetwarzane przez domenę uaktualnień.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Uaktualnij konfigurację certyfikatu klastra
Certyfikat klastra jest używany do uwierzytelniania między węzłami klastra. Przerzucanie certyfikatów powinno być wykonywane z zachowaniem dodatkowej uwagi, ponieważ błąd blokuje komunikację między węzłami klastra.

Obsługiwane są cztery opcje:  

* Uaktualnienie pojedynczego certyfikatu: ścieżka uaktualnienia to certyfikat A (podstawowy) — > certyfikat B (podstawowy) — > certyfikat C (podstawowy) — >...

* Podwójne uaktualnienie certyfikatu: ścieżka uaktualnienia to certyfikat A (podstawowy) — > certyfikat A (podstawowy) i B (pomocniczy) — > certyfikat B (podstawowy)-> Certificate B (podstawowy) i C (pomocniczy)-> certyfikat C (podstawowy)->...

* Uaktualnienie typu certyfikatu: Konfiguracja certyfikatu oparta na odcisku palca <-> konfiguracja pospolita certyfikatu opartego na certyfikacie. Przykład: odcisk palca certyfikatu A (podstawowy) i odcisk palca B (pomocniczy) — > pospolity certyfikat.

* Uaktualnienie odcisku palca wystawcy certyfikatu: ścieżka uaktualnienia to certyfikat CN = A, IssuerThumbprint = IT1 (podstawowy) — > certyfikat CN = A, IssuerThumbprint = IT1, IT2 (podstawowy) — > certyfikat CN = A, IssuerThumbprint = IT2 (podstawowy).


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak dostosować niektóre [Service Fabric ustawienia klastra](service-fabric-cluster-fabric-settings.md).
* Dowiedz się [, jak skalować klaster w programie i na zewnątrz](service-fabric-cluster-scale-in-out.md).
* Dowiedz się więcej na temat [uaktualnień aplikacji](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
