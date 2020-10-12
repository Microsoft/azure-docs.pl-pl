---
title: Integrowanie danych SIM w rozwiązaniu do zdalnego monitorowania — Azure | Microsoft Docs
description: W tym artykule opisano sposób integrowania danych Telefónica SIM z rozwiązaniem do zdalnego monitorowania.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323961"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrowanie danych SIM w rozwiązaniu do zdalnego monitorowania

Urządzenia IoT często łączą się z chmurą przy użyciu karty SIM, która umożliwia im wysyłanie strumieni danych z dowolnego miejsca. Rozwiązanie do monitorowania zdalnego usługi Azure IoT umożliwia integrację danych z łącznością IoT Managed, dzięki czemu operatorzy mogą również śledzić kondycję urządzenia za pośrednictwem danych dostarczanych przez usługę IoT SIM.

Zdalne monitorowanie zapewnia integrację z usługą Telefónica IoT, dzięki czemu klienci korzystający z jej platformy łączności IoT synchronizują dane łączności z urządzeniami SIMs z ich rozwiązaniami. To rozwiązanie można rozszerzyć, aby obsługiwać innych dostawców łączności IoT za pośrednictwem [repozytorium](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

* Integrowanie danych Telefónica IoT SIM z rozwiązaniem do zdalnego monitorowania
* Wyświetlanie danych telemetrycznych w czasie rzeczywistym
* Wyświetlanie danych z karty SIM

## <a name="telefnica-iot-integration-setup"></a>Telefónica — konfiguracja integracji IoT

### <a name="prerequisites"></a>Wymagania wstępne

Ta dodatkowa funkcja zdalnego monitorowania jest obecnie dostępna w wersji zapoznawczej. Aby zsynchronizować dane łączności z rozwiązaniem do zdalnego monitorowania platformy Azure, wykonaj następujące kroki:

1. Wypełnij żądanie w [witrynie Telefónica's](https://iot.telefonica.com/contact), wybierz opcję **Monitorowanie zdalne platformy Azure**, w tym dane kontaktowe.
2. Telefónica aktywuje Twoje konto.
3. Jeśli nie jesteś jeszcze klientem programu Telefónica i chcesz korzystać z tej usługi lub innych gotowych usług łączności IoT w chmurze, odwiedź [witrynę Telefónica's](https://iot.telefonica.com/) i wybierz opcję **łączność**.

### <a name="telefnica-sim-setup"></a>Konfiguracja Telefónica SIM
Skojarzenie identyfikatorów urządzeń z Telefónica SIM & platformy Azure jest oparte na właściwości "alias" Telefónica IoT SIM. 

Przejdź do [portalu Telefónica IoT Connectivity Platform](https://m2m-movistar-es.telefonica.com/) > > wybierz kartę SIM i zaktualizuj każdą kartę SIM "alias" z żądanym identyfikatorem. To zadanie można również wykonać w trybie zbiorczym (Zobacz podręczniki użytkownika platformy łączności IoT Telefónica).

To zadanie można również wykonać w trybie zbiorczym (Zobacz podręczniki użytkownika platformy łączności IoT Telefónica)

![Telefónica Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Aby połączyć urządzenie ze zdalnym monitorowaniem, możesz wykonać te samouczki przy użyciu języka [C](iot-accelerators-connecting-devices-linux.md) lub [węzła](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Wyświetlanie właściwości danych telemetrycznych urządzenia i karty SIM

Po poprawnym skonfigurowaniu konta Telefónica i przełączeniu urządzenia można wyświetlić szczegóły urządzenia i dane karty SIM.

Następujące parametry łączności są publikowane:

* Identyfikator ICCID
* Adres IP
* Obecność w sieci
* Stan SIM
* Lokalizacja sieciowa
* Zużywany ruch danych

![Zrzut ekranu przedstawiający okno Device Explorer na pulpicie nawigacyjnym monitorowania zdalnego usługi Azure IoT. Wiersz pokazujący szczegóły urządzenia WeatherStation jest wyróżniony.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zintegrować dane z karty SIM z monitorowaniem zdalnym usługi Azure IoT, poniżej przedstawiono sugerowane kolejne kroki dla akceleratorów rozwiązań:

* [Korzystanie z rozwiązania do zdalnego monitorowania usługi Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Wykonywanie zaawansowanego monitorowania](iot-accelerators-remote-monitoring-monitor.md)
* [Zarządzanie urządzeniami](iot-accelerators-remote-monitoring-manage.md)
* [Rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md)

