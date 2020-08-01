---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — subskrybowanie do grupy zasobów | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt interfejsu wiersza polecenia platformy Azure, który pokazuje, jak subskrybować zdarzenia Azure Event Grid dla grupy zasobów.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 4282c9d92692e1772a0555bda098e9b59b5f245b
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460851"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Subskrybowanie zdarzeń dla grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z grupą zasobów. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Przykładowy skrypt wersji zapoznawczej wymaga rozszerzenia usługi Event Grid. Aby je zainstalować, uruchom polecenie `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Przykładowy skrypt — stabilny

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Przykładowy skrypt — rozszerzenie w wersji zapoznawczej

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Tworzy subskrypcję usługi Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) — wersja rozszerzenia | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Informacje na temat wykonywania zapytań dotyczących subskrypcji można znaleźć w artykule [Query Event Grid subscriptions (Wykonywanie zapytań dotyczących subskrypcji usługi Event Grid)](../query-event-subscriptions.md).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
