---
title: Jak znaleźć subskrypcję platformy Azure
description: Znajdź subskrypcję platformy Azure, aby móc skonfigurować swoje środowisko.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: inhenkel
ms.custom: cli,portal
ms.openlocfilehash: fadb290d1bd1126c3bfc0cd76d6e6b862d1396e3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556367"
---
# <a name="find-your-azure-subscription"></a>Znajdź swoją subskrypcję platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W obszarze nagłówek usług platformy Azure wybierz pozycję subskrypcje. (Jeśli na liście nie ma żadnych subskrypcji, może być konieczne przełączenie dzierżawców usługi Azure AD). Identyfikatory subskrypcji są wymienione w drugiej kolumnie.
1. Skopiuj identyfikator subskrypcji i wklej go do wybranego dokumentu tekstowego do użycia później.

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Wyświetlanie listy subskrypcji platformy Azure za pomocą interfejsu wiersza polecenia

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Zobacz też

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
