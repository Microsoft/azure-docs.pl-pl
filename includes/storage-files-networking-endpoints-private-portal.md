---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376122"
---
Przejdź do konta magazynu, dla którego chcesz utworzyć prywatny punkt końcowy. W spisie treści konta magazynu wybierz pozycję **połączenia prywatne punktu końcowego**, a następnie **+ prywatny punkt końcowy** , aby utworzyć nowy prywatny punkt końcowy. 

[![Zrzut ekranu przedstawiający element połączenia prywatnego punktu końcowego w spisie treści konta magazynu](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Wynikiem pracy kreatora jest wiele stron.

W bloku **podstawowe** wybierz żądaną grupę zasobów, nazwę i region dla prywatnego punktu końcowego. Mogą one być odpowiednie, ale nie muszą być zgodne z kontem magazynu w dowolny sposób, chociaż należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się sieć wirtualna, w której ma zostać utworzony prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję podstawowe w sekcji Tworzenie prywatnego punktu końcowego](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

W bloku **zasób** wybierz przycisk radiowy, aby **nawiązać połączenie z zasobem platformy Azure w moim katalogu**. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. Storage/storageAccounts** dla typu zasobu. Pole **zasobu** to konto magazynu z udziałem plików platformy Azure, z którym chcesz nawiązać połączenie. Docelowy zasób podrzędny to **plik**, ponieważ jest on przeznaczony dla Azure Files.

Blok **Konfiguracja** umożliwia wybranie określonej sieci wirtualnej i podsieci, do której chcesz dodać prywatny punkt końcowy. Należy wybrać odrębną podsieć z podsieci, do której dodano punkt końcowy usługi. Blok konfiguracja zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS. Zalecamy korzystanie z strefy domyślnej `privatelink.file.core.windows.net` .

![Zrzut ekranu przedstawiający sekcję konfiguracyjną](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć prywatny punkt końcowy. 
