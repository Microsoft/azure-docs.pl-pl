---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86027339"
---
## <a name="protect-your-access-keys"></a>Ochrona kluczy dostępu

Klucze dostępu do konta magazynu są podobne do hasła głównego dla konta magazynu. Zawsze należy zachować ostrożność w ochronie kluczy dostępu. Użyj Azure Key Vault, aby bezpiecznie zarządzać kluczami i obrócić je. Należy unikać dystrybuowania kluczy dostępu do innych użytkowników, ich kodowania lub zapisywania w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych osób. Obróć klucze, jeśli uważasz, że zostały naruszone.

> [!NOTE]
> Firma Microsoft zaleca używanie Azure Active Directory (Azure AD) do autoryzowania żądań względem danych obiektów blob i kolejek, jeśli jest to możliwe, zamiast klucza współużytkowanego. Usługa Azure AD zapewnia znakomite zabezpieczenia i łatwość użycia w porównaniu z kluczami udostępnionymi. Aby uzyskać więcej informacji na temat autoryzowania dostępu do danych za pomocą usługi Azure AD, zobacz temat [autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
