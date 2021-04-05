---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760669"
---
Ten artykuł zawiera omówienie różnych typów błędów i zaleceń dotyczących obsługi typowych błędów logowania.

## <a name="msal-error-handling-basics"></a>Podstawowe informacje dotyczące obsługi błędów MSAL

Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone dla deweloperów aplikacji do rozwiązywania problemów, a nie do wyświetlania użytkownikom końcowym. Komunikaty o wyjątkach nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów można użyć samego typu wyjątku i kodu błędu, aby rozróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [Kod błędu uwierzytelniania i autoryzacji w usłudze Azure AD](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Podczas logowania mogą wystąpić błędy dotyczące zaleceń, dostęp warunkowy (MFA, zarządzanie urządzeniami, ograniczenia lokalizacji), wystawianie tokenów i wykup oraz właściwości użytkownika.

Poniższa sekcja zawiera więcej informacji na temat obsługi błędów aplikacji.