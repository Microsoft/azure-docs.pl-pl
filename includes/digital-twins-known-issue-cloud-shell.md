---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — poznanie znanego problemu z uwierzytelnianiem Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325162"
---
>[!NOTE]
>Obecnie występuje **znany problem** w Cloud Shell wpływu na te grupy poleceń w przypadku uruchamiania z *https://shell.azure.com* : `az dt route` , `az dt model` , `az dt twin` .
>
>Aby rozwiązać ten problem, można wykonać dowolną z następujących czynności:
> * Uruchom program `az login` w Cloud Shell przed uruchomieniem polecenia.
> * Otwórz okienko Cloud Shell w Azure Portal i Ukończ Cloud Shell z niego pracę.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Widok Azure Portal z wyróżnioną ikoną &quot;Cloud Shell&quot; i Cloud Shell pojawia się u dołu okna portalu":::
> * Użyj [lokalnego interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) zamiast Cloud Shell.
>
>Więcej szczegółów dotyczących tego problemu można znaleźć [*w temacie Rozwiązywanie problemów: znane problemy w usłudze Azure Digital bliźniaczych reprezentacji*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).