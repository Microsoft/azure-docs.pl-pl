---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — subskrybowanie do subskrypcji platformy Azure | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt interfejsu wiersza polecenia platformy Azure, który pokazuje, jak subskrybować zdarzenia Azure Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 9d40367c8d0f4f1250504c30fcd6c4846921a830
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76720898"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Subskrybowanie do zdarzeń dla subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z subskrypcją platformy Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Przykładowy skrypt wersji zapoznawczej wymaga rozszerzenia usługi Event Grid. Aby je zainstalować, uruchom polecenie `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Przykładowy skrypt — stabilny

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Przykładowy skrypt — rozszerzenie w wersji zapoznawczej

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Tworzy subskrypcję usługi Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) — wersja rozszerzenia | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Informacje na temat wykonywania zapytań dotyczących subskrypcji można znaleźć w artykule [Query Event Grid subscriptions (Wykonywanie zapytań dotyczących subskrypcji usługi Event Grid)](../query-event-subscriptions.md).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
