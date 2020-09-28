---
title: Wdrażanie aplikacji w klastrze zarządzanym Service Fabric za pomocą programu PowerShell (wersja zapoznawcza)
description: W tym samouczku nastąpi połączenie z klastrem zarządzanym Service Fabric i wdrożeniem aplikacji za pomocą programu PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410459"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Samouczek: wdrażanie aplikacji w klastrze zarządzanym Service Fabric (wersja zapoznawcza)

W tej serii samouczków omówiono następujące kwestie:

> [!div class="checklist"]
> * [Jak wdrożyć klaster zarządzany Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Jak skalować w poziomie Service Fabric zarządzany klaster](tutorial-managed-cluster-scale.md)
> * [Jak dodać i usunąć węzły w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * Jak wdrożyć aplikację w klastrze zarządzanym Service Fabric

Ta część serii obejmuje następujące elementy:

> [!div class="checklist"]
> * Nawiązywanie połączenia z klastrem zarządzanym Service Fabric
> * Przekazywanie aplikacji do klastra
> * Tworzenie wystąpienia aplikacji w klastrze
> * Usuwanie aplikacji z klastra

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster zarządzany Service Fabric (zobacz [*wdrażanie zarządzanego klastra*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Aby nawiązać połączenie z klastrem, potrzebny będzie odcisk palca certyfikatu klastra. Tę wartość można znaleźć we właściwościach klastra w danych wyjściowych wdrożenia zasobu lub przez zapytanie o właściwości klastra w istniejącym zasobie.

Następujące polecenie służy do wysyłania zapytań do zasobu klastra dla odcisku palca certyfikatu klastra.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Odcisk palca certyfikatu klastra umożliwia nawiązanie połączenia z klastrem.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Przekaż pakiet aplikacji

W tym samouczku będziemy używać przykładowej [aplikacji do głosowania Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) . Aby uzyskać więcej informacji na temat wdrażania aplikacji Service Fabric przy użyciu programu PowerShell [, zobacz Service Fabric wdrażania i usuwania aplikacji](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> W wersji zapoznawczej klastra zarządzanego Service Fabric nie będzie można publikować aplikacji bezpośrednio z programu Visual Studio.

Najpierw należy [spakować aplikację do wdrożenia](service-fabric-package-apps.md). W tym samouczku postępuj zgodnie z instrukcjami dotyczącymi tworzenia pakietów aplikacji z poziomu programu Visual Studio. Ważne jest zanotować ścieżkę, w której aplikacja została spakowana, ponieważ zostanie użyta dla poniższej ścieżki.

Po utworzeniu pakietu aplikacji można przekazać pakiet aplikacji do klastra. Zaktualizuj `$path` wartość reprezentującą ścieżkę, w której znajduje się pakiet aplikacji, i uruchom następujące polecenie:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Tworzenie aplikacji

Można utworzyć wystąpienie aplikacji z dowolnej wersji typu aplikacji, która została pomyślnie zarejestrowana, za pomocą polecenia cmdlet New-ServiceFabricApplication. Nazwa każdej aplikacji musi zaczynać się od schematu "Fabric:" i musi być unikatowa dla każdego wystąpienia aplikacji. Tworzone są również wszystkie domyślne usługi zdefiniowane w manifeście aplikacji typu aplikacji docelowej.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Po zakończeniu tej operacji powinny być widoczne wystąpienia aplikacji działające w Service Fabric Explorer.

### <a name="remove-an-application"></a>Usuwanie aplikacji

Gdy wystąpienie aplikacji nie jest już potrzebne, można trwale usunąć je przez nazwę za pomocą `Remove-ServiceFabricApplication` polecenia cmdlet, co spowoduje również automatyczne usunięcie wszystkich usług należących do aplikacji.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Następne kroki

W tym kroku wdrożono aplikację w klastrze zarządzanym Service Fabric. Aby dowiedzieć się więcej na temat Service Fabric zarządzanych klastrów, zobacz:

> [!div class="nextstepaction"]
> [Service Fabric często zadawane pytania dotyczące klastrów zarządzanych](faq-managed-cluster.md)
