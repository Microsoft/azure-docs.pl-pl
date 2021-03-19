---
title: Oczyszczanie klastra autonomicznego
description: W tym samouczku dowiesz się, jak usunąć AWS lub zasoby platformy Azure dla autonomicznego klastra Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842890"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: czyszczenie klastra autonomicznego

Service Fabric klastry autonomiczne umożliwiają wybranie własnego środowiska do hostowania Service Fabric. W tej serii samouczków utworzysz klaster autonomiczny hostowany na AWS lub na platformie Azure i wdrożono w nim aplikację.

Niniejszy samouczek jest czwartą częścią serii. W tej części samouczka przedstawiono sposób usuwania zasobów AWS lub platformy Azure, które zostały utworzone w celu hostowania klastra Service Fabric.

W tym artykule przedstawiono następujące informacje:

> [!div class="checklist"]
> * Usuwanie klastra Service Fabric
> * Usuwanie zasobów AWS lub platformy Azure

## <a name="remove-a-service-fabric-cluster"></a>Usuwanie klastra Service Fabric

1. Połącz protokół RDP z maszyną wirtualną, która została użyta do zainstalowania Service Fabric.
2. Otwórz program PowerShell.
3. Zmień katalog na wyodrębniony folder z drugiej części samouczka.
4. Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Wprowadź `Y` po wyświetleniu monitu. Jeśli zakończyło się pomyślnie, dane wyjściowe będą wyglądać następująco (przy użyciu własnych adresów IP):

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>Usuwanie zasobów AWS

1. Zaloguj się do konta AWS.
2. Przejdź do konsoli EC2.
3. Wybierz trzy węzły utworzone w pierwszej części samouczka.
4. Wybierz   >  **stan wystąpienia** akcji  >  **Przerwij**.

## <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure

1. Zaloguj się w witrynie Azure Portal.
2. Przejdź do sekcji **Virtual Machines** .
3. Zaznacz pola wyboru dla trzech węzłów utworzonych w pierwszej części samouczka.
4. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób usuwania zasobów utworzonych w poprzednich krokach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)
