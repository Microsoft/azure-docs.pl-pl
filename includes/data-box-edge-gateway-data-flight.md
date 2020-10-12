---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183868"
---
W przypadku danych w locie:

- Standard TLS 1,2 służy do przesyłania danych między urządzeniem i platformą Azure. Nie istnieje powrót do protokołu TLS 1,1 i jego wcześniejszych wersji. Komunikacja agentów zostanie zablokowana, jeśli protokół TLS 1,2 nie jest obsługiwany. Protokół TLS 1,2 jest również wymagany w przypadku zarządzania portalem i zestawem SDK.
- Gdy klienci uzyskują dostęp do urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web w przeglądarce, jako domyślny protokół TLS jest używany standard Secure Protocol 1,2.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do korzystania z protokołu TLS 1,2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1,2, można użyć protokołu TLS 1,1 lub TLS 1,0.
- Zalecamy użycie protokołu SMB 3,0 z szyfrowaniem w celu ochrony danych podczas kopiowania ich z serwerów danych.
