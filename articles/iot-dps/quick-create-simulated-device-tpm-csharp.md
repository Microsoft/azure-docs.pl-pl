---
title: 'Szybki start — aprowizować symulowane urządzenie TPM w celu Azure IoT Hub przy użyciu języka C #'
description: Szybki start — tworzenie i aprowizowanie symulowanego urządzenia TPM przy użyciu zestawu SDK języka C# dla Azure IoT Hub Device Provisioning Service (DPS). W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 43ad18173c96c506c0df87a01fc7452065442bc9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868667"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki start: tworzenie i aprowizować symulowane urządzenie TPM przy użyciu zestawu SDK języka C# dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Te kroki pokazują, jak symulować urządzenie TPM na maszynie deweloperskiej z systemem operacyjnym Windows przy użyciu [przykładów usługi Azure IoT dla języka C#](https://github.com/Azure-Samples/azure-iot-samples-csharp). Ten przykład obejmuje także połączenie symulowanego urządzenia z usługą IoT Hub przy użyciu usługi Device Provisioning. 

Przykład kodu używa symulatora modułu Windows TPM jako [sprzętowego modułu zabezpieczeń (HSM, hardware security module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) urządzenia. 

Jeśli nie masz jeszcze informacji na temat procesu automatycznego aprowizowania, zapoznaj się z [omówieniem aprowacji.](about-iot-dps.md#provisioning-process) Pamiętaj również, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego 

1. Upewnij się, że na maszynie jest zainstalowany zestaw [SDK platformy .NET Core 2.1](https://dotnet.microsoft.com/download) lub nowszy. 

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

1. Otwórz wiersz polecenia lub powłokę Git Bash. Sklonuj repozytorium GitHub Azure IoT Samples for C#:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Aprowizowanie urządzenia symulowanego

1. Zaloguj się w witrynie Azure Portal. Wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz usługę Device Provisioning Service. W bloku **Przegląd** zanotuj wartość **_Zakres_** identyfikatorów.

    ![Skopiuj identyfikator zakresu usługi aprowizacji z bloku portalu](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. W wierszu polecenia zmień katalogi na katalog projektu dla przykładu aprowizacji urządzenia TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Wpisz następujące polecenie, aby skompilować i uruchomić przykład aprowizacji urządzenia TPM. Zastąp wartość `<IDScope>` zakresem identyfikatorów Twojej usługi aprowizacji. 

    ```cmd
    dotnet run <IDScope>
    ```

    To polecenie spowoduje uruchomienie symulatora mikroukładu urządzenia TPM w osobnym wierszu polecenia. W systemie Windows może wystąpić alert Zabezpieczenia Windows z pytaniem, czy chcesz zezwolić Simulator.exe na komunikację w sieciach publicznych. Na potrzeby tego przykładu możesz anulować żądanie.

1. W oryginalnym oknie polecenia są wyświetlane wartości **_Klucz poręczenia,_** **_Identyfikator rejestracji_** i Sugerowany identyfikator **_urządzenia wymagany_** do rejestracji urządzenia. Zanotuj te wartości. Użyjesz tych wartości do utworzenia rejestracji indywidualnej w wystąpieniu usługi Device Provisioning. 
   > [!NOTE]
   > Nie pomyl okna zawierającego dane wyjściowe polecenia z oknem zawierającym dane wyjściowe z symulatora modułu TPM. Może być konieczne wybranie oryginalnego okna polecenia, aby wprowadzić je na pierwszy plan.

    ![Dane wyjściowe okna polecenia](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. W menu Azure Portal Device Provisioning Wybierz pozycję **Zarządzaj rejestracjami.** Wybierz **kartę Rejestracje indywidualne** i wybierz przycisk **Dodaj rejestrację indywidualną** u góry strony. 

1. W **panelu Dodawanie** rejestracji wprowadź następujące informacje:
   - Wybierz opcję **TPM** jako *Mechanizm* poświadczania tożsamości.
   - Wprowadź identyfikator *rejestracji i* klucz *poręczenia* dla urządzenia TPM z wartości zanotowych wcześniej.
   - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
   - Opcjonalnie można podać następujące informacje:
       - Wprowadź unikatowy *identyfikator urządzenia* (możesz użyć sugerowanego identyfikatora lub podać własny). Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. Jeśli zdecydujesz się go nie poświadczyć, identyfikator rejestracji zostanie użyty do zidentyfikowania urządzenia.
       - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij **przycisk** Zapisz. 

     ![Wprowadzanie informacji o rejestracji urządzenia w bloku portalu](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Po pomyślnej rejestracji *Identyfikator rejestracji* Twojego urządzenia pojawi się na liście na karcie *Indywidualne rejestracje*. 

1. Naciśnij *klawisz Enter* w oknie polecenia (tym, które wyświetliło klucz poręczenia, identyfikator rejestracji i sugerowany identyfikator urządzenia), aby zarejestrować symulowane urządzenie. **** **** **** Zwróć uwagę na komunikaty symulujące uruchamianie urządzenia i łączenie z usługą Device Provisioning Service w celu pobrania informacji z Twojego centrum IoT. 

1. Upewnij się, że urządzenie zostało zaaprowizowane. Po pomyślnym aprowizowania symulowanego urządzenia w centrum IoT połączonym z twoją usługą aprowiwizowania identyfikator urządzenia jest wyświetlany w bloku **urządzeń IoT** centrum. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Zamknij okno symulatora modułu TPM na swojej maszynie.
1. W menu po lewej stronie w Azure Portal wybierz pozycję **Wszystkie zasoby,** a następnie wybierz usługę Device Provisioning. W górnej części bloku **Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  
1. W menu po lewej stronie w Azure Portal wybierz pozycję Wszystkie **zasoby,** a następnie wybierz swoje centrum IoT. W górnej części bloku **Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie TPM na maszynie i aprowizowaliśmy je w centrum IoT przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak zarejestrować urządzenie TPM programowo, przejdź do przewodnika Szybki start na temat programowej rejestracji urządzenia TPM. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — rejestrowanie urządzenia TPM w Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
