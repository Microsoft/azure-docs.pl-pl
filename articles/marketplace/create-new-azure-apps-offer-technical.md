---
title: Jak dodać szczegóły techniczne dla oferty aplikacji platformy Azure
description: Dowiedz się, jak dodać szczegóły techniczne dla oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370235"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Jak dodać szczegóły techniczne dla oferty aplikacji platformy Azure

W tym artykule opisano, jak wprowadzać szczegóły techniczne, które ułatwiają firmie Microsoft komercyjne nawiązywanie połączenia z rozwiązaniem. To połączenie umożliwia nam zainicjowanie oferty dla klienta, jeśli zdecydują się ją nabyć i zarządzać.

Wypełnij tę sekcję tylko wtedy, gdy oferta obejmuje zarządzaną aplikację, która będzie emitować zdarzenia pomiaru przy użyciu [domierzonych interfejsów API rozliczeń w witrynie Marketplace](partner-center-portal/marketplace-metering-service-apis.md) i ma usługę, która będzie uwierzytelniana przy użyciu tokenu zabezpieczającego usługi Azure AD. Aby uzyskać więcej informacji, zobacz [strategie uwierzytelniania usługi w portalu Marketplace](partner-center-portal/marketplace-metering-service-authentication.md) dotyczące różnych opcji uwierzytelniania.

## <a name="technical-configuration-offer-level"></a>Konfiguracja techniczna (poziom oferty)

Karta **konfiguracja techniczna** ma zastosowanie tylko wtedy, gdy utworzysz zarządzaną aplikację, która emituje zdarzenia pomiarowe za pomocą [interfejsów API rozliczeń w portalu Marketplace](partner-center-portal/marketplace-metering-service-apis.md). W takim przypadku wykonaj następujące czynności. W przeciwnym razie przejdź do [sekcji Następne kroki](#next-steps). 

Aby uzyskać więcej informacji na temat tych pól, zobacz [Planowanie oferty aplikacji platformy Azure dla komercyjnej witryny Marketplace](plan-azure-application-offer.md#technical-configuration).

1. Na karcie **konfiguracja techniczna** Podaj **Azure Active Directory identyfikator dzierżawy** i **Identyfikator aplikacji Azure Active Directory** używany do sprawdzania poprawności połączenia między naszymi dwiema usługami jest za uwierzytelnianą komunikacją.

1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: przegląd planu.

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć plany dla oferty aplikacji platformy Azure](create-new-azure-apps-offer-plans.md)
