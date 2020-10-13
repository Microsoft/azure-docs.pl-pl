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
ms.custom: cli,portal
ms.openlocfilehash: 8924b77cddc9efc4d2b1b8451df38de77b37c09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267364"
---
# <a name="find-your-azure-subscription"></a>Znajdowanie subskrypcji platformy Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W obszarze nagłówek usług platformy Azure wybierz pozycję subskrypcje. (Jeśli na liście nie ma żadnych subskrypcji, może być konieczne przełączenie dzierżawców usługi Azure AD). Identyfikatory subskrypcji są wymienione w drugiej kolumnie.
1. Skopiuj identyfikator subskrypcji i wklej go do wybranego dokumentu tekstowego do użycia później.

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Wyświetlanie listy subskrypcji platformy Azure za pomocą interfejsu wiersza polecenia

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Zobacz też

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
