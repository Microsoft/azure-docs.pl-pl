---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Zapoznaj się z kluczowymi obszarami interfejsu użytkownika usługi Azure IoT Central, który służy do tworzenia i używania rozwiązania IoT oraz zarządzania nim.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c702cb16267d5495feedd8d361092b23b6d4deac
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372469"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central

W tym artykule przedstawiono wprowadzenie do interfejsu użytkownika usługi Microsoft Azure IoT Central. Interfejs użytkownika umożliwia tworzenie i używanie rozwiązania usługi Azure IoT Central oraz jego połączonych urządzeń, a także zarządzanie nimi.

Jako _Konstruktor rozwiązań_ możesz zdefiniować rozwiązanie IoT Central platformy Azure za pomocą interfejsu użytkownika IoT Central platformy Azure. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Definiowanie typów urządzeń łączących się z rozwiązaniem
* Konfigurowanie reguł i akcji dla urządzeń 
* Dostosowywanie interfejsu użytkownika dla _operatora_ używającego rozwiązania

_Operator_ używa interfejsu użytkownika usługi Azure IoT Central, aby zarządzać rozwiązaniem usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Monitorowanie urządzeń
* Konfigurowanie urządzeń
* Rozwiązywanie i korygowanie problemów z urządzeniami
* Aprowizacja nowych urządzeń.

## <a name="iot-central-homepage"></a>Strona główna IoT Central

Strona [główna IoT Central](https://aka.ms/iotcentral-get-started) jest miejscem, w którym można dowiedzieć się więcej na temat najnowszych wiadomości i funkcji dostępnych na IoT Central, tworzenia nowych aplikacji i uruchamiania istniejącej aplikacji.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Strona główna IoT Central":::

### <a name="create-an-application"></a>Tworzenie aplikacji

W sekcji Kompilacja można przeglądać listę odpowiednich szablonów IoT Central branżowych, aby pomóc szybko rozpocząć pracę lub zacząć od podstaw przy użyciu niestandardowego szablonu aplikacji.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Strona kompilacji IoT Central":::

Aby dowiedzieć się więcej, zapoznaj się z przewodnikiem Szybki Start [dotyczącym tworzenia aplikacji platformy Azure IoT Central](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Uruchom aplikację

Możesz uruchomić aplikację IoT Central, przechodząc do adresu URL wybranego przez Ciebie lub konstruktora rozwiązań podczas tworzenia aplikacji. Możesz również wyświetlić listę wszystkich aplikacji, do których masz dostęp, w programie [IoT Central App Manager](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central App Manager":::

## <a name="navigate-your-application"></a>Nawigowanie po aplikacji

Gdy jesteś w aplikacji IoT, użyj okienka po lewej stronie, aby uzyskać dostęp do różnych obszarów. Okienko po lewej stronie można rozwinąć lub zwinąć, wybierając ikonę z trzema znakami w górnej części okienka:

> [!NOTE]
> Elementy wyświetlane w lewym okienku są zależne od roli użytkownika. Dowiedz się więcej o [zarządzaniu użytkownikami i rolami](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="okienko po lewej stronie":::

  :::column-end:::
  :::column span="2":::
     **Pulpit nawigacyjny** wyświetla pulpit nawigacyjny aplikacji. Jako *Konstruktor rozwiązań* można dostosować globalny pulpit nawigacyjny dla operatorów. W zależności od ich roli użytkownika operatory mogą również tworzyć własne osobiste pulpity nawigacyjne.
     
     **Urządzenia** umożliwiają zarządzanie połączonymi urządzeniami — w rzeczywistości i symulowane.

     **Grupy urządzeń** umożliwiają wyświetlanie i tworzenie logicznych kolekcji urządzeń określonych przez zapytanie. Możesz zapisać to zapytanie i użyć grup urządzeń za pomocą aplikacji do wykonywania operacji zbiorczych.

     **Reguły** umożliwiają tworzenie i edytowanie reguł do monitorowania urządzeń. Reguły są oceniane na podstawie danych telemetrycznych urządzenia i wyzwalane akcje dostosowywalne.

     **Analiza** umożliwia tworzenie niestandardowych widoków na podstawie danych urządzeń w celu uzyskania szczegółowych informacji z aplikacji.

     **Zadania** umożliwiają zarządzanie urządzeniami na dużą skalę przez wykonywanie operacji zbiorczych.

     **Szablony urządzeń** to miejsce, w którym można tworzyć i zarządzać charakterystyką urządzeń, które łączą się z Twoją aplikacją.

     **Eksport danych** umożliwia skonfigurowanie eksportu ciągłego do usług zewnętrznych, takich jak magazyn i kolejki.

     **Administracja** to miejsce, w którym można zarządzać ustawieniami, dostosowaniami, rozliczeniami, użytkownikami i rolami aplikacji.

     **IoT Central** pozwala *administratorom* na powrót do Menedżera aplikacji IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Wyszukiwanie, pomoc, motyw i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central pasek narzędzi":::

* Aby wyszukać szablony urządzeń i urządzenia, wprowadź wartość **wyszukiwania** .
* Aby zmienić język lub motyw interfejsu użytkownika, wybierz ikonę **Ustawienia** . Dowiedz się więcej o [zarządzaniu preferencjami aplikacji](howto-manage-preferences.md)
* Aby wylogować się z aplikacji, wybierz ikonę **konta** .
* Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. Możesz [uzyskać informacje o aplikacji](./howto-get-app-info.md) z linku **Informacje o aplikacji** . W aplikacji w ramach bezpłatnego planu cenowego zasoby pomocy technicznej obejmują dostęp do [rozmowy na żywo](howto-show-hide-chat.md).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

> [!NOTE]
> Opcja wyboru między jasnym i ciemnym motywem nie jest dostępna, jeśli administrator skonfigurował niestandardowy motyw dla aplikacji.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Zrzut ekranu przedstawiający IoT Central wybierz motyw.":::

### <a name="dashboard"></a>Pulpit nawigacyjny

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Zrzut ekranu przedstawiający pulpit nawigacyjny IoT Central.":::

* Pulpit nawigacyjny to pierwsza strona wyświetlana podczas logowania do aplikacji IoT Central platformy Azure. Jako *Konstruktor rozwiązań* można tworzyć i dostosowywać wiele pulpitów nawigacyjnych aplikacji dla innych użytkowników. Dowiedz się więcej [na temat dodawania kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md)

* Jeśli masz rolę *użytkownika, możesz* utworzyć osobiste pulpity nawigacyjne, aby monitorować interesujące Cię informacje. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie osobistych pulpitów nawigacyjnych usługi Azure IoT Central](howto-create-personal-dashboards.md) .

### <a name="devices"></a>Urządzenia

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Zrzut ekranu przedstawiający stronę urządzeń.":::

Na stronie eksploratora są wyświetlane _urządzenia_ w aplikacji usługi Azure IoT Central pogrupowane według _szablonu urządzeń_. 

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją.
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji.

Aby dowiedzieć się więcej, zobacz [monitorowanie urządzeń](./quick-monitor-devices.md) — Szybki Start. 

### <a name="device-groups"></a>Grupy urządzeń

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Strona grupy urządzeń":::

Grupa urządzeń to kolekcja powiązanych urządzeń. *Konstruktor rozwiązań* definiuje zapytanie w celu zidentyfikowania urządzeń uwzględnionych w grupie urządzeń. Grupy urządzeń służą do wykonywania operacji zbiorczych w aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Używanie grup urządzeń w aplikacji platformy Azure IoT Central](tutorial-use-device-groups.md) .

### <a name="rules"></a>Reguły
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Zrzut ekranu przedstawiający stronę reguł.":::

Strona reguły umożliwia definiowanie reguł na podstawie danych telemetrycznych, stanu lub zdarzeń urządzeń. Po wygenerowaniu reguły może wyzwolić jedną lub więcej akcji — takich jak wysyłanie wiadomości e-mail, powiadamianie systemu zewnętrznego za pośrednictwem alertów elementu webhook itd. Aby dowiedzieć się więcej, zobacz samouczek [konfigurowania reguł](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analiza

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Zrzut ekranu przedstawiający stronę analiza.":::

Analiza umożliwia tworzenie niestandardowych widoków na podstawie danych urządzenia w celu uzyskania szczegółowych informacji z aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie analizy dla aplikacji platformy Azure IoT Central](howto-create-analytics.md) .

### <a name="jobs"></a>Stanowiska

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Strona zadań":::

Strona zadania umożliwia uruchamianie zbiorczych operacji zarządzania urządzeniami na urządzeniach. Można aktualizować właściwości, ustawienia i wykonywanie poleceń dla grup urządzeń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

### <a name="device-templates"></a>Szablony urządzeń

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Zrzut ekranu szablonów urządzeń.":::

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa charakterystykę urządzeń, takich jak:

* Pomiary danych telemetrycznych, stanowych i zdarzeń
* Właściwości
* Polecenia
* Widoki

*Konstruktor rozwiązań* umożliwia również tworzenie formularzy i pulpitów nawigacyjnych dla operatorów używanych do zarządzania urządzeniami.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](howto-set-up-template.md). 

### <a name="data-export"></a>Eksport danych

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Eksport danych":::

Eksport danych umożliwia konfigurowanie strumieni danych, takich jak dane telemetryczne, z aplikacji do systemów zewnętrznych. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administracja

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Zrzut ekranu przedstawiający administrację IoT.":::

Strona Administracja umożliwia skonfigurowanie i dostosowanie aplikacji IoT Central. W tym miejscu możesz zmienić nazwę aplikacji, adres URL, Tworzenie motywów, zarządzanie użytkownikami i rolami, utworzyć tokeny interfejsu API i wyeksportować aplikację. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).
