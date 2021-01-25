---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760694"
---
## <a name="retrying-after-errors-and-exceptions"></a>Ponawianie próby po wystąpieniu błędów i wyjątkach

Podczas wywoływania MSAL należy wdrożyć własne zasady ponawiania. MSAL nawiązuje wywołania protokołu HTTP do usługi Azure AD, a czasami mogą wystąpić błędy. Na przykład można przejść do sieci lub serwer jest przeciążony.  

### <a name="http-429"></a>HTTP 429

Gdy serwer tokenów usług jest przeciążony za dużo żądań, zwraca błąd HTTP 429 z wskazówką dotyczącą tego, jak długo można spróbować ponownie w `Retry-After` polu odpowiedzi.