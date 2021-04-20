---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732613"
---
Przejdź do konta magazynu, dla którego chcesz utworzyć prywatny punkt końcowy. W spisie treści konta magazynu wybierz pozycję **Sieć,** Połączenia z prywatnym punktem **końcowym,** a następnie pozycję + Prywatny punkt **końcowy,** aby utworzyć nowy prywatny punkt końcowy. 

[![Zrzut ekranu przedstawiający element połączeń z prywatnym punktem końcowym w spisie treści konta magazynu](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Wynikowy kreator ma wiele stron do ukończenia.

W bloku **Podstawowe wybierz** żądaną grupę zasobów, nazwę i region prywatnego punktu końcowego. Mogą to być dowolne dane. Nie muszą one w żaden sposób odpowiadać kontu magazynu, chociaż musisz utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się sieć wirtualna, w której chcesz utworzyć prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję Podstawowe w sekcji Tworzenie prywatnego punktu końcowego](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

W bloku **Zasób** wybierz przycisk radiowy Połącz z **zasobem platformy Azure w katalogu**. W **obszarze Typ** zasobu wybierz pozycję **Microsoft.Storage/storageAccounts** jako typ zasobu. Pole **Zasób** to konto magazynu z udziałem plików platformy Azure, z którą chcesz nawiązać połączenie. Docelowy zasób podrzędny **to plik**, ponieważ dotyczy Azure Files.

Blok **Konfiguracja** umożliwia wybranie określonej sieci wirtualnej i podsieci, do których chcesz dodać prywatny punkt końcowy. Musisz wybrać odrębną podsieć z podsieci, do których dodano punkt końcowy usługi. Blok Konfiguracja zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS. Zalecamy używanie strefy `privatelink.file.core.windows.net` domyślnej.

![Zrzut ekranu przedstawiający sekcję Konfiguracja](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Kliknij **pozycję Przejrzyj i utwórz,** aby utworzyć prywatny punkt końcowy. 
