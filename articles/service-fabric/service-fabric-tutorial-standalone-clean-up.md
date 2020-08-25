---
title: Oczyszczanie klastra autonomicznego
description: W tym samouczku dowiesz się, jak oczyścić AWS lub zasoby platformy Azure w autonomicznym klastrze Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "75639024"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: czyszczenie klastra autonomicznego

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz klaster autonomiczny hostowany na AWS lub na platformie Azure i zainstaluje do niego aplikację.

Niniejszy samouczek jest czwartą częścią serii. W tej części samouczka pokazano, jak wyczyścić AWS lub zasoby platformy Azure, które zostały utworzone w celu hostowania klastra Service Fabric.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie klastra usługi Service Fabric
> * Czyszczenie zasobów AWS lub platformy Azure

## <a name="clean-up-service-fabric-cluster"></a>Czyszczenie klastra usługi Service Fabric

1. Połącz protokół RDP z maszyną wirtualną, która została użyta do zainstalowania Service Fabric.
2. Otwórz program PowerShell.
3. Zmień katalog na wyodrębniony folder z drugiej części samouczka.
4. Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Wprowadź `Y` po wyświetleniu monitu, jeśli dane wyjściowe będą wyglądały jak poniżej, z własnymi adresami IP zastępują:

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

## <a name="clean-up-aws-resources"></a>Czyszczenie zasobów usługi AWS

1. Zaloguj się do konta AWS.
2. Przejdź do konsoli EC2.
3. Wybierz trzy węzły utworzone w pierwszej części samouczka.
4. Kliknij pozycję **Akcje**  >  **stan wystąpienia**  >  **Przerwij**.

## <a name="clean-up-azure-resources"></a>Czyszczenie zasobów platformy Azure

1. Zaloguj się do witryny Azure Portal.
2. Przejdź do sekcji **Virtual Machines** .
3. Zaznacz pola wyboru dla trzech węzłów utworzonych w pierwszej części samouczka.
4. Kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W czwartej części samouczka przedstawiono proces czyszczenia zasobów utworzonych w poprzednich częściach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)
