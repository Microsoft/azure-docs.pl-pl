---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760652"
---
## <a name="conditional-access-and-claims-challenges"></a>Wyzwania dotyczące dostępu warunkowego i oświadczeń

W przypadku odzyskania tokenów w trybie dyskretnym aplikacja może otrzymywać błędy w przypadku, gdy interfejs API, do którego próbujesz uzyskać dostęp, wymaga [żądania oświadczeń dostępu warunkowego](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) , takiego jak zasady MFA.

Wzorzec obsługi tego błędu polega na interaktywnej pozyskaniu tokenu przy użyciu MSAL. Zostanie wyświetlony komunikat z prośbą o to, aby użytkownik mógł spełnić wymagane zasady dostępu warunkowego.

W niektórych przypadkach podczas wywoływania interfejsu API wymagającego dostępu warunkowego można odebrać wyzwanie oświadczeń w błędzie z interfejsu API. Na przykład jeśli zasady dostępu warunkowego mają mieć urządzenie zarządzane (Intune), błąd będzie wyglądać podobnie do [AADSTS53000: urządzenie musi być zarządzane, aby można było uzyskać dostęp do tego zasobu](../articles/active-directory/develop/reference-aadsts-error-codes.md) lub czegoś podobnego. W takim przypadku można przekazać oświadczenia w wywołaniu tokenu pozyskiwania, aby użytkownik był monitowany o spełnienie odpowiednich zasad.
