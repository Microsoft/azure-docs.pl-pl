---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: eb55d993ad8960f821c2b72f0a53602166b7cc7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "69900608"
---
W przypadku danych magazynowanych:

- Dostęp do danych przechowywanych w udziałach jest ograniczony.

    - Klienci protokołu SMB, którzy uzyskują dostęp do danych udostępniania, potrzebują poświadczeń użytkownika skojarzonych z udziałem. Te poświadczenia są definiowane podczas tworzenia udziału.
    - Po utworzeniu udziału należy dodać adresy IP klientów systemu plików NFS, którzy uzyskują dostęp do udziału.
