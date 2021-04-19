---
title: Zarządzanie IoT Central z Azure Portal | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central i zarządzania nimi z Azure Portal.
services: iot-central
ms.service: iot-central
author: vishwam
ms.author: vishwams
ms.date: 04/17/2021
ms.topic: how-to
manager: philmea
ms.openlocfilehash: ed65e85c7428bf59fe770534e97afdd53564086a
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107601991"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzanie IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Za pomocą tego [Azure Portal](https://portal.azure.com) można tworzyć aplikacje IoT Central, podobnie jak w menedżerze aplikacji IoT Central, oraz [zarządzać nimi.](https://apps.azureiotcentral.com/myapps)

## <a name="create-iot-central-applications"></a>Tworzenie IoT Central aplikacji

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Aby utworzyć aplikację, przejdź do strony [IoT Central aplikacji](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) w Azure Portal i wypełnij formularz.

![Tworzenie IoT Central formularza](media/howto-manage-iot-central-from-portal/image6a.png)

* **Nazwa zasobu** to unikatowa nazwa, która można wybrać dla IoT Central w grupie zasobów platformy Azure.

* **Adres URL** aplikacji to adres URL, za pomocą których można uzyskać dostęp do aplikacji.

* **Lokalizacja** to [lokalizacja geograficzna,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej twoich urządzeń, aby uzyskać optymalną wydajność. Azure IoT Central jest obecnie dostępna w następujących lokalizacjach:
    * Azja i Pacyfik
    * Australia
    * Europa
    * Japonia
    * Zjednoczone Królestwo
    * Stany Zjednoczone

  Po wybraniu lokalizacji nie można później przenieść aplikacji do innej lokalizacji.

Po wypełnieniu wszystkich pól wybierz pozycję **Utwórz**. Aby uzyskać więcej informacji, zobacz przewodnik [Szybki start Tworzenie IoT Central aplikacji.](quick-deploy-iot-central.md)

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi IoT Central aplikacji

Jeśli masz już aplikację Azure IoT Central, możesz ją usunąć lub przenieść do innej subskrypcji lub grupy zasobów w Azure Portal.

> [!NOTE]
> Aplikacje utworzone przy użyciu *planu* bezpłatnego nie wymagają subskrypcji platformy Azure, dlatego nie znajdziesz ich na liście w ramach subskrypcji platformy Azure na Azure Portal. Bezpłatne aplikacje można wyświetlić i zarządzać nimi tylko z IoT Central portal.

Aby rozpocząć, wyszukaj aplikację na pasku wyszukiwania w górnej części Azure Portal. Możesz również wyświetlić wszystkie aplikacje, wyszukując pozycję "IoT Central Applications" i wybierając usługę:

![Zrzut ekranu przedstawiający wyniki wyszukiwania dla "IoT Central Aplikacji" z wybraną pierwszą usługą.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Po wybraniu aplikacji w wynikach wyszukiwania zostanie Azure Portal jej omówienie. Możesz przejść do rzeczywistej aplikacji, wybierając adres **URL IoT Central aplikacji:**

![Zrzut ekranu przedstawiający stronę "Przegląd" z wyróżni IoT Central Url aplikacji.](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, wybierz **pozycję zmień** obok grupy zasobów. Na **stronie Przenoszenie zasobów** wybierz grupę zasobów, do których chcesz przenieść tę aplikację:

![Zrzut ekranu przedstawiający stronę "Przegląd" z wyróżniona grupę zasobów (zmiana)".](media/howto-manage-iot-central-from-portal/image4a.png)

Aby przenieść aplikację do innej subskrypcji, wybierz pozycję  **zmień** obok subskrypcji. Na **stronie Przenoszenie zasobów** wybierz subskrypcję, do których chcesz przenieść tę aplikację:

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać aplikacjami Azure IoT Central aplikacji z Azure Portal, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)