---
title: Zarządzaj IoT Central z Azure Portal | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central i zarządzania nimi z poziomu Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 0802169898cad51e9a5d208e546a004f7ea7b3c0
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653336"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzaj IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [Azure Portal](https://portal.azure.com) do zarządzania aplikacjami.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central

Aby utworzyć aplikację, przejdź do [Azure Portal](https://ms.portal.azure.com) i wybierz pozycję **Utwórz zasób**.

W obszarze **Wyszukaj na pasku Marketplace** wpisz *IoT Central*:

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a1.png)

Wybierz kafelek **aplikacji IoT Central** w wynikach wyszukiwania:

![Portal zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b1.png)

Teraz wybierz pozycję **Utwórz**:

![Portal zarządzania: zasób IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Wypełnij wszystkie pola w formularzu. Ten formularz jest podobny do formularza wypełnianego, aby utworzyć aplikacje w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji IoT Central](quick-deploy-iot-central.md) — Szybki Start.

![Utwórz formularz IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Lokalizacja jest lokalizacją** geograficzną, [w której chcesz](https://azure.microsoft.com/global-infrastructure/geographies/) utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Usługa Azure IoT Central jest obecnie dostępna w **Australii**, **Azja i Pacyfik**, **Europie**, **Stany Zjednoczone**, **Zjednoczonym Królestwie**i **Japonii** lokalizacje geograficzne. Po wybraniu lokalizacji nie można przenieść aplikacji do innej lokalizacji później.

Po wypełnieniu wszystkich pól wybierz pozycję **Utwórz**.

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central

Jeśli masz już aplikację IoT Central platformy Azure, możesz ją usunąć lub przenieść do innej subskrypcji lub grupy zasobów w Azure Portal.

> [!NOTE]
> W Azure Portal nie są wyświetlane aplikacje utworzone w ramach bezpłatnego planu cenowego, ponieważ nie są one skojarzone z Twoją subskrypcją.

Aby rozpocząć, wybierz pozycję **wszystkie zasoby** w portalu. Wybierz pozycję **Pokaż ukryte typy** i zacznij wpisywać nazwę aplikacji w polu **Filtruj według nazwy** , aby je znaleźć. Następnie wybierz aplikację IoT Central, którą chcesz zarządzać.

Aby przejść do aplikacji, wybierz **adres URL aplikacji IoT Central**:

![Zrzut ekranu pokazujący stronę "przegląd" z wyróżnioną "IoT Central adresem URL aplikacji".](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, wybierz pozycję **Zmień** obok grupy zasobów. Na stronie **przenoszenie zasobów** wybierz grupę zasobów, do której chcesz przenieść tę aplikację:

![Zrzut ekranu przedstawiający stronę "przegląd" z wyróżnioną pozycją "Grupa zasobów (zmiana)".](media/howto-manage-iot-central-from-portal/image4a.png)

Aby przenieść aplikację do innej subskrypcji, wybierz pozycję  **Zmień** obok subskrypcji. Na stronie **przenoszenie zasobów** wybierz subskrypcję, do której chcesz przenieść tę aplikację:

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu Azure Portal, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)