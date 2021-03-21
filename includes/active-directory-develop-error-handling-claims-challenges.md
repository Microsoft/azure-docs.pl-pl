---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760652"
---
## <a name="conditional-access-and-claims-challenges"></a>Wyzwania dotyczące dostępu warunkowego i oświadczeń

W przypadku odzyskania tokenów w trybie dyskretnym aplikacja może otrzymywać błędy w przypadku, gdy interfejs API, do którego próbujesz uzyskać dostęp, wymaga [żądania oświadczeń dostępu warunkowego](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) , takiego jak zasady MFA.

Wzorzec obsługi tego błędu polega na interaktywnej pozyskaniu tokenu przy użyciu MSAL. Zostanie wyświetlony komunikat z prośbą o to, aby użytkownik mógł spełnić wymagane zasady dostępu warunkowego.

W niektórych przypadkach podczas wywoływania interfejsu API wymagającego dostępu warunkowego można odebrać wyzwanie oświadczeń w błędzie z interfejsu API. Na przykład jeśli zasady dostępu warunkowego mają mieć urządzenie zarządzane (Intune), błąd będzie wyglądać podobnie do [AADSTS53000: urządzenie musi być zarządzane, aby można było uzyskać dostęp do tego zasobu](../articles/active-directory/develop/reference-aadsts-error-codes.md) lub czegoś podobnego. W takim przypadku można przekazać oświadczenia w wywołaniu tokenu pozyskiwania, aby użytkownik był monitowany o spełnienie odpowiednich zasad.
