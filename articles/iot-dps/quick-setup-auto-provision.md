---
title: Szybki Start — Konfigurowanie IoT Hub Device Provisioning Service w Azure Portal
description: Szybki Start — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service (DPS) w Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e1bd521e9798b09f7930b43ab95c7cd7ef9e693d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737923"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service przy użyciu Azure Portal

IoT Hub Device Provisioning Service może być używana z IoT Hub, aby włączyć obsługę funkcji bezobsługowych, w tym samym czasie, do odpowiedniego centrum IoT, bez konieczności interwencji człowieka, umożliwiając klientom udostępnianie milionów urządzeń IoT w bezpieczny i skalowalny sposób. Usługa Azure IoT Hub Device Provisioning Service obsługuje urządzenia IoT z modułem TPM, kluczem symetrycznym i uwierzytelnianiem certyfikatu X. 509. Aby uzyskać więcej informacji, zobacz [omówienie IoT Hub Device Provisioning Service](./about-iot-dps.md)

W tym przewodniku szybki start dowiesz się, jak skonfigurować IoT Hub Device Provisioning Service w witrynie Azure Portal na potrzeby aprowizacji urządzeń, wykonując następujące czynności:

* Użyj Azure Portal, aby utworzyć IoT Hub
* Użycie witryny Azure Portal do utworzenia usługi IoT Hub Device Provisioning i pobrania zakresu identyfikatorów
* Połączenie centrum IoT z usługą Device Provisioning

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy z tym artykułem będzie potrzebna subskrypcja platformy Azure. Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), jeśli jeszcze tego nie zrobiono.


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Utwórz nowy IoT Hub Device Provisioning Service

1. Wybierz ponownie przycisk **+ Utwórz zasób** .

2. *Wyszukaj w portalu Marketplace* **usługę Device Provisioning**. Wybierz pozycję **IoT Hub Device Provisioning Service** i kliknij przycisk **Utwórz** . 

3. Podaj następujące informacje dotyczące nowego wystąpienia usługi Device Provisioning i kliknij przycisk **Utwórz**.

    * **Nazwa:** Podaj unikatową nazwę nowego wystąpienia usługi Device Provisioning. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.
    * **Subskrypcja:** Wybierz subskrypcję, której chcesz użyć, aby utworzyć to wystąpienie usługi Device Provisioning Service.
    * **Grupa zasobów:** w tym polu możesz utworzyć nową grupę zasobów lub wybrać istniejącą grupę, w której ma znajdować się nowe wystąpienie. Wybierz tę grupę zasobów, w której znajduje się utworzone wcześniej centrum IoT Hub, na przykład **TestResources**. Dzięki umieszczeniu wszystkich powiązanych zasobów w jednej grupie można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w tej grupie. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Lokalizacja:** Wybierz najbliższą lokalizację urządzeń.

      ![Wprowadź podstawowe informacje o wystąpieniu usługi Device Provisioning w bloku portalu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Wybierz przycisk powiadomień, aby monitorować Tworzenie wystąpienia zasobu. Po pomyślnym wdrożeniu usługi wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie **Przejdź do pozycji zasób**.

    ![Monitorowanie powiadomień dotyczących wdrażania](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Łączenie Centrum IoT Hub i usługi Device Provisioning

W tej sekcji dodasz konfigurację do wystąpienia usługi Device Provisioning. Ta konfiguracja określa centrum IoT Hub, dla którego będą aprowizowane urządzenia.

1. Wybierz przycisk **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz wystąpienie usługi Device Provisioning Service utworzone we wcześniejszej sekcji. 

    Jeśli menu jest skonfigurowane przy użyciu **okna wysuwanego** , a nie trybu **zadokowanego** w ustawieniach portalu, należy kliknąć 3 wiersze w lewym górnym rogu, aby otworzyć menu portalu po lewej stronie.  

2. Z menu usługi Device Provisioning wybierz pozycję **połączone centra IoT**. Kliknij przycisk **+ Dodaj** widoczny w górnej części strony. 

3. Na stronie **Dodawanie linku do usługi IoT Hub** podaj następujące informacje, aby połączyć nowe wystąpienie usługi Device Provisioning z Centrum IoT. Następnie naciśnij przycisk **Zapisz**. 

    * **Subskrypcja:** Wybierz subskrypcję zawierającą Centrum IoT Hub, którą chcesz połączyć z nowym wystąpieniem usługi Device Provisioning.
    * **Centrum IoT:** Wybierz Centrum IoT Hub, aby utworzyć połączenie z nowym wystąpieniem usługi Device Provisioning.
    * **Zasady dostępu:** wybierz **iothubowner** jako poświadczenia do nawiązania połączenia z centrum IoT Hub.  

      ![Połącz nazwę centrum, aby połączyć się z wystąpieniem usługi Device Provisioning w bloku portalu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Teraz wybrane centrum powinno być widoczne w bloku **Powiązane centra IoT**. Może być konieczne **odświeżenie** , aby można było je wyświetlić.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. W górnej części okienka szczegółów urządzenia wybierz pozycję **Usuń**.  
2. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. W górnej części okienka szczegółów centrum wybierz pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Centrum IoT Hub i wystąpienie usługi Device Provisioning Service i połączono te dwa zasoby. Aby dowiedzieć się, jak używać tego Instalatora do aprowizacji symulowanego urządzenia, przejdź do przewodnika Szybki Start dotyczącego tworzenia symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący tworzenia symulowanego urządzenia](./quick-create-simulated-device-symm-key.md)
