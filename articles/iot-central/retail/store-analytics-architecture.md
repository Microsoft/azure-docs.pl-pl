---
title: Architektura usługi Store Analytics
description: Dowiedz się, jak utworzyć aplikację do analizy w sklepie przy użyciu szablonu aplikacji do wyewidencjonowania w IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832202"
---
# <a name="in-store-analytics-architecture"></a>Architektura analizy w sklepie


Rozwiązania do analizy w sklepie umożliwiają monitorowanie różnych warunków w środowisku magazynu detalicznego. Te rozwiązania można skompilować przy użyciu jednego z szablonów aplikacji w ramach IoT Central i architektury poniżej jako wskazówki.


![Analiza sklepu IoT Central Azure](./media/architecture/store-analytics-architecture-frame.png)

- Zbiór czujników IoT wysyłających dane telemetryczne do urządzenia bramy
- Urządzenia bramy wysyłające dane telemetryczne i agregowane szczegółowe dane do IoT Central
- Ciągły eksport danych do żądanej usługi platformy Azure na potrzeby manipulowania
- Dane mogą być uporządkowane w żądanym formacie i wysyłane do usługi magazynu
- Aplikacje biznesowe mogą wykonywać zapytania dotyczące danych i generować szczegółowe informacje, które umożliwiają wykonywanie operacji sprzedaży detalicznej
 
Przyjrzyjmy się kluczowym składnikom, które zwykle odgrywają część w rozwiązaniu do analizy w sklepie.

## <a name="condition-monitoring-sensors"></a>Czujniki monitorowania warunku

Rozwiązanie IoT rozpoczyna się od zestawu czujników przechwytywania znaczących sygnałów z w środowisku magazynu detalicznego. Jest to odzwierciedlone przez różne rodzaje czujników z lewej strony diagramu architektury powyżej.

## <a name="gateway-devices"></a>Urządzenia bramy

Wiele czujników IoT może strumieniowo pokazywać surowe sygnały bezpośrednio do chmury lub do urządzenia bramy znajdującego się w jego sąsiedztwie. Urządzenie bramy wykonuje agregację danych na granicy przed wysłaniem szczegółowych informacji do IoT Central aplikacji. Urządzenia bramy są również odpowiedzialne za przekazywanie operacji na urządzenia czujnika i sterowanie nim, gdy ma to zastosowanie. 

## <a name="iot-central-application"></a>Aplikacja IoT Central

Aplikacja platformy Azure IoT Central pozyskiwanie danych z różnych rodzajów czujników IoT jako urządzeń z bramą w sklepie detalicznym oraz generowanie zestawu istotnych informacji.

Usługa Azure IoT Central zapewnia również dostosowane środowisko operatora magazynu umożliwiające im zdalne monitorowanie urządzeń infrastruktury i zarządzanie nimi.

## <a name="data-transform"></a>Przekształcanie danych
Aplikację IoT Central platformy Azure w ramach rozwiązania można skonfigurować do eksportowania nieprzetworzonych lub zagregowanych informacji do zestawu usługi Azure PaaS (platformy jako usługi), które umożliwiają manipulowanie danymi i wzbogacają te informacje przed przekazaniem ich do aplikacji biznesowej. 

## <a name="business-application"></a>Aplikacja biznesowa
Dane IoT mogą służyć do zarządzania różnymi rodzajami aplikacji firmowych wdrożonymi w środowisku handlu detalicznego. Menedżer sklepu detalicznego lub członek personelu może używać tych aplikacji do wizualizacji szczegółowych informacji o firmie i wykonywania znaczących działań w czasie rzeczywistym. Aby dowiedzieć się, jak utworzyć pulpit nawigacyjny Power BI w czasie rzeczywistym dla Twojego zespołu sprzedaży detalicznej, postępuj zgodnie z [samouczkiem](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Następne kroki
* Rozpocznij pracę z szablonami aplikacji do [wyewidencjonowania analizy w sklepie](https://aka.ms/checkouttemplate) oraz [monitorowania w sklepie](https://aka.ms/conditiontemplate) . 
* Zapoznaj się z [samouczkiem kompleksowym](https://aka.ms/storeanalytics-tutorial) , który przeprowadzi Cię przez proces tworzenia rozwiązania przy użyciu jednego z szablonów aplikacji In-Store Analytics.
