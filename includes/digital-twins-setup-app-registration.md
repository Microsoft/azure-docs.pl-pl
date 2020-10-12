---
author: baanders
description: Dołączanie pliku dla kroku uprawnień dostępu w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009669"
---
Po skonfigurowaniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji często można korzystać z tego wystąpienia za pomocą aplikacji klienckiej. Aby można było utworzyć działającą aplikację kliencką, należy upewnić się, że aplikacja kliencka będzie mogła się uwierzytelniać w usłudze Azure Digital bliźniaczych reprezentacji. W tym celu należy skonfigurować **rejestrację aplikacji** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) do użycia w aplikacji klienckiej.

Ta rejestracja aplikacji to miejsce, w którym można skonfigurować uprawnienia dostępu do [interfejsów API Digital bliźniaczych reprezentacji platformy Azure](../articles/digital-twins/how-to-use-apis-sdks.md). Później aplikacja kliencka będzie uwierzytelniana względem rejestracji aplikacji i w wyniku przyznania skonfigurowanych uprawnień dostępu do interfejsów API.

>[!TIP]
> Jako właściciel/administrator subskrypcji możesz skonfigurować nową rejestrację aplikacji dla każdego nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji *lub* zrobić to tylko raz, ustanawiając rejestrację pojedynczej aplikacji, która będzie współużytkowana przez wszystkie wystąpienia usługi Azure Digital bliźniaczych reprezentacji w ramach subskrypcji.

### <a name="create-the-registration"></a>Utwórz rejestrację