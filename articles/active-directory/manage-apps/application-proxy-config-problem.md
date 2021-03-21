---
title: Wystąpił problem podczas tworzenia aplikacji serwera proxy aplikacji | Microsoft Docs
description: Jak rozwiązywać problemy podczas tworzenia aplikacji serwera proxy aplikacji w portalu administracyjnym usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258018"
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
