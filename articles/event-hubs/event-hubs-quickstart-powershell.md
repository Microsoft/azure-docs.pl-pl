---
title: 'Szybki Start: tworzenie centrum zdarzeń przy użyciu programu PowerShell — Event Hubs platformy Azure'
description: Ten przewodnik Szybki start przedstawia tworzenie centrum zdarzeń za pomocą programu Azure PowerShell oraz wysyłanie i odbieranie zdarzeń za pomocą zestawu .NET Standard SDK.
services: event-hubs
author: spelluru
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 89ec1957e75fa45eef6c7939a77e5cc1b3cf7806
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162127"
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

Jeśli używasz programu PowerShell lokalnie, musisz uruchomić najnowszą wersję programu PowerShell, aby ukończyć ten przewodnik Szybki start. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie i konfigurowanie Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Przejdź](event-hubs-go-get-started-send.md)
- [C (tylko wysyłanie)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko odbieranie)](event-hubs-storm-getstarted-receive.md)


[Utwórz bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
