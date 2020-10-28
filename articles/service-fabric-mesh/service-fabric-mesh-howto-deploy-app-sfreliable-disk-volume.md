---
title: Service Fabric niezawodny wolumin dysku z siatką Service Fabric
description: Dowiedz się, jak przechowywać informacje o stanie w aplikacji sieci Service Fabricej na platformie Azure, instalując Service Fabric niezawodny wolumin oparty na dyskach wewnątrz kontenera przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 1db412a3b83da905475749deea0432fbeae06e8f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742776"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Instalowanie wysokiej dostępności Service Fabric niezawodnego woluminu opartego na dyskach w aplikacji Service Fabric siatki 
Typową metodą utrwalania stanu przy użyciu aplikacji kontenera jest używanie zdalnego magazynu, takiego jak usługa Azure File Storage lub baza danych, taka jak Azure Cosmos DB. Wiąże się to z istotnym opóźnieniem odczytu i zapisu sieci dla magazynu zdalnego.

W tym artykule pokazano, jak przechowywać stan w Service Fabric niezawodnego dysku przez zainstalowanie woluminu wewnątrz kontenera aplikacji Service Fabric siatki.
Service Fabric niezawodny dysk zapewnia woluminy do odczytu lokalnego z zapisami replikowanymi w klastrze Service Fabric w celu zapewnienia wysokiej dostępności. Spowoduje to usunięcie wywołań sieciowych dla operacji odczytu i zmniejsza czas oczekiwania sieci na zapis. Jeśli kontener zostanie ponownie uruchomiony lub przejdzie do innego węzła, nowe wystąpienie kontenera będzie widziało ten sam wolumin jak starszy. W ten sposób jest to zarówno wydajna, jak i wysoka dostępność.

W tym przykładzie aplikacja licznika ma usługę ASP.NET Core ze stroną sieci Web, która wyświetla wartość licznika w przeglądarce.

`counterService`Okresowo odczytuje wartość licznika z pliku, zwiększa ją i zapisuje z powrotem do pliku. Plik jest przechowywany w folderze, który jest instalowany na woluminie, Service Fabric niezawodny dysk.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać to zadanie, można użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Aby użyć interfejsu wiersza polecenia platformy Azure z tym artykułem, upewnij się, że program `az --version` zwraca co najmniej `azure-cli (2.0.43)` .  Zainstaluj (lub zaktualizuj) moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric siatka, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której zostanie wdrożona aplikacja. Następujące polecenie tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji w Stany Zjednoczone wschodnim. W przypadku zmiany nazwy grupy zasobów w poniższym poleceniu Pamiętaj, aby zmienić ją we wszystkich kolejnych poleceniach.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Następujące polecenie wdraża aplikację systemu Linux przy użyciu [counter.sfreliablevolume.linux.jsna szablonie](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Aby wdrożyć aplikację systemu Windows, użyj [counter.sfreliablevolume.windows.jsna szablonie](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Należy pamiętać, że wdrażanie większych obrazów kontenerów może trwać dłużej.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Możesz również zobaczyć stan wdrożenia za pomocą polecenia

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Zwróć uwagę na nazwę zasobu bramy, który ma typ zasobu jako `Microsoft.ServiceFabricMesh/gateways` . Ta wartość zostanie użyta w celu uzyskania publicznego adresu IP aplikacji.

## <a name="open-the-application"></a>Otwieranie aplikacji

Po pomyślnym wdrożeniu aplikacji Pobierz adres IP zasobu bramy dla aplikacji. Użyj nazwy bramy podanej w poprzedniej sekcji.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Dane wyjściowe powinny mieć właściwość `ipAddress` , która jest publicznym adresem IP dla punktu końcowego usługi. Otwórz go w przeglądarce. Zostanie wyświetlona strona sieci Web z wartością licznika, która jest aktualizowana co sekundę.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja może korzystać z woluminu

Aplikacja tworzy plik o nazwie `counter.txt` w folderze woluminu wewnątrz `counter/counterService` . Zawartość tego pliku jest wartością licznika wyświetlaną na stronie sieci Web.

## <a name="delete-the-resources"></a>Usuwanie zasobów

Często usuwaj zasoby, które nie są już używane na platformie Azure. Aby usunąć zasoby związane z tym przykładem, Usuń grupę zasobów, w której zostały wdrożone (co spowoduje usunięcie wszystkich skojarzonych z tą grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Wyświetl przykładową aplikację Service Fabric niezawodnego dysku woluminu w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
