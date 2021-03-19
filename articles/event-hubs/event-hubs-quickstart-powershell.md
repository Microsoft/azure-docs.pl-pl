---
title: 'Szybki Start: tworzenie centrum zdarzeń przy użyciu programu PowerShell — Event Hubs platformy Azure'
description: Ten przewodnik Szybki start przedstawia tworzenie centrum zdarzeń za pomocą programu Azure PowerShell oraz wysyłanie i odbieranie zdarzeń za pomocą zestawu .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933974"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Szybki start: tworzenie centrum zdarzeń za pomocą programu Azure PowerShell

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym przewodniku Szybki start utworzysz centrum zdarzeń za pomocą programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto][] .
- [Program Visual Studio 2019](https://www.visualstudio.com/vs).
- [Zestaw .NET Standard SDK](https://www.microsoft.com/net/download/windows) w wersji 2.0 lub nowszej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli używasz programu PowerShell lokalnie, musisz uruchomić najnowszą wersję programu PowerShell, aby ukończyć ten przewodnik Szybki start. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczna kolekcja zasobów platformy Azure i jest ona potrzebna, aby utworzyć centrum zdarzeń. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Wschodnie stany USA. Zastąp ciąg `myResourceGroup` nazwą grupy zasobów, której chcesz używać:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Po utworzeniu grupy zasobów utwórz przestrzeń nazw usługi Event Hubs w ramach tej grupy zasobów. Przestrzeń nazw usługi Event Hubs zapewnia unikatową, w pełni kwalifikowaną nazwę domeny, w której można utworzyć centrum zdarzeń. Zastąp ciąg `namespace_name` unikatową nazwą swojej przestrzeni nazw:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Teraz, gdy masz przestrzeń nazw usługi Event Hubs, utwórz centrum zdarzeń w ramach tej przestrzeni nazw:  
Dozwolony okres dla parametru `MessageRetentionInDays` to od 1 do 7 dni.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gratulacje! Za pomocą programu Azure PowerShell utworzono przestrzeń nazw usługi Event Hubs i centrum zdarzeń w ramach tej przestrzeni nazw. 

## <a name="next-steps"></a>Następne kroki

W ramach tego artykułu utworzono przestrzeń nazw usługi Event Hubs oraz użyto aplikacji przykładowych do wysyłania zdarzeń do centrum zdarzeń i odbierania ich z niego. Aby uzyskać instrukcje krok po kroku dotyczące wysyłania zdarzeń do (lub) odbierania zdarzeń z centrum zdarzeń, zobacz samouczki **wysyłania i odbierania zdarzeń** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Przejdź](event-hubs-go-get-started-send.md)
- [C (tylko wysyłanie)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko odbieranie)](event-hubs-storm-getstarted-receive.md)


[Utwórz bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
