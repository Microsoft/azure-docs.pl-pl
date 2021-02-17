---
title: Zarządzanie kontenerami woluminów dla urządzeń z serii StorSimple 8000
description: Wyjaśnia, jak można użyć strony kontenery woluminów usługi StorSimple Device Manager do dodawania, modyfikowania lub usuwania kontenera woluminów.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545397"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Use the StorSimple Device Manager service to manage StorSimple volume containers (Zarządzanie kontenerami woluminów StorSimple za pomocą usługi Menedżer urządzeń StorSimple)

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak używać usługi StorSimple Device Manager do tworzenia kontenerów woluminów StorSimple i zarządzania nimi.

Kontener woluminów na urządzeniu Microsoft Azure StorSimple zawiera jeden lub więcej woluminów, które współużytkują ustawienia kont magazynu, szyfrowania i zużycia przepustowości. Urządzenie może mieć wiele kontenerów woluminów dla wszystkich woluminów. 

Kontener woluminów ma następujące atrybuty:

* **Woluminy** — woluminy w warstwach lub lokalnie przypięte StorSimple, które znajdują się w kontenerze woluminów. 
* **Szyfrowanie** — klucz szyfrowania, który można zdefiniować dla każdego kontenera woluminów. Ten klucz służy do szyfrowania danych wysyłanych z urządzenia StorSimple do chmury. Klucz bitowy w klasie wojskowej AES-256 jest używany z kluczem wprowadzonym przez użytkownika. Aby zabezpieczyć dane, zalecamy włączenie szyfrowania magazynu w chmurze.
* **Konto magazynu** — konto usługi Azure Storage, które jest używane do przechowywania danych. Wszystkie woluminy znajdujące się w kontenerze woluminów współużytkują to konto magazynu. Można wybrać konto magazynu z istniejącej listy lub utworzyć nowe konto podczas tworzenia kontenera woluminów, a następnie określić poświadczenia dostępu dla tego konta.
* **Przepustowość chmury** — przepustowość wykorzystywana przez urządzenie, gdy dane z urządzenia są wysyłane do chmury. Można wymusić kontrolę przepustowości, określając wartość z przedziału od 1 MB/s do 1 000 MB/s podczas tworzenia tego kontenera. Jeśli chcesz, aby urządzenie korzystało z całej dostępnej przepustowości, ustaw to pole na **nieograniczoną**. Możesz również utworzyć i zastosować szablon przepustowości, aby przydzielić przepustowość na podstawie harmonogramu.

W poniższych procedurach opisano, jak użyć bloku **kontenery woluminów** StorSimple, aby wykonać następujące typowe operacje:

* Dodawanie kontenera woluminów
* Modyfikowanie kontenera woluminów
* Usuwanie kontenera woluminów

## <a name="add-a-volume-container"></a>Dodawanie kontenera woluminów
Wykonaj następujące kroki, aby dodać kontener woluminów.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modyfikowanie kontenera woluminów
Wykonaj następujące kroki, aby zmodyfikować kontener woluminów.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Usuwanie kontenera woluminów
Kontener woluminów zawiera woluminy. Można ją usunąć tylko wtedy, gdy wszystkie woluminy zawarte w niej zostały najpierw usunięte. Wykonaj następujące kroki, aby usunąć kontener woluminów.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzaniu woluminami StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

