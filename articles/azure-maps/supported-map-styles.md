---
title: Obsługiwane wbudowane style mapy Azure Maps
description: Dowiedz się więcej na temat wbudowanych stylów mapy obsługiwanych przez Azure Maps, takich jak Road, blank_accessible, satelit, satellite_road_labels, road_shaded_relief i nocy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4a50a9d6d4a485f7d8e63adb9ae5032f49edc261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310360"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps obsługiwane style wbudowanej mapy

Azure Maps obsługuje kilka różnych wbudowanych stylów mapy, zgodnie z poniższym opisem.

## <a name="road"></a>zwiększa

Mapa **drogowa** to standardowa mapa, która wyświetla drogi. Wyświetla również funkcje naturalne i sztuczne oraz etykiety dla tych funkcji.

![styl mapy drogowej](./media/supported-map-styles/road.png)

**Odpowiednie interfejsy API:**

* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="blank-and-blank_accessible"></a>puste i blank_accessible

Style " **puste** " i " **blank_accessible** map" zawierają pustą kanwę do wizualizacji danych. Styl **blank_accessible** będzie nadal zapewniać aktualizacje czytnika ekranu ze szczegółami lokalizacji mapy, nawet jeśli mapa podstawowa nie jest wyświetlana.

> [!Note]
> W zestawie SDK sieci Web można zmienić kolor tła mapy poprzez ustawienie `background-color` stylu CSS elementu map DIV.

**Odpowiednie interfejsy API:**

* Kontrolka mapy zestawu SDK sieci Web

## <a name="satellite"></a>satellite

Styl **satelity** to kombinacja zdjęć satelitarnych i antenowych.

![styl mapy kafelków satelitarnych](./media/supported-map-styles/satellite.png)

**Odpowiednie interfejsy API:**

* [Kafelek satelitarny](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Ten styl mapy jest hybrydą dróg i etykiet, które są nakładane na Zdjęcia satelitarne i anteny.

![styl mapy satellite_road_labels](./media/supported-map-styles/satellite-road-labels.png)

**Odpowiednie interfejsy API:**

* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**ciemna Skala szarości** jest ciemną wersją stylu mapy drogowej.

![styl mapy gray_scale](./media/supported-map-styles/grayscale-dark.png)

**Odpowiednie interfejsy API:**

* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="grayscale_light"></a>grayscale_light

**sygnalizator skali szarości** to jasna wersja stylu mapy drogowej.

![styl mapy światła skali szarości](./media/supported-map-styles/grayscale-light.png)

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="night"></a>noc

**nocna** to ciemna wersja stylu mapy drogowej z kolorami dróg i symboli.

![styl mapy nocnej](./media/supported-map-styles/night.png)

**Odpowiednie interfejsy API:**

* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

nastąpi **odcień drogi** jest Azure Maps głównym stylem, który został ukończony z obprezentacją ziemi.

![styl mapy zacieniowanych ulg](./media/supported-map-styles/shaded-relief.png)

**Odpowiednie interfejsy API:**

* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** jest ciemnym stylem mapy z wyższym kontrastem niż inne style.

![ciemny styl mapy o dużym kontraście](./media/supported-map-styles/high-contrast-dark.png)

**Odpowiednie interfejsy API:**

* Kontrolka mapy zestawu SDK sieci Web
* Wizualizacja usługi Power BI

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat sposobu ustawiania stylu mapy w Azure Maps:

[Wybieranie stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)