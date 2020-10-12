---
author: baanders
description: plik dołączany do weryfikowania przypisywania ról w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864733"
---
Jednym ze sposobów sprawdzenia, czy przypisanie roli zostało pomyślnie skonfigurowane, jest wyświetlenie przypisań ról dla wystąpienia usługi bliźniaczych reprezentacji Digital w [Azure Portal](https://portal.azure.com). Przejdź do wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal (możesz to sprawdzić na stronie [wystąpień usługi Azure Digital bliźniaczych reprezentacji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) lub przeszukać jego nazwę na pasku wyszukiwania portalu).

Następnie Wyświetl wszystkie przypisane role w obszarze *Kontrola dostępu (IAM) > przypisań ról*. Użytkownik powinien zostać wyświetlony na liście z rolą *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Widok przypisań roli dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal":::