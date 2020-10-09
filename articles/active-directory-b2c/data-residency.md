---
title: Dostępność w poszczególnych regionach i miejsce przechowywania danych
titleSuffix: Azure AD B2C
description: Dostępność regionów, zamieszkania danych i informacje o Azure Active Directory B2Cych w wersji zapoznawczej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: f8c6f7daecd38babaa4f2961d04a6cd4c3b4dbed
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840561"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: dostępność regionu & dane miejsca do zamieszkania

Dostępność regionów i miejsca do magazynowania danych to dwie bardzo różne koncepcje, które różnią się w zależności od pozostałej części platformy Azure Azure AD B2C. W tym artykule wyjaśniono różnice między tymi dwoma pojęciami i porównano sposób ich stosowania z platformą Azure a Azure AD B2C.

Azure AD B2C jest **ogólnie dostępna na całym świecie** z opcją **zamieszkania danych** w **Stany Zjednoczone, Europie lub Azja i Pacyfik**.

[Dostępność regionu](#region-availability) dotyczy miejsca, w którym usługa jest dostępna do użycia.

Miejsce [zamieszkania](#data-residency) polega na tym, gdzie są przechowywane dane użytkownika.

## <a name="region-availability"></a>Dostępność w danym regionie

Azure AD B2C jest dostępna na całym świecie za pośrednictwem chmury publicznej platformy Azure.

Różni się to od modelu, po którym następuje większość innych usług platformy Azure, które zwykle oddzielają *dostępność* z miejscami do *zamieszkania*. Przykłady tego przykładu można znaleźć na stronie produkty platformy Azure [dostępne według regionów](https://azure.microsoft.com/regions/services/) i [kalkulatora cen Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezydencja danych

Azure AD B2C przechowuje dane użytkowników w Stany Zjednoczone, Europie lub regionie Azja i Pacyfik.

Dane zamieszkania są określane przez kraj/region wybrany podczas [tworzenia dzierżawy Azure AD B2C](tutorial-create-tenant.md):

![Zrzut ekranu przedstawiający formularz Tworzenie dzierżawy, Wybieranie kraju lub regionu.](./media/data-residency/data-residency-b2c-tenant.png)

Dane znajdują się w **Stany Zjednoczone** dla następujących krajów/regionów:

> Stany Zjednoczone (Stany Zjednoczone), Kanada (CA), Kostaryka (CR), Dominikana (DO), Salwador (SV), Gwatemala (GT), Meksyk (MX), Panama (PA), Portoryko (PR) i Trinidad & Tobago (TT)

Dane znajdują się w **Europie** w następujących krajach/regionach:

> Algieria (DZ), Austria (AT), Azerbejdżan (AZ), Bahrajn (BH), Białoruś (przez), Belgia (), Bułgaria (BG), Chorwacja (HR), Cypr (CY), Republika Czeska (CZ), Danię (Francja), Egipt (EG), Estonia (EE), Finlandia (FT), Francja (FR), Niemcy (DE), Grecja (GR), Węgry (HU), Islandia (IS), Irlandia (IE), Izrael (IL), Włochy (IT), Jordania (JO), Kazachstan (KZ), Kenia (KE), Kuwejt (KW), Łotwa (LV), Liban (LB), Liechtenstein (LI), Litwa (LT) , Luksemburg (LU), Północna Macedonia (ML), Malta (MT), Czarnogóra (ME), Maroko (MA), Holandia (NL), Nigeria (NG), Norwegia (NO), Oman (OM), Pakistan (PK), Polskę (PL), Portugalia (PT), Katar (pytania i odpowiedzi), Rumunia (RO), Rosja (RU), Arabia Saudyjska (SA), Serbia (RS), Słowacja (SK), Słowenii (ST), Afryka Południowa (za), Hiszpania (ES), Szwecja (SE), Szwajcaria (CH), Tunezja (TN), Turcja (TR), Ukraina (UA), Zjednoczone Emiraty Arabskie (AE) i Zjednoczone Królestwo (GB)

Dane znajdują się w **Azja i Pacyfik** w następujących krajach/regionach:

> Afganistan (AF), Hongkong SAR (HK), Indie (w), Indonezja (ID), Japonia (JP), Korea (KR), Malezja (MY), Filipiny (PH), Singapur (SG), Sri Lanka (LK), Tajwan (TW) i Tajlandia (TH).

Następujące kraje/regiony są w trakcie dodawania do listy. Na razie można nadal używać Azure AD B2C, wybierając którykolwiek z powyższych krajów/regionów.

> Argentyna, Australia, Brazylia, Chile, Kolumbia, Ekwador, Irak, Nowa Zelandia, Paragwaj, Peru, Urugwaj i Wenezuela.

## <a name="preview-tenant"></a>Zapoznawcza dzierżawy

Jeśli dzierżawa B2C została utworzona w okresie zapoznawczym usługi Azure AD B2C's, prawdopodobnie **Typ dzierżawy** ma wartość **wersja zapoznawcza**.

W takim przypadku należy użyć dzierżawy tylko do celów deweloperskich i testowych. NIE używaj dzierżawy w wersji zapoznawczej dla aplikacji produkcyjnych.

**Brak ścieżki migracji** z DZIERŻAWy B2C w wersji zapoznawczej do DZIERŻAWy B2C w skali produkcyjnej. Musisz utworzyć nową dzierżawę B2C dla aplikacji produkcyjnych.

Istnieją znane problemy związane z usuwaniem dzierżawy B2C w wersji zapoznawczej i tworzenie dzierżawy B2C w skali produkcyjnej o tej samej nazwie domeny. *Musisz utworzyć dzierżawę B2C w skali produkcyjnej z inną nazwą domeny*.

![Zrzut ekranu przedstawiający typ dzierżawy jako dzierżawę w wersji zapoznawczej.](./media/data-residency/preview-b2c-tenant.png)