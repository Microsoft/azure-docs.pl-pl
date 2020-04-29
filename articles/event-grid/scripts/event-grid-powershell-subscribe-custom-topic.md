---
title: Przykładowy skrypt programu Azure PowerShell — subskrybowanie do tematu niestandardowego | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt Azure PowerShell, który pokazuje, jak subskrybować zdarzenia Event Grid dla tematu niestandardowego.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 1c3b158a2608d63fae6eea38c7263d0614519a5e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76721527"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Subskrybowanie do zdarzeń dla tematu niestandardowego przy użyciu programu PowerShell

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z tematem niestandardowym.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Przykładowy skrypt wersji zapoznawczej wymaga modułu usługi Event Grid. Aby go zainstalować, uruchom polecenie `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Przykładowy skrypt — stabilny

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Przykładowy skrypt — moduł w wersji zapoznawczej

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
