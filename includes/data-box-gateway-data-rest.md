---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 8e2bd10081bb344fde239e92bd834b31062efde6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96583075"
---
W przypadku danych magazynowanych:

- Dostęp do danych przechowywanych w udziałach jest ograniczony.

    - Klienci protokołu SMB, którzy uzyskują dostęp do danych udostępniania, potrzebują poświadczeń użytkownika skojarzonych z udziałem. Te poświadczenia są definiowane podczas tworzenia udziału.
    - Po utworzeniu udziału należy dodać adresy IP klientów systemu plików NFS, którzy uzyskują dostęp do udziału.
