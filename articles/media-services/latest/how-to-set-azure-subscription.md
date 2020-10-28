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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: 07088ebfa5fde4c3c790bd554c1eba8d6844b7d1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736068"
---
# <a name="find-your-azure-subscription"></a>Znajdowanie subskrypcji platformy Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. W obszarze nagłówek usług platformy Azure wybierz pozycję subskrypcje. (Jeśli na liście nie ma żadnych subskrypcji, może być konieczne przełączenie dzierżawców usługi Azure AD). Identyfikatory subskrypcji są wymienione w drugiej kolumnie.
1. Skopiuj identyfikator subskrypcji i wklej go do wybranego dokumentu tekstowego do użycia później.

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Wyświetlanie listy subskrypcji platformy Azure za pomocą interfejsu wiersza polecenia

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Zobacz także

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
