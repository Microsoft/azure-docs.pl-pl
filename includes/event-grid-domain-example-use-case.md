---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198664"
---
Domeny zdarzeń można łatwo wyjaśnić przy użyciu przykładu. Załóżmy, że uruchomiono maszyny do budowy firmy Contoso, w której są wytwarzane ciągniki, urządzenia przeszukiwanie stosów oraz inne duże maszyny. W ramach działania firmy użytkownik wysyła informacje w czasie rzeczywistym do klientów dotyczących konserwacji sprzętu, kondycji systemów i aktualizacji umów. Wszystkie te informacje znajdują się w różnych punktach końcowych, w tym aplikacji, punktach końcowych klienta i w innej infrastrukturze, którą skonfigurowali klienci.

Domeny zdarzeń umożliwiają modelowanie maszyn konstrukcyjnych contoso jako pojedynczej jednostki zdarzeń. Każdy z klientów jest reprezentowany jako temat w ramach domeny. Uwierzytelnianie i autoryzacja są obsługiwane za pomocą Azure Active Directory. Każdy z klientów może subskrybować swój temat i uzyskać do nich swoje zdarzenia. Dostęp zarządzany przez domenę zdarzeń zapewnia dostęp tylko do ich tematu.

Zapewnia również pojedynczy punkt końcowy, w którym można opublikować wszystkie zdarzenia klienta w usłudze. Event Grid zajmie się zapewnieniem, że każdy temat wie o zdarzeniach objętych zakresem jego dzierżawy.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Przykład konstruowania contoso" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::