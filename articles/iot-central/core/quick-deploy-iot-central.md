---
title: Szybki start — tworzenie Azure IoT Central aplikacji | Microsoft Docs
description: Szybki start — tworzenie nowej aplikacji Azure IoT Central aplikacji. Utwórz aplikację przy użyciu bezpłatnego planu cenowego lub jednego ze standardowych planów cenowych.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 7b33beaad580e64a4760b0557f04f266ecfc1b4d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718813"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Szybki start — tworzenie Azure IoT Central aplikacji

W tym przewodniku Szybki start pokazano, jak utworzyć Azure IoT Central aplikacji.

## <a name="prerequisite"></a>Wymaganie wstępne 

 - Konto platformy Azure z aktywną subskrypcją. Utwórz bezpłatne [konto.](https://aka.ms/createazuresubscription)
 - Subskrypcja platformy Azure powinna mieć dostęp współautora

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny [Azure IoT Central Kompilacja.](https://aka.ms/iotcentral) Następnie zaloguj się przy użyciu osobistego, służbowego lub szkolnego konta Microsoft.

Możesz utworzyć nową aplikację na podstawie listy branżowych szablonów IoT Central, aby ułatwić szybkie rozpoczęcie pracy, lub zacząć od podstaw przy użyciu niestandardowego **szablonu** aplikacji. W tym przewodniku Szybki start użyjemy szablonu **Aplikacja niestandardowa.**

Aby utworzyć nową aplikację Azure IoT Central szablonu **aplikacji niestandardowej:**

1. Przejdź do strony **Kompilacja:**

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Tworzenie strony aplikacji IoT":::

1. Wybieranie **aplikacji niestandardowej**

1. Na stronie **Nowa aplikacja** upewnij się, że w obszarze Szablon aplikacji **wybrano** opcję Aplikacja **niestandardowa.**

1. Azure IoT Central automatycznie sugeruje **nazwę aplikacji** na podstawie wybranego szablonu aplikacji. Możesz użyć tej nazwy lub wprowadzić własną przyjazną nazwę aplikacji.

1. Azure IoT Central generuje również unikatowy prefiks **adresu URL** na podstawie nazwy aplikacji. Ten adres URL umożliwia dostęp do aplikacji. Jeśli chcesz, zmień ten prefiks adresu URL na coś bardziej pamiętnego.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central tworzenie strony aplikacji":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central informacji rozliczeniowych":::

    > [!Tip]
    > Jeśli na **poprzedniej stronie wybrano** opcję Aplikacja niestandardowa, zobaczysz menu **rozwijane Szablon** aplikacji. Na liście rozwijanej mogą być wyświetlane inne szablony, które zostały udostępnione Przez organizację.

1. Utwórz tę aplikację przy użyciu planu cenowego 7-dniowej bezpłatnej wersji próbnej lub jednego ze standardowych planów cenowych:

    - Aplikacje, które tworzysz za pomocą *planu bezpłatnego,* są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Można je przekonwertować w celu korzystania ze standardowego planu cenowego w dowolnym momencie, zanim wygasną.
        > [!NOTE]
        > Aplikacje utworzone przy użyciu *planu bezpłatnego* nie wymagają subskrypcji platformy Azure, dlatego nie będą one wyświetlane w twojej subskrypcji platformy Azure na Azure Portal. Bezpłatne aplikacje można wyświetlić i zarządzać nimi tylko z IoT Central portal.          
    - Aplikacje, które  tworzysz przy użyciu planu standardowego, są rozliczane według urządzeń. Możesz wybrać plan cenowy **Standard 0,** **Standard 1** lub **Standard 2** z dwoma pierwszymi urządzeniami, które są bezpłatne. Dowiedz się więcej o bezpłatnych i standardowych planach cenowych na [Azure IoT Central cennika.](https://azure.microsoft.com/pricing/details/iot-central/) Jeśli tworzysz aplikację przy użyciu standardowego planu cenowego, musisz wybrać *katalog,* *subskrypcję platformy Azure* i *lokalizację*:
        - *Katalog* to Azure Active Directory, w którym tworzysz aplikację. Aplikacja Azure Active Directory tożsamości użytkowników, poświadczenia i inne informacje organizacyjne. Jeśli nie masz konta usługi Azure Active Directory, zostanie on utworzony podczas tworzenia subskrypcji platformy Azure.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central apowizyjuje zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć ją bezpłatnie na stronie tworzenia [konta platformy Azure.](https://aka.ms/createazuresubscription) Po utworzeniu subskrypcji platformy Azure wróć do strony **Nowa** aplikacja. Nowa subskrypcja zostanie wyświetlona na liście **rozwijanej** Subskrypcja platformy Azure.
        - *Lokalizacja* to [lokalizacja geograficzna,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Po wybraniu lokalizacji nie można później przenieść aplikacji do innej lokalizacji.

1. Przejrzyj warunki i postanowienia, a następnie wybierz **pozycję Utwórz** w dolnej części strony. Po kilku minutach aplikacja IoT Central będzie gotowa do użycia:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central aplikacji":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono aplikację usługi IoT Central. Oto sugerowany następny krok, aby kontynuować naukę IoT Central:

> [!div class="nextstepaction"]
> [Dodawanie symulowanego urządzenia do IoT Central aplikacji](./quick-create-simulated-device.md)

Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w kod, następnym sugerowanym krokiem jest:
> [!div class="nextstepaction"]
> [Tworzenie aplikacji klienckiej i łączenie jej z aplikacją Azure IoT Central aplikacji](./tutorial-connect-device.md)
