---
title: Zarządzanie aplikacjami dla wielu środowisk
description: Aplikacje Service Fabric platformy Azure można uruchamiać w klastrach, które mają zakres wielkości z jednego komputera na tysiące maszyn. W niektórych przypadkach należy skonfigurować aplikację inaczej dla tych różnych środowisk. W tym artykule opisano sposób definiowania różnych parametrów aplikacji dla danego środowiska.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78196983"
---
# <a name="manage-applications-for-multiple-environments"></a>Zarządzanie aplikacjami dla wielu środowisk

Klastry usługi Azure Service Fabric umożliwiają tworzenie klastrów przy użyciu dowolnego miejsca z jednego do wielu komputerów tysięcy. W większości przypadków należy wdrożyć aplikację w wielu konfiguracjach klastra: lokalny klaster programistyczny, współużytkowany klaster programistyczny i klaster produkcyjny. Wszystkie te klastry są traktowane jako różne środowiska, w których kod musi działać. Pliki binarne aplikacji można uruchamiać bez modyfikacji w tym szerokim spektrum, ale często należy skonfigurować aplikację inaczej.

Rozważmy dwa proste przykłady:
  - Usługa nasłuchuje na określonym porcie, ale potrzebujesz tego portu w różnych środowiskach
  - należy podać inne poświadczenia powiązania dla bazy danych w środowiskach

## <a name="specifying-configuration"></a>Określanie konfiguracji

Podaną konfigurację można podzielić na dwie kategorie:

- Konfiguracja, która ma zastosowanie w przypadku uruchamiania usług
  - Na przykład numer portu punktu końcowego lub liczba wystąpień usługi
  - Ta konfiguracja jest określona w pliku manifestu aplikacji lub usługi
- Konfiguracja, która ma zastosowanie do kodu aplikacji
  - Na przykład informacje o powiązaniu dla bazy danych
  - Tę konfigurację można określić za pomocą plików konfiguracji lub zmiennych środowiskowych

> [!NOTE]
> Nie wszystkie atrybuty w pliku manifestu aplikacji i usługi są obsługiwane przez parametry.
> W takich przypadkach należy polegać na podstawianiu ciągów w ramach przepływu pracy wdrażania. Na platformie Azure DevOps można użyć rozszerzenia, takiego jak Replace Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens lub w Jenkins można uruchomić zadanie skryptu, aby zastąpić wartości.
>

## <a name="specifying-parameters-during-application-creation"></a>Określanie parametrów podczas tworzenia aplikacji

Podczas tworzenia nazwanych wystąpień aplikacji w programie Service Fabric istnieje możliwość przekazania parametrów. W ten sposób zależy od sposobu tworzenia wystąpienia aplikacji.

  - W programie PowerShell [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenie cmdlet przyjmuje parametry aplikacji jako tablicę skrótów.
  - Przy użyciu sfctl [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) polecenie przyjmuje parametry jako ciąg JSON. Skrypt install.sh używa sfctl.
  - Program Visual Studio udostępnia zestaw plików parametrów w folderze Parameters w projekcie aplikacji. Te pliki parametrów są używane podczas publikowania z programu Visual Studio przy użyciu Azure DevOps Services lub Azure DevOps Server. W programie Visual Studio pliki parametrów są przesyłane do skryptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach pokazano, jak używać niektórych pojęć opisanych tutaj:

- [Jak określić zmienne środowiskowe dla usług w Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Jak określić numer portu usługi przy użyciu parametrów w Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak Sparametryzuj pliki konfiguracji](service-fabric-how-to-parameterize-configuration-files.md)

- [Odwołanie do zmiennej środowiskowej](service-fabric-environment-variables-reference.md)
