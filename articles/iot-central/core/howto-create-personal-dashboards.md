---
title: Tworzenie pulpitów nawigacyjnych usługi Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak tworzyć pulpity nawigacyjne i zarządzać nimi.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366725"
---
# <a name="create-and-manage-multiple-dashboards"></a>Tworzenie wielu pulpitów nawigacyjnych i zarządzanie nimi

**Pulpit nawigacyjny** jest stroną, która ładuje się po pierwszym przejściu do aplikacji. **Konstruktor** w aplikacji definiuje domyślnego pulpitu nawigacyjnego aplikacji dla wszystkich użytkowników. Możesz również utworzyć własny, spersonalizowany pulpit nawigacyjny aplikacji. Można mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi.

Jeśli jesteś **administratorem** aplikacji, możesz również utworzyć maksymalnie 10 pulpitów nawigacyjnych na poziomie aplikacji, aby udostępnić je innym użytkownikom aplikacji. Tylko **administratorzy** mogą tworzyć, edytować i usuwać pulpity nawigacyjne na poziomie aplikacji.  

## <a name="create-dashboard"></a>Utwórz pulpit nawigacyjny

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie **niestandardowego szablonu aplikacji** . Możesz zastąpić domyślny pulpit nawigacyjny aplikacji za pomocą osobistego pulpitu nawigacyjnego lub administratora, innego pulpitu nawigacyjnego na poziomie aplikacji. Aby to zrobić, wybierz pozycję **+ Nowy** w lewym górnym rogu strony.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny dla aplikacji opartych na szablonie "aplikacja niestandardowa"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Wybranie pozycji **+ Nowy** powoduje otwarcie edytora pulpitu nawigacyjnego. W edytorze możesz nadać pulpitowi nawigacyjnemu nazwę i wybrać elementy z biblioteki. Biblioteka zawiera elementy pierwotne kafelków i pulpitów nawigacyjnych, których można użyć do dostosowania pulpitu nawigacyjnego.

> [!div class="mx-imgBorder"]
> ![Biblioteka pulpitu nawigacyjnego](media/howto-create-personal-dashboards/dashboard-library.png)

Jeśli jesteś **administratorem** aplikacji, otrzymasz opcję utworzenia pulpitu nawigacyjnego na poziomie osobistym lub pulpitu nawigacyjnego na poziomie aplikacji. W przypadku tworzenia pulpitu nawigacyjnego na poziomie osobistym tylko ty zobaczysz. Jeśli utworzysz pulpit nawigacyjny na poziomie aplikacji, będzie on widoczny dla każdego użytkownika aplikacji. Po wprowadzeniu tytułu i wybraniu typu pulpitu nawigacyjnego, który chcesz utworzyć, możesz zapisać i dodać kafelki później. Lub jeśli jesteś teraz gotowy i dodasz szablon urządzenia i wystąpienie urządzenia, możesz utworzyć pierwszy kafelek.  

> [!div class="mx-imgBorder"]
> ![Konfigurowanie szczegółów urządzenia z informacjami o temperaturze](media/howto-create-personal-dashboards/device-details.png)

Na przykład można dodać kafelek **telemetrii** dla bieżącej temperatury urządzenia. W tym celu:

1. Wybierz **szablon urządzenia**
1. Wybierz urządzenie z **urządzeń** , które ma być wyświetlane na kafelku pulpitu nawigacyjnego. Następnie zobaczysz listę właściwości urządzenia, które mogą być używane na kafelku.
1. Aby utworzyć kafelek na pulpicie nawigacyjnym, wybierz pozycję **temperatura** i przeciągnij ją do obszaru pulpit nawigacyjny. Możesz również zaznaczyć pole wyboru obok pozycji **temperatura** i wybrać pozycję **Dodaj kafelek**. Poniższy zrzut ekranu przedstawia Wybieranie szablonu urządzenia i urządzenia, a następnie Tworzenie kafelka telemetrii na pulpicie nawigacyjnym.
1. Wybierz pozycję **Zapisz** w lewym górnym rogu, aby zapisać zmiany na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny — karta z szczegółowymi informacjami o kafelku temperatury](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Teraz, gdy przeglądasz osobisty pulpit nawigacyjny, zobaczysz nowy kafelek z ustawieniem **temperatury** dla urządzenia:

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący nowy kafelek z ustawieniem temperatury dla urządzenia.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Możesz eksplorować inne typy kafelków w bibliotece, aby dowiedzieć się, jak dodatkowo dostosować swoje osobiste pulpity nawigacyjne.

Aby dowiedzieć się więcej na temat używania kafelków w usłudze Azure IoT Central, zobacz [Dodawanie kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Zarządzanie pulpitami nawigacyjnymi

Możesz mieć kilka osobistych pulpitów nawigacyjnych i przełączać się między nimi lub wybierać spośród jednego z domyślnych pulpitów nawigacyjnych aplikacji:

> [!div class="mx-imgBorder"]
> ![Przełączanie między pulpitami nawigacyjnymi](media/howto-create-personal-dashboards/switch-dashboards.png)

Możesz edytować osobiste pulpity nawigacyjne i usunąć wszystkie pulpity nawigacyjne, które nie są już potrzebne. Jeśli jesteś **administratorem**, możesz również edytować lub usuwać pulpity nawigacyjne na poziomie aplikacji.

> [!div class="mx-imgBorder"]
> ![Usuń pulpity nawigacyjne](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć osobiste pulpity nawigacyjne i zarządzać nimi, możesz [dowiedzieć się, jak zarządzać preferencjami aplikacji](howto-manage-preferences.md).
