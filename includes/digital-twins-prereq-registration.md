---
author: baanders
description: Dołącz plik do usługi Azure Digital bliźniaczych reprezentacji — wymaganie wstępne, aby skonfigurować rejestrację aplikacji
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96303592"
---
Aby uwierzytelnić wszystkie zasoby używane w tym artykule, należy **skonfigurować rejestrację aplikacji [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md)**. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Create a App Registration*](../articles/digital-twins/how-to-create-app-registration.md) to set up. 

Po zarejestrowaniu aplikacji będziesz potrzebować identyfikatora **_aplikacji (klienta)_** rejestracji i **_identyfikatora (dzierżawy)_** ([Znajdź w Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Zwróć uwagę na te wartości, aby użyć ich później do udzielenia dostępu do interfejsów API Digital bliźniaczych reprezentacji platformy Azure.