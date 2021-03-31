---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118071"
---
## <a name="about-the-user-delegation-sas"></a>Informacje o obszarze SAS delegowania użytkownika

Token sygnatury dostępu współdzielonego, który umożliwia dostęp do kontenera lub obiektu BLOB, może być zabezpieczony przy użyciu poświadczeń usługi Azure AD lub klucza konta. Sygnatura dostępu współdzielonego zabezpieczony przy użyciu poświadczeń usługi Azure AD nazywa się sygnaturą dostępu współdzielonego użytkownika, ponieważ token OAuth 2,0 używany do podpisywania sygnatury dostępu współdzielonego jest wymagany w imieniu użytkownika.

Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie zabezpieczeń zamiast używania klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego, Użyj poświadczeń usługi Azure AD, aby utworzyć SYGNATURę czasową delegowania użytkowników w celu zapewnienia bezpieczeństwa wyższego poziomu. Aby uzyskać więcej informacji na temat delegowania SKOJARZEŃ użytkowników, zobacz [Tworzenie skojarzeń zabezpieczeń delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Każdy klient, który dysponuje prawidłowym sygnaturą dostępu współdzielonego, może uzyskać dostęp do danych na koncie magazynu, zgodnie z dozwolonym przez ten SAS. Ważne jest, aby chronić sygnaturę dostępu współdzielonego przed złośliwym lub niezamierzonym użyciem. Używaj uznania w dystrybucji sygnatury dostępu współdzielonego i masz plan do odwoływania złamanej sygnatury dostępu współdzielonego.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../articles/storage/common/storage-sas-overview.md).
