---
author: baanders
description: plik dołączany do weryfikowania przypisywania ról w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92489046"
---
Jednym ze sposobów sprawdzenia, czy przypisanie roli zostało pomyślnie skonfigurowane, jest wyświetlenie przypisań ról dla wystąpienia usługi bliźniaczych reprezentacji Digital w [Azure Portal](https://portal.azure.com). Przejdź do wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal (możesz to sprawdzić na stronie [wystąpień usługi Azure Digital bliźniaczych reprezentacji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) lub przeszukać jego nazwę na pasku wyszukiwania portalu).

Następnie Wyświetl wszystkie przypisane role w obszarze *Kontrola dostępu (IAM) > przypisań ról*. Użytkownik powinien zostać wyświetlony na liście z rolą *właściciela danych cyfrowych bliźniaczych reprezentacji systemu Azure*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Widok przypisań roli dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal":::