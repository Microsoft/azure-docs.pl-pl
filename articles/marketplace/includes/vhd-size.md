---
title: Plik dyrektywy include
description: " — plik"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284816"
---
W przypadku wybrania jednej z wstępnie skonfigurowanych maszyn wirtualnych z systemem operacyjnym (i opcjonalnymi dodatkowymi usługami) masz już pobrany standardowy rozmiar maszyny wirtualnej platformy Azure. Zalecanym podejściem jest uruchomienie rozwiązania ze wstępnie skonfigurowanym systemem operacyjnym. Jeśli jednak instalujesz system operacyjny ręcznie, musisz zmienić rozmiar podstawowego wirtualnego dysku twardego w obrazie maszyny wirtualnej. Upewnij się, że rozmiar dysku systemu operacyjnego mieści się w granicach dla systemu Linux lub Windows.

| System operacyjny | Rozmiar dysku VHD |
| --- | --- |
| Linux | od 30 do 1023 GB |
| Windows | od 30 do 250 GB |
|

Podstawowe obrazy systemu Windows lub SQL Server dostarczone jako zatwierdzone podstawowe już spełniają te wymagania, dlatego nie zmieniaj formatu ani rozmiaru dysku VHD.

Dyski danych mogą mieć rozmiar maksymalnie 1 TB. Przy wyborze rozmiaru należy pamiętać, że klienci nie mogą zmieniać rozmiarów wirtualnych dysków twardych w ramach obrazu w czasie wdrażania. Utwórz dyski VHD z danymi jako dyski VHD o stałym formacie. Powinny również być rozwijane (rozrzedzony/dynamiczny). Dyski danych mogą początkowo być puste lub zawierać dane.