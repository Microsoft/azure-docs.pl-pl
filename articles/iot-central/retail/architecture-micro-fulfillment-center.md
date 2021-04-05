---
title: Centrum usługi Azure IoT Central Micro-zrealizować | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację centrum Micro-Build przy użyciu naszego szablonu aplikacji centrum firmy Micro do realizacji w IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833783"
---
# <a name="micro-fulfillment-center-architecture"></a>Architektura centrum mikrorealizacji

Rozwiązania do centrów wypełniania rozwiązań umożliwiają cyfrowe łączenie, monitorowanie i zarządzanie wszystkimi aspektami w pełni zautomatyzowanego centrum realizacji, aby zmniejszyć koszty, eliminując czas przestoju, zwiększając bezpieczeństwo i ogólną wydajność. Te rozwiązania można skompilować przy użyciu jednego z szablonów aplikacji w ramach IoT Central i architektury poniżej jako wskazówki.

![Analiza sklepu IoT Central Azure](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Zbiór czujników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i agregowane szczegółowe dane do IoT Central
3. Ciągły eksport danych do żądanej usługi platformy Azure na potrzeby manipulowania
4. Dane mogą być uporządkowane w żądanym formacie i wysyłane do usługi magazynu
5. Aplikacje biznesowe mogą wykonywać zapytania dotyczące danych i generować szczegółowe informacje, które umożliwiają wykonywanie operacji sprzedaży detalicznej
 
Przyjrzyjmy się kluczowym składnikom, które zwykle odgrywają część w rozwiązaniu Centrum rozwiązań.

## <a name="robotic-carriers"></a>Przewoźnicy zautomatyzowane

Rozwiązanie do centrów wypełniania rozwiązań będzie prawdopodobnie mieć duży zestaw zautomatyzowanych nośników generujących różne rodzaje sygnałów telemetrycznych. Sygnały te mogą być pozyskane przez urządzenie bramy, zagregowane, a następnie wysyłane do IoT Central, które są odzwierciedlone po lewej stronie diagramu architektury.  

## <a name="condition-monitoring-sensors"></a>Czujniki monitorowania warunku

Rozwiązanie IoT rozpoczyna się od zestawu czujników przechwytywania znaczących sygnałów z poziomu centrum realizacji. Jest to odzwierciedlone przez różne rodzaje czujników z lewej strony diagramu architektury powyżej.

## <a name="gateway-devices"></a>Urządzenia bramy

Wiele czujników IoT może strumieniowo pokazywać surowe sygnały bezpośrednio do chmury lub do urządzenia bramy znajdującego się w jego sąsiedztwie. Urządzenie bramy wykonuje agregację danych na granicy przed wysłaniem szczegółowych informacji do IoT Central aplikacji. Urządzenia bramy są również odpowiedzialne za przekazywanie operacji na urządzenia czujnika i sterowanie nim, gdy ma to zastosowanie. 

## <a name="iot-central-application"></a>Aplikacja IoT Central

Aplikacja Azure IoT Central pobiera dane z różnych rodzajów czujników IoT, robotów, jak również urządzeń bramy w środowisku centrum realizacji i generuje zestaw istotnych informacji.

Usługa Azure IoT Central zapewnia również dostosowane środowisko operatora magazynu umożliwiające im zdalne monitorowanie urządzeń infrastruktury i zarządzanie nimi.

## <a name="data-transform"></a>Przekształcanie danych
Aplikację IoT Central platformy Azure w ramach rozwiązania można skonfigurować do eksportowania nieprzetworzonych lub zagregowanych informacji do zestawu usług Azure PaaS (platformy jako usługi), które mogą wykonywać manipulowanie danymi i wzbogacać te informacje przed przekazaniem ich do aplikacji biznesowej. 

## <a name="business-application"></a>Aplikacja biznesowa
Dane IoT mogą służyć do zarządzania różnymi rodzajami aplikacji firmowych wdrożonymi w środowisku handlu detalicznego. Menedżer centrum realizacji lub pracownik mogą używać tych aplikacji do wizualizacji szczegółowych informacji biznesowych i wykonywania znaczących akcji w czasie rzeczywistym. Aby dowiedzieć się, jak utworzyć pulpit nawigacyjny Power BI w czasie rzeczywistym dla Twojego zespołu sprzedaży detalicznej, postępuj zgodnie z [samouczkiem](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Następne kroki
* Rozpocznij pracę z szablonem aplikacji [centrum Micro-zrealizować](https://aka.ms/checkouttemplate) . 
* Zapoznaj się z [samouczkiem](https://aka.ms/mfc-tutorial) , który przeprowadzi Cię przez proces tworzenia rozwiązania przy użyciu szablonu aplikacji centrum mikrorealizacji.
