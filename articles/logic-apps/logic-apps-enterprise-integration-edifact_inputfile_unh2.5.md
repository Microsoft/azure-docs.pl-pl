---
title: Segmenty UNH 2,5 w komunikatach EDIFACT
description: Rozpoznawanie komunikatów EDIFACT z segmentami UNH 2.5 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c6d95cb938a341b94a32989e82c42d37956c0a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179848"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Obsługa dokumentów EDIFACT z segmentami UNH 2.5 w Azure Logic Apps

Jeśli w dokumencie EDIFACT istnieje segment UNH 2.5, segment jest używany do wyszukiwania w schemacie. Na przykład w tym przykładowym komunikacie EDIFACT pole UNH jest `EAN008` następujące:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Aby obsłużyć ten komunikat, wykonaj następujące kroki opisane poniżej:

1. Zaktualizuj schemat.

1. Sprawdź ustawienia umowy.

## <a name="update-the-schema"></a>Aktualizowanie schematu

Aby przetworzyć komunikat, należy wdrożyć schemat, który ma nazwę węzła głównego UNH 2.5. Na przykład główna nazwa schematu dla przykładowego pola UNH `EFACT_D03B_ORDERS_EAN008` . Dla każdego, `D03B_ORDERS` który ma inny segment UNH 2,5, należy wdrożyć pojedynczy schemat.

## <a name="add-schema-to-edifact-agreement"></a>Dodaj schemat do umowy EDIFACT

### <a name="edifact-decode"></a>Dekodowanie EDIFACT

Aby zdekodować komunikat przychodzący, skonfiguruj schemat w ustawieniach odbioru umowy EDIFACT:

1. W [Azure Portal](https://portal.azure.com)Otwórz konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach odbioru umowy EDIFACT.

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako kod JSON**. Dodaj wartość UNH 2,5 do sekcji umowy odbioru `schemaReferences` :

   ![Dodaj UNH 2.5, aby uzyskać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kodowanie EDIFACT

Aby zakodować komunikat przychodzący, skonfiguruj schemat w ustawieniach umowy EDIFACT

1. W [Azure Portal](https://portal.azure.com)Otwórz konto integracji.

1. Dodaj schemat do konta integracji.

1. Skonfiguruj schemat w ustawieniach wysyłania umowy EDIFACT.

1. Wybierz umowę EDIFACT i wybierz pozycję **Edytuj jako kod JSON**. Dodaj wartość UNH 2,5 do sekcji z umową wysyłania `schemaReferences` :

   ![Dodaj UNH 2.5, aby wysłać umowę](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [umowach dotyczących kont integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)
