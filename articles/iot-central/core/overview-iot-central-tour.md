---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Zapoznaj się z kluczowymi obszarami interfejsu użytkownika Azure IoT Central, których używasz do tworzenia i używania rozwiązania IoT oraz zarządzania nim.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 564e7d3d2353661d0a69cbd15ad8b281defacf87
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600585"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central

Ten artykuł zawiera wprowadzenie do interfejsu Azure IoT Central użytkownika. Za pomocą interfejsu użytkownika można tworzyć i wykorzystywać aplikację IoT Central i jej połączone urządzenia oraz zarządzać nimi.

## <a name="iot-central-homepage"></a>IoT Central strony głównej

Strona [główna IoT Central](https://aka.ms/iotcentral-get-started) to miejsce, na którym można dowiedzieć się więcej o najnowszych wiadomościach i funkcjach dostępnych na stronie IoT Central, tworzyć nowe aplikacje oraz zobaczyć i uruchomić istniejące aplikacje.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central strony głównej":::

### <a name="create-an-application"></a>Tworzenie aplikacji

W sekcji Build (Kompilacja) możesz przeglądać listę szablonów aplikacji IoT Central branżowych lub zacząć od podstaw przy użyciu szablonu aplikacji niestandardowej.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central kompilacji":::

Aby dowiedzieć się więcej, zobacz przewodnik [Szybki start Tworzenie Azure IoT Central aplikacji.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację IoT Central, przejdź do adresu URL wybranego podczas tworzenia aplikacji. Listę wszystkich aplikacji, do których masz dostęp, można również wyświetlić w [menedżerze IoT Central aplikacji.](https://aka.ms/iotcentral-apps)

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central App Manager":::

## <a name="navigate-your-application"></a>Nawigowanie po aplikacji

Gdy znajdziesz się w aplikacji IoT, użyj okienka po lewej stronie, aby uzyskać dostęp do różnych funkcji. Lewe okienko można rozwinąć lub zwinąć, wybierając ikonę z trzema wierszami w górnej części okienka:

> [!NOTE]
> Elementy, które widzisz w okienku po lewej stronie, zależą od Twojej roli użytkownika. Dowiedz się więcej o [zarządzaniu użytkownikami i rolami.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="lewe okienko":::

  :::column-end:::
  :::column span="2":::
     **Na pulpitach** nawigacyjnych są wyświetlane wszystkie aplikacje i osobiste pulpity nawigacyjne. 
     
     **Urządzenia** umożliwiają zarządzanie wszystkimi urządzeniami.

     **Grupy urządzeń** umożliwiają wyświetlanie i tworzenie kolekcji urządzeń określonych przez zapytanie. Grupy urządzeń są używane za pośrednictwem aplikacji do wykonywania operacji zbiorczych.

     **Reguły** umożliwiają tworzenie i edytowanie reguł w celu monitorowania urządzeń. Reguły są oceniane na podstawie danych urządzenia i wyzwalają dostosowywalne akcje.

     **Analiza** udostępnia zaawansowane możliwości analizowania trendów historycznych i korelowania różnych telemetrii z urządzeń.

     **Zadania** umożliwiają zarządzanie urządzeniami na dużą skalę przez uruchamianie operacji zbiorczych.

     **Szablony urządzeń** umożliwiają tworzenie cech urządzeń, które łączą się z aplikacją, i zarządzanie nimi.

     **Eksportowanie** danych umożliwia skonfigurowanie ciągłego eksportu do usług zewnętrznych, takich jak magazyn i kolejki.

     **Administracja** umożliwia zarządzanie ustawieniami aplikacji, dostosowywaniem, rozliczeniami, użytkownikami i rolami.

     **Moje aplikacje** umożliwia powrót do menedżera IoT Central aplikacji.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Wyszukiwanie, pomoc, motyw i obsługa techniczna

Górne menu jest wyświetlane na każdej stronie:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central narzędzi":::

* Aby wyszukać urządzenia, wprowadź **wartość** wyszukaj.
* Aby zmienić język lub motyw interfejsu użytkownika, wybierz **ikonę** Ustawienia. Dowiedz się więcej o [zarządzaniu preferencjami aplikacji](howto-manage-preferences.md)
* Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. Informacje o [aplikacji można uzyskać za pomocą](./howto-get-app-info.md) linku Informacje **o** aplikacji. W aplikacji w bezpłatnym planie cenowy zasoby pomocy technicznej obejmują dostęp do czatu [na żywo.](howto-show-hide-chat.md)
* Aby wylogować się z aplikacji, wybierz **ikonę** Konto.

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

> [!NOTE]
> Opcja wyboru motywów jasnych i ciemnych nie jest dostępna, jeśli administrator skonfigurował motyw niestandardowy dla aplikacji.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Zrzut ekranu IoT Central wybierz motyw.":::

### <a name="dashboard"></a>Pulpit nawigacyjny

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Zrzut ekranu przedstawiający pulpit IoT Central nawigacyjnym.":::

* Pulpit nawigacyjny to pierwsza strona, która jest widzina po zalogowaniu się do IoT Central aplikacji. Możesz tworzyć i dostosowywać wiele pulpitów nawigacyjnych aplikacji. Dowiedz się więcej o [dodawaniu kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md)

* Osobiste pulpity nawigacyjne można również tworzyć, aby monitorować to, co Cię zależy. Aby dowiedzieć się więcej, zobacz artykuł [Azure IoT Central tworzenie osobistych pulpitów](howto-create-personal-dashboards.md) nawigacyjnych.

### <a name="devices"></a>Urządzenia

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Zrzut ekranu przedstawiający stronę Urządzenia.":::

Na tej stronie przedstawiono urządzenia w aplikacji IoT Central pogrupowane według _szablonu urządzenia._ 

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją.
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji.

Aby dowiedzieć się więcej, zobacz [przewodnik Szybki start Monitorowanie](./quick-monitor-devices.md) urządzeń. 

### <a name="device-groups"></a>Grupy urządzeń

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Strona Grupy urządzeń":::

Ta strona umożliwia tworzenie i wyświetlanie grup urządzeń w IoT Central aplikacji. Za pomocą grup urządzeń można wykonać operacje zbiorcze w aplikacji lub analizować dane. Aby dowiedzieć się więcej, zobacz artykuł Korzystanie z [grup urządzeń w Azure IoT Central aplikacji.](tutorial-use-device-groups.md)

### <a name="rules"></a>Reguły
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Zrzut ekranu przedstawiający stronę Reguły.":::

Ta strona umożliwia wyświetlanie i tworzenie reguł na podstawie danych urządzenia. Po aktywowaniu reguły może ona wyzwolić jedną lub więcej akcji, takich jak wysłanie wiadomości e-mail lub wywołanie element webhook. Aby dowiedzieć się więcej, zobacz [samouczek Konfigurowanie](tutorial-create-telemetry-rules.md) reguł. 

### <a name="analytics"></a>Analiza

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Zrzut ekranu przedstawiający stronę Analiza.":::

Analiza udostępnia zaawansowane możliwości analizowania trendów historycznych i korelowania różnych telemetrii z urządzeń. Aby dowiedzieć się więcej, zobacz [artykuł Tworzenie analizy dla Azure IoT Central aplikacji.](howto-create-analytics.md)

### <a name="jobs"></a>Stanowiska

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Strona zadań":::

Ta strona umożliwia wyświetlanie i tworzenie zadań, które mogą być używane do zbiorczych operacji zarządzania urządzeniami na urządzeniach. Można aktualizować właściwości i ustawienia urządzeń oraz wykonywać polecenia względem grup urządzeń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

### <a name="device-templates"></a>Szablony urządzeń

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Zrzut ekranu przedstawiający szablony urządzeń.":::

Strona szablonów urządzeń umożliwia wyświetlanie i tworzenie szablonów urządzeń w aplikacji. Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](howto-set-up-template.md). 

### <a name="data-export"></a>Eksport danych

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Eksportowanie danych":::

Eksportowanie danych umożliwia konfigurowanie strumieni danych do systemów zewnętrznych. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administracja

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Zrzut ekranu przedstawiający administrację IoT.":::

Strona administracyjna umożliwia konfigurowanie i dostosowywanie aplikacji IoT Central aplikacji. W tym miejscu możesz zmienić nazwę aplikacji, adres URL, tworzenie themingu, zarządzać użytkownikami i rolami, tworzyć tokeny interfejsu API i eksportować aplikację. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).
