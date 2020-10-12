---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — poznanie znanego problemu z uwierzytelnianiem Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290105"
---
>[!NOTE]
>Obecnie występuje **znany problem** w Cloud Shell wpływu na te grupy poleceń: `az dt route` , `az dt model` , `az dt twin` .
>
>Aby rozwiązać ten problem, należy uruchomić `az login` polecenie w Cloud Shell przed uruchomieniem polecenia lub użyć [lokalnego interfejsu wiersza](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) polecenia, a nie Cloud Shell. Aby uzyskać szczegółowe informacje na ten temat, zobacz [*Rozwiązywanie problemów: znane problemy w usłudze Azure Digital bliźniaczych reprezentacji*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).