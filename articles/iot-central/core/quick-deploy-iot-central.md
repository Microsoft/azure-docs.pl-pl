---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Tworzenie nowej aplikacji usługi Azure IoT Central. Utwórz aplikację przy użyciu bezpłatnego planu cenowego lub jednego ze standardowych planów cenowych.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b670776804c9758774bf216052254148f063e7da
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758147"
---
# <a name="create-an-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Ten przewodnik Szybki start pokazuje, jak utworzyć aplikację Azure IoT Central.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do centralnej witryny [kompilacji usługi Azure IoT.](https://aka.ms/iotcentral) Następnie zaloguj się za pomocą konta osobistego, służbowego lub szkolnego firmy Microsoft.

Możesz utworzyć nową aplikację z listy szablonów IoT Central odpowiednich dla branży, aby ułatwić szybkie rozpoczęcie pracy, lub rozpocząć od zera przy użyciu szablonu **aplikacji niestandardowych.** W tym przewodniku Szybki start można użyć szablonu **aplikacji niestandardowej.**

Aby utworzyć nową aplikację Usługi Azure IoT Central na podstawie szablonu **aplikacji niestandardowej:**

1. Przejdź do strony **Kompilacja:**

    ![Tworzenie strony aplikacji IoT](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Wybierz **pozycję Aplikacje niestandardowe** i upewnij się, że jest zaznaczony szablon **aplikacji niestandardowej.**

1. Usługa Azure IoT Central automatycznie sugeruje **nazwę aplikacji** na podstawie wybranego szablonu aplikacji. Możesz użyć tej nazwy lub wprowadzić własną przyjazną nazwę aplikacji.

1. Usługa Azure IoT Central generuje również unikatowy prefiks **adresu URL aplikacji** na podstawie nazwy aplikacji. Ten adres URL służy do uzyskiwania dostępu do aplikacji. Zmień ten prefiks adresu URL na coś bardziej pamiętnego, jeśli chcesz.

    ![Usługa Azure IoT Central Tworzenie strony aplikacji](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Informacje rozliczeniowe usługi Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Jeśli na poprzedniej stronie **wybrano aplikację niestandardową,** zostanie wyświetlona listy rozwijanej **Szablon aplikacji.** W tym miejscu można przełączać się między szablonami niestandardowymi i starszymi. Mogą również zostać wyświetlone inne szablony, które zostały udostępnione w organizacji.

1. Wybierz, aby utworzyć tę aplikację przy użyciu 7-dniowego bezpłatnego planu cenowego próbnego lub jednego ze standardowych planów cenowych:

    - Aplikacje utworzone za pomocą *bezpłatnego* planu są bezpłatne przez siedem dni i obsługują do pięciu urządzeń. Możesz przekonwertować je na standardowy plan cenowy w dowolnym momencie przed ich wygaśnięciem.
    - Aplikacje utworzone przy użyciu *planu standardowego* są rozliczane na podstawie jednego urządzenia, można wybrać plan cenowy **Standard 1** lub **Standard 2,** przy czym dwa pierwsze urządzenia są bezpłatne. Dowiedz się więcej o bezpłatnych i standardowych planach cenowych na stronie cennik Azure [IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Jeśli tworzysz aplikację przy użyciu standardowego planu cenowego, musisz wybrać *katalog,* *subskrypcję platformy Azure*i *lokalizację:*
        - *Katalog* to usługa Azure Active Directory, w której można utworzyć aplikację. Usługa Azure Active Directory zawiera tożsamości użytkowników, poświadczenia i inne informacje organizacyjne. Jeśli nie masz usługi Azure Active Directory, jeden jest tworzony dla Ciebie podczas tworzenia subskrypcji platformy Azure.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. Zasoby IoT Central w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć bezpłatnie na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription) Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do **strony Nowa aplikacja.** Twoja nowa subskrypcja jest teraz wyświetlana w rozwijanej **subskrypcji platformy Azure.**
        - *Lokalizacja* to [lokalizacja,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej urządzeń, aby uzyskać optymalną wydajność. Po wybraniu lokalizacji nie można później przenieść aplikacji do innej lokalizacji.

1. Zapoznaj się z Regulaminem i wybierz pozycję **Utwórz** u dołu strony. Po kilku minutach aplikacja IoT Central jest gotowa do użycia:

    ![Aplikacja Azure IoT Central](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono aplikację usługi IoT Central. Oto sugerowany następny krok, aby kontynuować naukę o IoT Central:

> [!div class="nextstepaction"]
> [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-simulated-device.md)

Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w jakiś kod, sugerowanym następnym krokiem jest:
> [!div class="nextstepaction"]
> [Tworzenie i łączenie aplikacji klienckiej z aplikacją Azure IoT Central](./tutorial-connect-device-nodejs.md)
