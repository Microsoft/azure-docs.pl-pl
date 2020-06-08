---
title: Dostępność regionu i dane w miejscu zamieszkania
titleSuffix: Azure AD B2C
description: Dostępność regionów, zamieszkania danych i informacje o Azure Active Directory B2Cych w wersji zapoznawczej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: e7776317b80dbe6d927cf33792d1a180a546fa05
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484228"
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

> Stany Zjednoczone, Kanada, Kostaryka, Dominikana, Salwador, Gwatemala, Meksyk, Panama, Portoryko i Trinidad & Tobago

Dane znajdują się w **Europie** w następujących krajach/regionach:

> Algieria, Austria, Azerbejdżan, Bahrajn, Białoruś, Belgia, Bułgaria, Chorwacja, Cypr, Republika Czeska, Dania, Egipt, Estonia, Finlandia, Francja, Niemcy, Grecja, Węgry, Islandia, Irlandia, Izrael, Włochy, Jordania Kazachstan, Kenia, Kuwejt, Łotwa, Liban, Liechtenstein, Litwa, Luksemburg, Republika Południowej Macedonii, Malta, Czarnogóra, Maroko, Niderlandy, Nigeria, Norwegia, Katar, Portugalia, Rumunia, Hiszpania, Macedonia, Słowacja, REP, REP, Republika Południowej Afryki, Rep. Zjednoczone Królestwo

Dane znajdują się w **Azja i Pacyfik** w następujących krajach/regionach:

> Afganistan, Hongkong SAR, Indie, Indonezja, Japonia, Korea, Malezja, Filipiny, Singapur, Sri Lanka, Tajwan i Tajlandia.

Następujące kraje/regiony są w trakcie dodawania do listy. Na razie można nadal używać Azure AD B2C, wybierając którykolwiek z powyższych krajów/regionów.

> Argentyna, Australia, Brazylia, Chile, Kolumbia, Ekwador, Irak, Nowa Zelandia, Paragwaj, Peru, Urugwaj i Wenezuela.

## <a name="preview-tenant"></a>Zapoznawcza dzierżawy

Jeśli dzierżawa B2C została utworzona w okresie zapoznawczym usługi Azure AD B2C's, prawdopodobnie **Typ dzierżawy** ma wartość **wersja zapoznawcza**.

W takim przypadku należy użyć dzierżawy tylko do celów deweloperskich i testowych. NIE używaj dzierżawy w wersji zapoznawczej dla aplikacji produkcyjnych.

**Brak ścieżki migracji** z DZIERŻAWy B2C w wersji zapoznawczej do DZIERŻAWy B2C w skali produkcyjnej. Musisz utworzyć nową dzierżawę B2C dla aplikacji produkcyjnych.

Istnieją znane problemy związane z usuwaniem dzierżawy B2C w wersji zapoznawczej i tworzenie dzierżawy B2C w skali produkcyjnej o tej samej nazwie domeny. *Musisz utworzyć dzierżawę B2C w skali produkcyjnej z inną nazwą domeny*.

![Zrzut ekranu przedstawiający typ dzierżawy jako dzierżawę w wersji zapoznawczej.](./media/data-residency/preview-b2c-tenant.png)