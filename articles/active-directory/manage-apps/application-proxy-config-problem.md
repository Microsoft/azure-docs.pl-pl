---
title: Wystąpił problem podczas tworzenia aplikacji serwera proxy aplikacji | Microsoft Docs
description: Jak rozwiązywać problemy podczas tworzenia aplikacji serwera proxy aplikacji w portalu administracyjnym usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "65825817"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem z tworzeniem aplikacji serwera proxy aplikacji 

Poniżej przedstawiono niektóre często spotykane problemy podczas tworzenia nowej aplikacji serwera proxy aplikacji.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby dowiedzieć się więcej na temat tworzenia aplikacji serwera proxy aplikacji za pomocą portalu administracyjnego, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

Jeśli używasz kroków z tego dokumentu i wystąpi błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu naprawy aplikacji. Większość komunikatów o błędach zawiera sugerowaną poprawkę. 

## <a name="specific-things-to-check"></a>Konkretne elementy do sprawdzenia

Aby uniknąć typowych błędów, należy sprawdzić:

-   Jesteś administratorem z uprawnieniami do tworzenia aplikacji serwera proxy aplikacji

-   Wewnętrzny adres URL jest unikatowy

-   Zewnętrzny adres URL jest unikatowy

-   Adresy URL zaczynają się od http lub https i kończą się znakiem "/"

-   Adres URL powinien być nazwą domeny, a nie adresem IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Monit powiadamiania](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Następne kroki
[Włączanie serwera proxy aplikacji w witrynie Azure Portal](application-proxy-add-on-premises-application.md)
