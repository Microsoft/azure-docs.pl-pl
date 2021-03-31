---
title: Dostępność regionu StorSimple
description: Wyjaśnia regiony platformy Azure, w których są dostępne różne modele urządzeń StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90564155"
---
# <a name="available-regions-for-your-storsimple"></a>Dostępne regiony dla StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Centra danych platformy Azure działają w wielu lokalizacje geograficzneach na całym świecie, aby zaspokoić wymagania dotyczące wydajności, wymagań i preferencji związanych z danymi w przypadku klientów. Lokalizacja geograficzna platformy Azure to zdefiniowany obszar świata, który zawiera co najmniej jeden region świadczenia usługi Azure. Region świadczenia usługi Azure jest obszarem geograficznym zawierającym co najmniej jedno centrum danych.

Wybór regionu platformy Azure jest bardzo ważne, a wybór regionu ma wpływ na takie czynniki jak zamieszkania danych i suwerenność, dostępność usług, wydajność, koszt i nadmiarowość. Aby uzyskać więcej informacji na temat wybierania regionu, przejdź do tego, [który region świadczenia usługi Azure jest odpowiedni dla mnie?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

W przypadku rozwiązania StorSimple wybór regionu zależy od następujących czynników:

- Regiony, w których jest dostępna usługa Device Manager StorSimple.
- Kraje/regiony, w których jest dostępna StorSimple fizyczna, Chmura lub urządzenie wirtualne.
- Regiony, w których należy zlokalizować konta magazynu przechowujące dane StorSimple, aby uzyskać optymalną wydajność.

W tym samouczku opisano dostępność regionu usługi StorSimple Device Manager, lokalne i fizyczne urządzenia w chmurze. Informacje zawarte w tym artykule dotyczą urządzeń z serii StorSimple 8000 i 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostępność regionów dla usługi StorSimple Device Manager

Usługa StorSimple Device Manager jest obecnie obsługiwana w 12 regionach publicznych i 2 Azure Government regionach.

Zdefiniuj region lub lokalizację podczas pierwszego tworzenia usługi StorSimple Device Manager. Ogólnie rzecz biorąc wybierz lokalizację znajdującą się najbliżej regionu geograficznego, w którym wdrożono urządzenie. Ale urządzenie i usługa można również wdrożyć w różnych lokalizacjach.

Poniżej znajduje się lista regionów, w których Usługa StorSimple Device Manager jest dostępna dla chmury publicznej platformy Azure i można ją wdrożyć.

![Zrzut ekranu przedstawia StorSimple Device Manager z wyróżnioną listą lokalizacji.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

W przypadku chmury Azure Government Usługa StorSimple Device Manager jest dostępna w US Gov Iowa i US Gov Wirginia centrach danych.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostępność regionu dla danych przechowywanych w StorSimple

Dane StorSimple są fizycznie przechowywane na kontach usługi Azure Storage, a te konta są dostępne we wszystkich regionach świadczenia usługi Azure. Podczas tworzenia konta usługi Azure Storage jest wybierana podstawowa lokalizacja konta magazynu, która określa region, w którym znajdują się dane.

Podczas pierwszego tworzenia usługi StorSimple Device Manager i kojarzenia z nią konta magazynu, Usługa StorSimple Device Manager i Magazyn Azure mogą znajdować się w dwóch oddzielnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie.

Ogólnie rzecz biorąc, wybierz najbliższy region usługi dla konta magazynu. Jednak najbliższy region Microsoft Azure może nie być regionem o najniższym opóźnieniu. Jest to opóźnienie, które wymusza wydajność usługi sieciowej i w związku z tym wydajność rozwiązania. W przypadku wybrania konta magazynu w innym regionie należy wiedzieć, jakie opóźnienia należy do usługi i regionu skojarzonego z kontem magazynu.

Jeśli używasz urządzenia w chmurze StorSimple, zalecamy, aby usługa i skojarzone konto magazynu znajdują się w tym samym regionie. Konta magazynu w innym regionie mogą powodować niską wydajność.

## <a name="availability-of-storsimple-device"></a>Dostępność urządzenia StorSimple

W zależności od modelu urządzenia StorSimple mogą być dostępne w różnych lokalizacje geograficzne lub krajach/regionach.

### <a name="storsimple-physical-device-models-81008600"></a>Urządzenie fizyczne StorSimple (modele 8100/8600)

W przypadku korzystania z urządzenia fizycznego z systemem StorSimple 8100 lub 8600 urządzenie jest dostępne w następujących krajach/regionach.

| #  | Kraj/region        | #  | Kraj/region     | #  | Kraj/region      | #  | Kraj/region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australia             | 16 | SRA Hongkong      | 31 | Nowa Zelandia         | 46 | Republika Południowej Afryki               |
| 2  | Austria               | 17 | Węgry            | 32 | Nigeria             | 47 | Korea Południowa                |
| 3  | Bahrajn               | 18 | Islandia            | 33 | Norwegia              | 48 | Hiszpania                      |
| 4  | Belgia               | 19 | Indie              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brazylia                | 20 | Indonezja          | 35 | Filipiny         | 50 | Szwecja                     |
| 6  | Kanada                | 21 | Irlandia            | 36 | Polska              | 51 | Szwajcaria                |
| 7  | Chile                 | 22 | Izrael             | 37 | Portugalia            | 52 | Tajwan                     |
| 8  | Kolumbia              | 23 | Włochy              | 38 | Portoryko         | 53 | Tajlandia                   |
| 9  | Republika Czeska        | 24 | Japonia              | 39 | Katar               | 54 | Turcja                     |
| 10 | Dania               | 25 | Kenia              | 40 | Rumunia             | 55 | Ukraina                    |
| 11 | Egipt                 | 26 | Kuwejt             | 41 | Rosja              | 56 | Zjednoczone Emiraty Arabskie       |
| 12 | Finlandia               | 27 | SRA Makau          | 42 | Arabia Saudyjska        | 57 | Zjednoczone Królestwo             |
| 13 | Francja                | 28 | Malezja           | 43 | Singapur           | 58 | Stany Zjednoczone              |
| 14 | Niemcy               | 29 | Meksyk             | 44 | Słowacja            | 59 | Wietnam                    |
| 15 | Grecja                | 30 | Holandia        | 45 | Słowenia            | 60 | Chorwacja                    |

Ta lista zmienia się w miarę dodawania większej liczby krajów/regionów. Aby uzyskać najbardziej aktualną listę lokalizacje geograficzne, przejdź do załącznika warunki tablicy magazynowej w temacie [warunki produktu](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Firma Microsoft może dostarczać sprzęt fizyczny i dostarczać zamienniki sprzętowych części zamiennych StorSimple do lokalizacje geograficzne na powyższej liście.

> [!IMPORTANT]
> Nie umieszczaj urządzenia fizycznego StorSimple w regionie, w którym StorSimple nie jest obsługiwana. Firma Microsoft nie będzie mogła dostarczać żadnych części zamiennych do krajów/regionów, w których StorSimple nie jest obsługiwana.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Urządzenie w chmurze StorSimple (modele 8010/8020)

W przypadku korzystania z urządzenia w chmurze StorSimple 8010 lub 8020 urządzenie jest obsługiwane i dostępne we wszystkich regionach, w których jest obsługiwana źródłowa maszyna wirtualna. 8010 używa maszyny wirtualnej _Standard_A3_ , która jest obsługiwana we wszystkich regionach świadczenia usługi Azure.

8020 używa usługi Premium Storage i _Standard_DS3_ VM do tworzenia urządzenia w chmurze. 8020 jest obsługiwana w regionach platformy Azure, które obsługują Premium Storage i _Standard_DS3_ maszyn wirtualnych platformy Azure. Przy użyciu [tej listy](https://azure.microsoft.com/regions/services/) sprawdź, czy w Twoim regionie jest dostępna zarówno opcja **Maszyny wirtualne > Seria DS**, jak i opcja **Magazyn > Magazyn na dysku**.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (model 1200)

W przypadku używania macierzy wirtualnej StorSimple serii 1200, obraz dysku wirtualnego jest obsługiwany we wszystkich regionach świadczenia usługi Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [cenach różnych modeli StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Dowiedz się więcej o [zarządzaniu kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej na temat [używania usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
