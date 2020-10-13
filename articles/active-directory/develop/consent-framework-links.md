---
title: Jak działa zgoda dla aplikacji
description: Dowiedz się więcej o tym, jak działa struktura zgody usługi Azure AD, aby zobaczyć, jak można jej używać podczas tworzenia aplikacji w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117366"
---
# <a name="how-application-consent-works"></a>Jak działa zgoda dla aplikacji

Ten artykuł ma na celu ułatwienie Dowiedz się więcej o tym, jak działa struktura zgody na usługę Azure AD, dzięki czemu możesz bardziej efektywnie opracowywać aplikacje.

## <a name="recommended-documents"></a>Zalecane dokumenty

- Zapoznaj się z ogólnymi informacjami o tym, [jak zgoda Właściciel zasobu może zarządzać dostępem aplikacji do zasobów](./developer-glossary.md#consent).
- Zapoznaj się z omówieniem krok po kroku [na temat sposobu, w jaki platforma zgody usługi Azure AD implementuje wyrażanie zgody](./quickstart-register-app.md).
- Aby uzyskać większą głębokość, Dowiedz się, w [jaki sposób aplikacja wielodostępna może używać struktury zgody](./howto-convert-app-to-be-multi-tenant.md) do implementowania zgody "User" i "admin", co pozwala na bardziej zaawansowane wzorce aplikacji wielowarstwowej.
- Aby uzyskać więcej informacji, Dowiedz się, [jak zgoda jest obsługiwana w warstwie protokołu OAuth 2,0 podczas przepływu przydzielenia kodu autoryzacji.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Następne kroki
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)