---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460566"
---
## <a name="protect-your-access-keys"></a>Ochrona kluczy dostępu

Klucze dostępu do konta magazynu są podobne do hasła głównego dla konta magazynu. Zawsze należy zachować ostrożność w ochronie kluczy dostępu. Użyj Azure Key Vault, aby bezpiecznie zarządzać kluczami i obrócić je. Należy unikać dystrybuowania kluczy dostępu do innych użytkowników, ich kodowania lub zapisywania w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych osób. Obróć klucze, jeśli uważasz, że zostały naruszone.

Jeśli to możliwe, użyj Azure Active Directory (Azure AD), aby autoryzować żądania do obiektów blob i magazynu kolejek zamiast klucza współużytkowanego. Usługa Azure AD zapewnia znakomite zabezpieczenia i łatwość użycia w porównaniu z kluczami udostępnionymi. Aby uzyskać więcej informacji na temat autoryzowania dostępu do danych za pomocą usługi Azure AD, zobacz temat [autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
