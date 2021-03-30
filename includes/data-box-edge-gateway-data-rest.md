---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: eb55d993ad8960f821c2b72f0a53602166b7cc7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "69900608"
---
W przypadku danych magazynowanych:

- Dostęp do danych przechowywanych w udziałach jest ograniczony.

    - Klienci protokołu SMB, którzy uzyskują dostęp do danych udostępniania, potrzebują poświadczeń użytkownika skojarzonych z udziałem. Te poświadczenia są definiowane podczas tworzenia udziału.
    - Po utworzeniu udziału należy dodać adresy IP klientów systemu plików NFS, którzy uzyskują dostęp do udziału.
