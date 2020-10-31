---
author: baanders
description: Dołącz plik do usługi Azure Digital bliźniaczych reprezentacji — wymaganie wstępne, aby skonfigurować rejestrację aplikacji
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124854"
---
Aby uwierzytelnić wszystkie zasoby używane w tym artykule, należy skonfigurować **rejestrację aplikacji** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) . Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Create a App Registration*](../articles/digital-twins/how-to-create-app-registration.md) to set up. 

Po zarejestrowaniu aplikacji będziesz potrzebować identyfikatora **_aplikacji (klienta)_** rejestracji i **_identyfikatora (dzierżawy)_** ( [Znajdź w Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Zwróć uwagę na te wartości, aby użyć ich później do udzielenia dostępu do interfejsów API Digital bliźniaczych reprezentacji platformy Azure.