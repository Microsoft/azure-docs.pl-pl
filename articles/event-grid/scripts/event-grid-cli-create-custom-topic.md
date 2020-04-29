---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie tematu niestandardowego | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt interfejsu wiersza polecenia platformy Azure, który pokazuje, jak utworzyć niestandardowy temat Azure Event Grid.
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
ms.openlocfilehash: 2c6e027d234104d5e45c632dd73e79d8f069deb5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76720847"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Tworzenie tematu niestandardowego usługi Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy temat niestandardowy usługi Event Grid.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia tematu niestandardowego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Tworzy niestandardowy temat usługi Event Grid. |


## <a name="next-steps"></a>Następne kroki

* Informacje na temat wykonywania zapytań dotyczących subskrypcji można znaleźć w artykule [Query Event Grid subscriptions (Wykonywanie zapytań dotyczących subskrypcji usługi Event Grid)](../query-event-subscriptions.md).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
