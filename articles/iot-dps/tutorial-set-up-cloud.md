---
title: Samouczek — Konfigurowanie chmury dla usługi Azure IoT Hub Device Provisioning Service w portalu
description: W tym samouczku przedstawiono sposób konfigurowania zasobów w chmurze na potrzeby aprowizacji urządzeń w [Azure Portal](https://portal.azure.com) przy użyciu IoT Hub Device PROVISIONING Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316073"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Samouczek: Konfigurowanie zasobów w chmurze na potrzeby aprowizacji urządzeń przy użyciu IoT Hub Device Provisioning Service

Ten samouczek przedstawia konfigurowanie chmury pod kątem automatycznego aprowizowania urządzeń za pomocą usługi IoT Hub Device Provisioning. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użycie witryny Azure Portal do utworzenia usługi IoT Hub Device Provisioning i pobrania zakresu identyfikatorów
> * Tworzenie centrum IoT
> * Połączenie centrum IoT z usługą Device Provisioning
> * Ustawienie zasad alokacji w usłudze Device Provisioning

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Utworzenie wystąpienia usługi Device Provisioning i uzyskanie zakresu identyfikatorów

Wykonaj następujące kroki, aby utworzyć nowe wystąpienie usługi Device Provisioning.

1. W lewym górnym rogu Azure Portal kliknij pozycję **Utwórz zasób**.

2. W polu wyszukiwania wpisz ciąg **device provisioning**. 

3. Kliknij pozycję **Usługa IoT Hub Device Provisioning**.

4. Wypełnij formularz **Usługa IoT Hub Device Provisioning** przy użyciu następujących informacji:
    
   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa** | Dowolna unikatowa nazwa | -- | 
   | **Subskrypcja** | Twoja subskrypcja  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa). |
   | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |   

   ![Wprowadzanie podstawowych informacji o usłudze Device Provisioning Service w portalu](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Kliknij pozycję **Utwórz**. Po kilku chwilach wystąpienie usługi Device Provisioning zostaje utworzone i wyświetlana jest strona **Przegląd**.

6. Na stronie **Przegląd** nowego wystąpienia usługi skopiuj wartość **Zakres identyfikatorów** do późniejszego użycia. Ta wartość służy do określania identyfikatorów rejestracji i daje gwarancję, że identyfikator rejestracji jest unikatowy.

7. Skopiuj również wartość **Punkt końcowy usługi** do późniejszego użycia. 

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Łączenie usługi Device Provisioning z centrum IoT

Następnym krokiem jest połączenie usługi Device Provisioning z centrum IoT w celu umożliwienia usłudze IoT Hub Device Provisioning rejestrowania urządzeń w tym centrum. Usługa może aprowizować urządzenia tylko w centrach IoT połączonych z usługą Device Provisioning. Wykonaj następujące kroki.

1. Na stronie **Wszystkie zasoby** kliknij wystąpienie usługi Device Provisioning utworzone wcześniej.

2. Na stronie Usługa Device Provisioning kliknij pozycję **Połączone centra IoT Hub**.

3. Kliknij pozycję **Dodaj**.

4. Na stronie **Dodawanie linku do centrum IoT Hub** podaj następujące informacje i kliknij przycisk **Zapisz**:

    * **Subskrypcja:** upewnij się, że wybrano subskrypcję, która zawiera centrum IoT. Istnieje możliwość utworzenia linku do centrum IoT, które znajduje się innej subskrypcji.

    * **Centrum IoT:** wybierz nazwę centrum IoT, które ma zostać połączone z tym wystąpieniem usługi Device Provisioning.

    * **Zasady dostępu:** wybierz **iothubowner** jako poświadczenia używane do nawiązania połączenia z centrum IoT.

   ![Tworzenie powiązania nazwy centrum z usługą Device Provisioning Service w portalu](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ustawienie zasad alokacji w usłudze Device Provisioning

Zasada alokacji jest ustawieniem IoT Hub Device Provisioning Service, które określa sposób przypisywania urządzeń do centrum IoT Hub. Są obsługiwane trzy zasady alokacji: 

1. **Najmniejsze opóźnienie**: urządzenia są aprowizowane do centrum IoT z uwzględnieniem centrum z najmniejszym opóźnieniem do urządzenia.

2. **Dystrybucja z równymi wagami** (domyślnie): aprowizacja do każdego z połączonych centrów IoT jest jednakowo prawdopodobna. Jest to ustawienie domyślne. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie. 

3. **Konfiguracja statyczna za pośrednictwem listy rejestracji**: określenie żądanego centrum IoT na liście rejestracji ma wyższy priorytet niż zasady alokacji na poziomie usługi Device Provisioning.

Aby ustawić zasady alokacji, na stronie Usługa Device Provisioning kliknij pozycję **Zarządzanie zasadami alokacji**. Upewnij się, że dla zasad alokacji ustawiono wartość **Dystrybucja z równymi wagami** (wartość domyślną). Jeśli wprowadzisz zmiany, kliknij polecenie **Zapisz**, gdy wszystko będzie gotowe.

![Zarządzanie zasadami alokacji](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne samouczki w tej kolekcji zależą od tego samouczka. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym samouczku. Jeśli nie planujesz kontynuowania pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka.

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz wystąpienie usługi IoT Hub Device Provisioning. U góry strony **Wszystkie zasoby** kliknij polecenie **Usuń**.  

2. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. U góry strony **Wszystkie zasoby** kliknij polecenie **Usuń**.
 
## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użycie witryny Azure Portal do utworzenia usługi IoT Hub Device Provisioning i pobrania zakresu identyfikatorów
> * Tworzenie centrum IoT
> * Połączenie centrum IoT z usługą Device Provisioning
> * Ustawienie zasad alokacji w usłudze Device Provisioning

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować urządzenie pod kątem aprowizacji.

> [!div class="nextstepaction"]
> [Set up device for provisioning (Konfigurowanie urządzenia pod kątem aprowizacji)](tutorial-set-up-device.md)
