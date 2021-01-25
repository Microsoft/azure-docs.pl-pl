---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760669"
---
Ten artykuł zawiera omówienie różnych typów błędów i zaleceń dotyczących obsługi typowych błędów logowania.

## <a name="msal-error-handling-basics"></a>Podstawowe informacje dotyczące obsługi błędów MSAL

Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone dla deweloperów aplikacji do rozwiązywania problemów, a nie do wyświetlania użytkownikom końcowym. Komunikaty o wyjątkach nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów można użyć samego typu wyjątku i kodu błędu, aby rozróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [Kod błędu uwierzytelniania i autoryzacji w usłudze Azure AD](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Podczas logowania mogą wystąpić błędy dotyczące zaleceń, dostęp warunkowy (MFA, zarządzanie urządzeniami, ograniczenia lokalizacji), wystawianie tokenów i wykup oraz właściwości użytkownika.

Poniższa sekcja zawiera więcej informacji na temat obsługi błędów aplikacji.