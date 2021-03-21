---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583051"
---
W przypadku danych w locie:

- Standard TLS 1,2 służy do przesyłania danych między urządzeniem i platformą Azure. Nie istnieje powrót do protokołu TLS 1,1 i jego wcześniejszych wersji. Komunikacja agentów zostanie zablokowana, jeśli protokół TLS 1,2 nie jest obsługiwany. Protokół TLS 1,2 jest również wymagany w przypadku zarządzania portalem i zestawem SDK.
- Gdy klienci uzyskują dostęp do urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web w przeglądarce, jako domyślny protokół TLS jest używany standard Secure Protocol 1,2.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do korzystania z protokołu TLS 1,2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1,2, można użyć protokołu TLS 1,1 lub TLS 1,0.
- Zalecamy użycie protokołu SMB 3,0 z szyfrowaniem w celu ochrony danych podczas kopiowania ich z serwerów danych.
