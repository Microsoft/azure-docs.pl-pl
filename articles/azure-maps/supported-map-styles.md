---
title: Obsługiwane style mapy | Mapy Microsoft Azure
description: W tym artykule omówiono różne style renderowania mapy obsługiwane przez Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c22363b3b1e0b8283de572ff578e6d78f0627486
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128067"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps obsługiwane style mapy
Azure Maps obsługuje kilka różnych wbudowanych stylów mapy, zgodnie z poniższym opisem.

## <a name="road"></a>zwiększa
Mapa **drogowa** to standardowa mapa, która wyświetla drogi, naturalne i sztuczne funkcje wraz z etykietami dla tych funkcji.

![styl mapy drogowej](./media/supported-map-styles/road.png)

**Odpowiednie interfejsy API:**
* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android
* Wizualizacja usługi Power BI

## <a name="blank-and-blank_accessible"></a>puste i blank_accessible

Style " **puste** " i " **blank_accessible** map" zawierają pustą kanwę, na której można wizualizować dane. Styl **blank_accessible** będzie nadal udostępniać aktualizacje czytnika ekranu ze szczegółami lokalizacji mapy, nawet jeśli mapa podstawowa nie jest wyświetlana.

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

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
