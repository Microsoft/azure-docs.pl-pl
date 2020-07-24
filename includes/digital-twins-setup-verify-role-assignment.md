---
author: baanders
description: plik dołączany do weryfikowania przypisywania ról w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098105"
---
Jednym ze sposobów sprawdzenia, czy przypisanie roli zostało pomyślnie skonfigurowane, jest wyświetlenie przypisań ról dla wystąpienia usługi bliźniaczych reprezentacji Digital w Azure Portal. Na stronie portalu [wystąpień usługi Azure Digital bliźniaczych reprezentacji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)wybierz nazwę wystąpienia, które chcesz sprawdzić. Następnie Wyświetl wszystkie przypisane role w obszarze *Kontrola dostępu (IAM) > przypisań ról*. Użytkownik powinien zostać wyświetlony na liście z rolą *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="Widok przypisań roli dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal":::