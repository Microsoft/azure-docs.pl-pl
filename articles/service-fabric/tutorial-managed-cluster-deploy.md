---
title: Wdróż klaster zarządzany Service Fabric (wersja zapoznawcza)
description: W tym samouczku zostanie wdrożony Service Fabric zarządzany klaster na potrzeby testowania.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: c7ed1a8fceeddecb942edb541c6112492a6e5a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410452"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Samouczek: Wdrażanie klastra zarządzanego Service Fabric (wersja zapoznawcza)

W tej serii samouczków omówiono następujące kwestie:

> [!div class="checklist"]
> * Jak wdrożyć klaster zarządzany Service Fabric 
> * [Jak skalować w poziomie Service Fabric zarządzany klaster](tutorial-managed-cluster-scale.md)
> * [Jak dodać i usunąć węzły w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Jak wdrożyć aplikację w klastrze zarządzanym Service Fabric](tutorial-managed-cluster-deploy-app.md)

Ta część serii obejmuje następujące elementy:

> [!div class="checklist"]
> * Nawiąż połączenie z kontem platformy Azure
> * Tworzenie nowej grupy zasobów
> * Wdróż klaster zarządzany Service Fabric
> * Dodawanie typu węzła podstawowego do klastra

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli nie masz jeszcze subskrypcji platformy Azure

* Zainstaluj [zestaw Service Fabric SDK i moduł programu PowerShell](service-fabric-get-started.md).

* Zainstaluj [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) (lub nowszy).

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Zastąp `<your-subscription>` ciąg subskrypcją konta platformy Azure, a następnie połącz:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów

Następnie utwórz grupę zasobów dla klastra zarządzanego Service Fabric, zastępując `<your-rg>` i `<location>` z żądaną nazwą i lokalizacją grupy.

> [!NOTE]
> Obsługiwane regiony dla publicznej wersji zapoznawczej obejmują,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` , i `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Wdróż klaster zarządzany Service Fabric

### <a name="create-a-service-fabric-managed-cluster"></a>Tworzenie klastra zarządzanego usługi Service Fabric

W tym kroku utworzysz klaster zarządzany Service Fabric przy użyciu polecenia programu PowerShell New-AzServiceFabricManagedCluster. W poniższym przykładzie tworzony jest klaster o nazwie "Moja klaster" w grupie zasobów o nazwie Moje zasoby. Ta grupa zasobów została utworzona w poprzednim kroku w regionie eastus2.

W tym kroku Podaj własne wartości dla następujących parametrów:

* **Nazwa klastra**: Wprowadź unikatową nazwę klastra, na przykład *mysfcluster*.
* **Hasło administratora**: wprowadź hasło administratora, które ma być używane na potrzeby protokołu RDP na podstawowych maszynach wirtualnych w klastrze.
* **Odcisk palca certyfikatu klienta**: Podaj odcisk palca certyfikatu klienta, który ma być używany w celu uzyskania dostępu do klastra. Jeśli nie masz certyfikatu, postępuj zgodnie z [zestawem i pobieraniem certyfikatu](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) w celu utworzenia certyfikatu z podpisem własnym.
* **Jednostka SKU klastra**: określ [Typ Service Fabric zarządzanego klastra](overview-managed-cluster.md#service-fabric-managed-cluster-skus) do wdrożenia. *Podstawowa* Klastry SKU są przeznaczone wyłącznie do wdrożeń testowych i nie zezwalają na dodawanie lub usuwanie typu węzła.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Dodawanie typu węzła podstawowego do Service Fabric zarządzanego klastra

W tym kroku dodasz typ węzła podstawowego do klastra, który właśnie został utworzony. Każdy klaster Service Fabric musi mieć co najmniej jeden typ węzła podstawowego.

W tym kroku Podaj własne wartości dla następujących parametrów:

* **Nazwa typu węzła**: Wprowadź unikatową nazwę typu węzła, który ma zostać dodany do klastra, na przykład "NT1".

> [!NOTE]
> Jeśli dodawany typ węzła to pierwszy lub jedyny typ węzła w klastrze, należy użyć właściwości podstawowej.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Wykonanie tego polecenia może potrwać kilka minut.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrażania Znajdź Service Fabric Explorer wartość na stronie Przegląd Service Fabric zarządzanych zasobów klastra w portalu. Po wyświetleniu monitu o certyfikat Użyj certyfikatu, dla którego odcisk palca klienta został podany w poleceniu programu PowerShell.

## <a name="next-steps"></a>Następne kroki

W tym kroku utworzyliśmy i wdrożono pierwszy Service Fabric zarządzany klaster. Aby dowiedzieć się więcej na temat skalowania klastra, zobacz:

> [!div class="nextstepaction"]
> [Skalowanie w poziomie Service Fabric klastra zarządzanego](tutorial-managed-cluster-scale.md)
