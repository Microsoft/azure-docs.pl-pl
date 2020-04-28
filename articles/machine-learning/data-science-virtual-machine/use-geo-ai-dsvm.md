---
title: Użyj geograficznego AI
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak używać geograficznego Data Science Virtual Machine AI do analizowania danych i tworzenia modeli opartych na danych geograficznych.
keywords: uczenie głębokie, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278413"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Korzystanie z geograficznie sztucznej analizy Data Science Virtual Machine

Data Science VM geograficznej AI do pobierania danych do analizy, wykonywania przetwarzanie danych i tworzenia modeli dla aplikacji AI korzystających z informacji geoprzestrzennych. Po zainicjowaniu obsługi Data Science VM geograficznym AI i zalogowaniu się w usłudze ArcGIS Pro za pomocą konta ArcGIS możesz rozpocząć korzystanie z usługi ArcGIS Desktop i ArcGIs online. Możesz również uzyskać dostęp do ArcGIS z interfejsów języka Python i mostka językowego R, który jest wstępnie skonfigurowany w Data Science VM geograficznym. Aby tworzyć bogate aplikacje AI, Połącz Data Science VM geograficzną z platformami uczenia maszynowego i głębokiego uczenia oraz innym oprogramowaniem do nauki o danych, które są dla niego dostępne.  


## <a name="configuration-details"></a>Szczegóły konfiguracji

Biblioteka języka Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), która jest używana do interfejsu z ArcGIS, jest instalowana w globalnym głównym środowisku conda środowiska Data Science VM, które znajduje się w ```c:\anaconda```lokalizacji.

- Jeśli używasz języka Python w wierszu polecenia, uruchom ```activate``` polecenie, aby aktywować w głównym środowisku Python Conda.
- Jeśli używasz notesu IDE lub Jupyter, możesz wybrać środowisko lub jądro, aby upewnić się, że jesteś w odpowiednim środowisku Conda.

Mostek R do ArcGIS jest instalowany jako biblioteka języka R o nazwie [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) w głównym wystąpieniu autonomicznym Microsoft Machine Learning Server, które znajduje ```C:\Program Files\Microsoft\ML Server\R_SERVER```się w. Programy Visual Studio, RStudio i Jupyter są już wstępnie skonfigurowane do korzystania z tego środowiska R i będą miały dostęp do biblioteki ```arcgisbinding``` języka r. 


## <a name="geo-ai-data-science-vm-samples"></a>Przykłady geograficznie Data Science VM AI

Oprócz przykładów opartych na środowisku uczenia maszynowego i głębokiego uczenia z Data Science VM podstawowej zestaw próbek geograficznych jest również dostarczany jako część geograficznego Data Science VM AI. Te przykłady mogą ułatwić szybkie rozpoczęcie tworzenia aplikacji AI przy użyciu danych geoprzestrzennych i oprogramowania ArcGIS:


1. Rozpoczynanie pracy [z analizami geograficznymi przy użyciu języka Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): przykład wprowadzający pokazujący, jak korzystać z danych geograficznych za pomocą interfejsu języka Python do ArcGIS, zapewnia Biblioteka [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Pokazano również, jak połączyć tradycyjne Uczenie maszynowe z danymi geograficznymi, a następnie wizualizować wynik mapy w ArcGIS.

2. [Wprowadzenie do analizy geograficznej przy użyciu języka r](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): przykład wprowadzający, który pokazuje, jak korzystać z danych geoprzestrzennych przy użyciu interfejsu języka R do ArcGIS, który jest udostępniany przez bibliotekę [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Klasyfikacja użycia gruntów na poziomie pikseli](https://github.com/Azure/pixel_level_land_classification): samouczek, w którym pokazano, jak utworzyć model sieci głębokiej neuronowych, który akceptuje obraz antenowy jako dane wejściowe i zwraca etykietę okładki gruntowej. Przykłady etykiet okładki gruntowej są *zalesione* i *wodne*. Model zwraca taką etykietę dla każdego piksela na obrazie. 


## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady używające Data Science VM są dostępne tutaj:

* [Samples](dsvm-samples-and-walkthroughs.md)