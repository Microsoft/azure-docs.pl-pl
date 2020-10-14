---
title: 'Samouczek: łączenie kompleksowego rozwiązania'
titleSuffix: Azure Digital Twins
description: Samouczek przedstawiający kompleksowe rozwiązanie bliźniaczych reprezentacji Digital na platformie Azure, które jest sterowane danymi urządzeń.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: e7d966aa5aa4b5a498c80cab26686411dd586185
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044602"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Samouczek: Tworzenie kompleksowego rozwiązania

Aby skonfigurować kompleksowe rozwiązanie oparte na danych na żywo ze środowiska, możesz połączyć wystąpienie usługi Azure Digital bliźniaczych reprezentacji z innymi usługami platformy Azure w celu zarządzania urządzeniami i danymi.

W tym samouczku będziesz...
> [!div class="checklist"]
> * Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji
> * Zapoznaj się z przykładowym scenariuszem kompilowania i wystąpieniami wstępnie zapisanych składników
> * Korzystanie z aplikacji [Azure Functions](../azure-functions/functions-overview.md) do kierowania symulowanych danych telemetrycznych z urządzenia [IoT Hub](../iot-hub/about-iot-hub.md) do właściwości cyfrowej przędzy
> * Propaguj zmiany za pomocą **grafu bliźniaczyego**, przetwarzając powiadomienia Digital bliźniaczy z Azure Functions, punktami końcowymi i trasami

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Wprowadzenie do scenariusza tworzenia

Przykładowy projekt używany w tym samouczku reprezentuje rzeczywisty **scenariusz budowania**, zawierający piętro, pomieszczenie i termostat. Te składniki zostaną cyfrowo reprezentowane w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, które zostaną następnie połączone z [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)i dwoma [funkcjami platformy Azure](../azure-functions/functions-overview.md) w celu ułatwienia przenoszenia danych.

Poniżej znajduje się Diagram przedstawiający pełny scenariusz. 

Najpierw utworzysz wystąpienie usługi Azure Digital bliźniaczych reprezentacji (**sekcja A** na diagramie), a następnie skonfigurujesz przepływ danych telemetrycznych do Digital bliźniaczych reprezentacji (**strzałka B**), a następnie skonfigurujesz propagację danych za pomocą wykresu dwuosiowego (**strzałka C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Aby obejść ten scenariusz, będziesz korzystać ze składników wstępnie pobranej przykładowej aplikacji, która została wcześniej pobrana.

Poniżej przedstawiono składniki zaimplementowane w przykładowej aplikacji *AdtSampleApp* scenariusza:
* Uwierzytelnianie urządzeń 
* Przykłady użycia [zestawu SDK dla platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) (znaleziono w *CommandLoop.cs*)
* Interfejs konsoli do wywoływania interfejsu API Digital bliźniaczych reprezentacji platformy Azure
* *SampleClientApp* — przykładowe rozwiązanie Azure Digital bliźniaczych reprezentacji
* *SampleFunctionsApp* — aplikacja Azure Functions, która aktualizuje wykres usługi Azure Digital bliźniaczych reprezentacji w wyniku użycia telemetrii z IoT Hub i usługi Azure Digital bliźniaczych reprezentacji

Przykładowy projekt zawiera również interaktywny składnik autoryzacji. Za każdym razem, gdy uruchamiasz projekt, zostanie otwarte okno przeglądarki z monitem o zalogowanie się przy użyciu konta platformy Azure.

### <a name="instantiate-the-pre-created-twin-graph"></a>Tworzenie wystąpienia grafu wstępnie utworzonego

Najpierw użyjemy rozwiązania *AdtSampleApp* z przykładowego projektu, aby skompilować cyfrowy bliźniaczych reprezentacji, kompleksowy scenariusz (**sekcja A**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

W oknie programu Visual Studio, w którym jest otwarty projekt _**AdtE2ESample**_ , Uruchom projekt przy użyciu tego przycisku na pasku narzędzi:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Otworzy się okno konsoli, przeprowadzisz uwierzytelnianie i poczekasz na polecenie. W tej konsoli Uruchom następne polecenie, aby utworzyć wystąpienie przykładowego rozwiązania Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Jeśli masz już bliźniaczych reprezentacji cyfrowe i relacje w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, uruchomienie tego polecenia spowoduje jego usunięcie i zastąpienie ich bliźniaczych reprezentacji i relacjami dla przykładowego scenariusza.

```cmd/sh
SetupBuildingScenario
```

Dane wyjściowe tego polecenia to seria komunikatów potwierdzających, ponieważ trzy [**cyfrowe bliźniaczych reprezentacji**](concepts-twins-graph.md) są tworzone i połączone w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji: piętro o nazwie *floor1*, pokoju o nazwie *room21*oraz czujnik temperatury o nazwie *thermostat67*. Te cyfrowe bliźniaczych reprezentacji reprezentują jednostki, które byłyby dostępne w środowisku rzeczywistym.

Są one połączone przez relacje z poniższym [**wykresem bliźniaczym**](concepts-twins-graph.md). Wykres dwuosiowy reprezentuje środowisko jako całość, w tym sposób, w jaki jednostki współdziałają ze sobą i odnoszą się do siebie.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)" border="false":::

Możesz sprawdzić bliźniaczych reprezentacji, które zostały utworzone, uruchamiając następujące polecenie, które wysyła zapytanie do połączonego wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla wszystkich takich cyfrowych bliźniaczych reprezentacji:

```cmd/sh
Query
```

>[!TIP]
> Ta uproszczona Metoda jest udostępniana jako część projektu _**AdtE2ESample**_ . Poza kontekstem tego przykładowego kodu można w dowolnym momencie wykonać zapytanie o wszystkie bliźniaczych reprezentacji w wystąpieniu przy użyciu [interfejsów API zapytań](how-to-use-apis-sdks.md) lub [poleceń interfejsu wiersza polecenia](how-to-use-cli.md).
>
> Oto pełna treść zapytania, aby pobrać wszystkie bliźniaczych reprezentacji cyfrowe w wystąpieniu:
> 
> ```sql
> SELECT *
> FROM DIGITALTWINS
> ``` 

Następnie można zatrzymać uruchamianie projektu. Zachowaj otwarte rozwiązanie w programie Visual Studio, ponieważ będziesz nadal korzystać z niego w całym samouczku.

## <a name="set-up-the-sample-function-app"></a>Konfigurowanie przykładowej aplikacji funkcji

Następnym krokiem jest skonfigurowanie [aplikacji Azure Functions](../azure-functions/functions-overview.md) , która będzie używana w tym samouczku do przetwarzania danych. Aplikacja funkcji, *SampleFunctionsApp*, zawiera dwie funkcje:
* *ProcessHubToDTEvents*: przetwarza przychodzące dane IoT Hub i aktualizuje odpowiednio usługę Azure Digital bliźniaczych reprezentacji
* *ProcessDTRoutedData*: przetwarza dane z bliźniaczych reprezentacji Digital i aktualizuje odpowiednio bliźniaczych reprezentacji nadrzędny w usłudze Azure Digital bliźniaczych reprezentacji

W tej sekcji zostanie opublikowana wstępnie zapisana aplikacja funkcji i upewnij się, że aplikacja funkcji może uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, przypisując jej tożsamość Azure Active Directory (Azure AD). Wykonanie tych kroków umożliwi pozostałej części samouczka Korzystanie z funkcji wewnątrz aplikacji funkcji. 

Wróć do okna programu Visual Studio, w którym projekt _**AdtE2ESample**_ jest otwarty, aplikacja funkcji znajduje się w pliku projektu _**SampleFunctionsApp**_ . Można go wyświetlić w okienku *Eksplorator rozwiązań* .

### <a name="update-dependencies"></a>Aktualizowanie zależności

Przed opublikowaniem aplikacji dobrym pomysłem jest upewnienie się, że Twoje zależności są aktualne, aby upewnić się, że masz najnowszą wersję wszystkich dołączonych pakietów.

W okienku *Eksplorator rozwiązań* rozwiń pozycję *SampleFunctionsApp > zależności*. Wybierz pozycję *pakiety* , a następnie wybierz pozycję *Zarządzaj pakietami NuGet.*...

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)" border="false":::

Spowoduje to otwarcie Menedżera pakietów NuGet. Wybierz kartę *aktualizacje* , a jeśli istnieją pakiety do zaktualizowania, zaznacz pole wyboru *wszystkie pakiety*. Następnie kliknij przycisk *Aktualizuj*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

### <a name="publish-the-app"></a>Publikowanie aplikacji

Wróć do okna programu Visual Studio, w którym otwarty jest projekt _**AdtE2ESample**_ , w okienku *Eksplorator rozwiązań* kliknij prawym przyciskiem myszy plik projektu _**SampleFunctionsApp**_ i wybierz polecenie **Publikuj**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Na poniższej stronie *Publikuj* pozostaw domyślny wybór dla **platformy Azure** i kliknij przycisk *dalej*. 

Dla konkretnego obiektu docelowego wybierz pozycję **Azure aplikacja funkcji (Windows)** i naciśnij przycisk *dalej*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Na stronie *wystąpienie funkcji* wybierz swoją subskrypcję. Powinno to spowodować Wypełnienie pola *grupami zasobów* w ramach subskrypcji.

Wybierz grupę zasobów wystąpienia i naciśnij przycisk *+ Utwórz nową funkcję platformy Azure.*..

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

W *aplikacja funkcji (Windows) — Utwórz nowe* okno, wypełnij pola w następujący sposób:
* **Nazwa** to nazwa planu zużycia, który będzie używany przez platformę Azure do hostowania aplikacji Azure Functions. Będzie to również nazwa aplikacji funkcji, która zawiera rzeczywistą funkcję. Możesz wybrać własną unikatową wartość lub pozostawić domyślną sugestię.
* Upewnij się, że **subskrypcja** jest zgodna z subskrypcją, której chcesz użyć 
* Upewnij się, że **Grupa zasobów** jest grupą zasobów, której chcesz użyć
* Pozostaw **Typ planu** jako *zużycie*
* Wybierz **lokalizację** pasującą do lokalizacji grupy zasobów
* Utwórz nowy zasób **usługi Azure Storage** przy użyciu nowego linku. *..* . Ustaw lokalizację zgodną z grupą zasobów, użyj innych wartości domyślnych i naciśnij przycisk OK.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Następnie wybierz przycisk **Utwórz**.

Powinno to spowodować powrót do strony *wystąpienia funkcji* , gdzie Nowa aplikacja funkcji jest teraz widoczna poniżej grupy zasobów. *Koniec*trafień.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

W okienku *Publikowanie* , które zostanie otwarte z powrotem w głównym oknie programu Visual Studio, sprawdź, czy wszystkie informacje są poprawne, a następnie wybierz pozycję **Publikuj**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

> [!NOTE]
> Jeśli zobaczysz podręczny ekran podobny do tego: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)" border="false":::
> Wybierz pozycję **próba pobrania poświadczeń z platformy Azure** i **Zapisz**.
>
> Jeśli zostanie wyświetlone ostrzeżenie, aby *uaktualnić wersję funkcji na platformie Azure* lub że *wersja środowiska uruchomieniowego funkcji jest niezgodna z wersją działającą na platformie Azure*:
>
> Postępuj zgodnie z monitami, aby przeprowadzić uaktualnienie do najnowszej wersji środowiska uruchomieniowego Azure Functions. Ten problem może wystąpić, jeśli używasz starszej wersji programu Visual Studio niż zalecana w sekcji *wymagania wstępne* na początku tego samouczka.

### <a name="assign-permissions-to-the-function-app"></a>Przypisywanie uprawnień do aplikacji funkcji

Aby umożliwić aplikacji funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji, następnym krokiem jest skonfigurowanie ustawienia aplikacji, przypisanie aplikacji do tożsamości usługi Azure AD zarządzanego przez system i nadanie tej tożsamości roli *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Ta rola jest wymagana dla każdego użytkownika lub funkcji, które chcą wykonywać wiele działań na płaszczyźnie danych w wystąpieniu. Więcej informacji na temat przypisań zabezpieczeń i ról można znaleźć w tematach [*: Security for Azure Digital bliźniaczych reprezentacji Solutions*](concepts-security.md).

W Azure Cloud Shell Użyj następującego polecenia, aby ustawić ustawienie aplikacji, które będzie używane przez aplikację funkcji do odwoływania się do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

Użyj następującego polecenia, aby utworzyć tożsamość zarządzaną przez system. Zwróć uwagę na pole *principalId* w danych wyjściowych.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Użyj wartości *principalId* z danych wyjściowych w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli *właściciela usługi Azure Digital bliźniaczych reprezentacji (wersja zapoznawcza)* dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli. Aplikacja funkcji ma teraz uprawnienia dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Przetwarzaj symulowane dane telemetryczne z urządzenia IoT Hub

Usługa Azure Digital bliźniaczych reprezentacji Graph ma być oparta na danych telemetrycznych z rzeczywistych urządzeń. 

W tym kroku nastąpi podłączenie symulowanego termostatu zarejestrowanego w [IoT Hub](../iot-hub/about-iot-hub.md) do cyfrowej przędzy, która reprezentuje ją w usłudze Azure Digital bliźniaczych reprezentacji. Gdy symulowane urządzenie emituje telemetrię, dane będą kierowane przez funkcję *ProcessHubToDTEvents* platformy Azure, która wyzwala odpowiednią aktualizację w wieloosiowu cyfrowym. W ten sposób cyfrowe sznurki są aktualne na bieżąco z danymi rzeczywistego urządzenia. W przypadku usługi Azure Digital bliźniaczych reprezentacji proces kierowania danych zdarzeń z jednego miejsca do innego jest nazywany [**zdarzeniami routingu**](concepts-route-events.md).

Dzieje się tak w tej części scenariusza kompleksowe (**strzałka B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Oto akcje, które należy wykonać w celu skonfigurowania tego połączenia urządzenia:
1. Utwórz Centrum IoT Hub, które będzie zarządzać urządzeniem symulowanym
2. Połącz Centrum IoT Hub z odpowiednią funkcją platformy Azure, konfigurując subskrypcję zdarzeń
3. Rejestrowanie symulowanego urządzenia w usłudze IoT Hub
4. Uruchamianie symulowanego urządzenia i generowanie telemetrii
5. Wysyłanie zapytań do usługi Azure Digital bliźniaczych reprezentacji w celu wyświetlenia wyników na żywo

### <a name="create-an-iot-hub-instance"></a>Tworzenie wystąpienia usługi IoT Hub

Usługa Azure Digital bliźniaczych reprezentacji została zaprojektowana tak, aby działała wraz z [IoT Hub](../iot-hub/about-iot-hub.md), usługą platformy Azure do zarządzania urządzeniami i ich danymi. W tym kroku utworzysz Centrum IoT Hub, które będzie zarządzać przykładowym urządzeniem w tym samouczku.

W Azure Cloud Shell Użyj tego polecenia, aby utworzyć nowe centrum IoT Hub:

```azurecli
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Dane wyjściowe tego polecenia to informacje o utworzonym Centrum IoT Hub.

Zapisz nazwę nadaną do centrum IoT Hub. Użyjesz jej później.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Łączenie Centrum IoT Hub z funkcją platformy Azure

Następnie połącz Centrum IoT Hub z funkcją *ProcessHubToDTEvents* platformy Azure w opublikowanej wcześniej aplikacji funkcji, aby dane mogły być przesyłane z urządzenia w IoT Hub za pośrednictwem funkcji, która aktualizuje usługę Azure Digital bliźniaczych reprezentacji.

W tym celu utworzysz **subskrypcję zdarzeń** na IoT Hub przy użyciu funkcji platformy Azure jako punktu końcowego. Ta "subskrybuje" funkcję do zdarzeń występujących w IoT Hub.

W [Azure Portal](https://portal.azure.com/)przejdź do nowo utworzonego Centrum IoT Hub, wyszukując jego nazwę na górnym pasku wyszukiwania. Wybierz pozycję *zdarzenia* z menu centrum i wybierz pozycję *+ subskrypcja zdarzeń*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Spowoduje to wyświetlenie strony *Tworzenie subskrypcji zdarzeń* .

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Wypełnij pola w następujący sposób (pola wypełnione domyślnie nie są wymienione):
* *szczegóły*  >  subskrypcji zdarzeń **Nazwa**: nadaj nazwę subskrypcji zdarzenia.
* *Szczegóły tematu*  >  **Nazwa tematu systemu**: Podaj nazwę dla tematu systemowego. 
* *typy zdarzeń*  >  **Filtruj do typów zdarzeń**: wybierz pozycję *Telemetria urządzenia* z opcji menu.
* *szczegóły*  >  punktu końcowego **Typ punktu końcowego**: wybierz pozycję *Funkcja platformy Azure* z opcji menu.
* *szczegóły*  >  punktu końcowego **Punkt końcowy**: naciśnij link *Wybierz punkt końcowy* . Spowoduje to otwarcie okna *Wybieranie funkcji platformy Azure* : :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)" border="false":::
    - Wypełnij swoją **subskrypcję**, **grupę zasobów**, **funkcję App** and **Function** (*ProcessHubToDTEvents*). Niektóre z tych elementów mogą być wypełniane automatycznie po wybraniu subskrypcji.
    - Kliknij przycisk **Potwierdź wybór**.

Wróć na stronę *Tworzenie subskrypcji zdarzeń* , kliknij przycisk **Utwórz**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Rejestrowanie symulowanego urządzenia za pomocą IoT Hub 

Ta sekcja tworzy reprezentację urządzenia w IoT Hub z IDENTYFIKATORem *thermostat67*. Symulowane urządzenie nawiąże połączenie z tą usługą i polega na tym, że zdarzenia telemetryczne przechodzą z urządzenia do IoT Hub, gdzie funkcja subskrybowanej platformy Azure z poprzedniego kroku nasłuchuje, jest gotowa do pobrania zdarzeń i kontynuowania przetwarzania.

W Azure Cloud Shell Utwórz urządzenie w IoT Hub za pomocą następującego polecenia:

```azurecli
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Dane wyjściowe to informacje o utworzonym urządzeniu.

### <a name="configure-and-run-the-simulation"></a>Konfigurowanie i uruchamianie symulacji

Następnie skonfiguruj symulator urządzenia, aby wysyłał dane do wystąpienia IoT Hub.

Zacznij od pobrania *parametrów połączenia usługi IoT Hub* za pomocą tego polecenia:

```azurecli
az iot hub connection-string show -n <your-IoT-hub-name>
```

Następnie Pobierz *Parametry połączenia z urządzeniem* za pomocą tego polecenia:

```azurecli
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Te wartości zostaną podłączone do kodu symulatora urządzeń w projekcie lokalnym, aby połączyć symulatora z tym urządzeniem IoT Hub i IoT Hub.

W nowym oknie programu Visual Studio Otwórz program (z folderu pobranego rozwiązania) _symulatora urządzeń > **DeviceSimulator. sln**_.

>[!NOTE]
> Teraz powinny być dostępne dwa okna programu Visual Studio — jeden z _**DeviceSimulator. sln**_ i jeden z wcześniejszych elementów z _**AdtE2ESample. sln**_.

W okienku *Eksplorator rozwiązań* w tym nowym oknie programu Visual Studio wybierz pozycję _DeviceSimulator/**AzureIoTHub.cs** _ , aby otworzyć ją w oknie edycji. Zmień następujące wartości parametrów połączenia na zebrane powyżej wartości:

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

Zapisz plik.

Teraz, aby wyświetlić wyniki symulacji danych, które zostały skonfigurowane, Uruchom projekt **DeviceSimulator** za pomocą tego przycisku na pasku narzędzi:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Zostanie otwarte okno konsoli zawierające symulowane komunikaty telemetryczne dotyczące temperatury. Są one wysyłane do IoT Hub, gdzie są następnie odbierane i przetwarzane przez funkcję platformy Azure.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Nie musisz wykonywać żadnych innych czynności w tej konsoli, ale pozostaw to działanie w trakcie wykonywania następnych kroków.

### <a name="see-the-results-in-azure-digital-twins"></a>Zobacz wyniki w usłudze Azure Digital bliźniaczych reprezentacji

Funkcja *ProcessHubToDTEvents* , która została opublikowana wcześniej nasłuchuje danych IoT Hub i wywołuje interfejs API Digital bliźniaczych reprezentacji platformy Azure w celu zaktualizowania właściwości *temperatury* na sznurze *thermostat67* .

Aby wyświetlić dane ze strony usługi Azure Digital bliźniaczych reprezentacji, przejdź do okna programu Visual Studio, w którym projekt _**AdtE2ESample**_ jest otwarty i Uruchom projekt.

W otwartym oknie konsoli projektu uruchom następujące polecenie, aby uzyskać temperatury zgłaszane przez *thermostat67*cyfrowej przędzy:

```cmd
ObserveProperties thermostat67 Temperature
```

Zaktualizowane temperatury na żywo *z wystąpienia usługi Azure Digital bliźniaczych reprezentacji* są wyświetlane w konsoli co 10 sekund.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Po sprawdzeniu, że działanie zostało zakończone pomyślnie, można zatrzymać uruchamianie obu projektów. Pozostaw otwarte okna programu Visual Studio, ponieważ będziesz nadal korzystać z nich w pozostałej części tego samouczka.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Propagowanie zdarzeń bliźniaczych reprezentacji cyfrowych platformy Azure za pomocą grafu

Do tej pory w tym samouczku pokazano, jak można aktualizować usługę Azure Digital bliźniaczych reprezentacji z danych urządzenia zewnętrznego. Następnie zobaczysz, jak zmiany w jednej cyfrowej przędzy mogą być propagowane za pośrednictwem grafu Digital bliźniaczych reprezentacji Azure — innymi słowy, jak zaktualizować bliźniaczych reprezentacji z danych wewnętrznych usługi.

W tym celu należy użyć funkcji *ProcessDTRoutedData* platformy Azure, aby zaktualizować *dwuosiową* , gdy zostanie zaktualizowane połączenie z podłączoną *termostatem* . Dzieje się tak w tej części scenariusza kompleksowe (**strzałka C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Poniżej przedstawiono akcje, które należy wykonać w celu skonfigurowania tego przepływu danych:
1. Utwórz punkt końcowy usługi Azure Digital bliźniaczych reprezentacji, który łączy wystąpienie z Event Grid
2. Konfigurowanie trasy w ramach usługi Azure Digital bliźniaczych reprezentacji do wysyłania zdarzeń zmiany właściwości przędzy do punktu końcowego
3. Wdróż aplikację Azure Functions, która nasłuchuje (za pomocą [Event Grid](../event-grid/overview.md)) w punkcie końcowym i odpowiednio aktualizuje inne bliźniaczych reprezentacji
4. Uruchamianie symulowanego urządzenia i zapytanie usługi Azure Digital bliźniaczych reprezentacji w celu wyświetlenia wyników na żywo

### <a name="set-up-endpoint"></a>Konfigurowanie punktu końcowego

[Event Grid](../event-grid/overview.md) to usługa platformy Azure, która ułatwia kierowanie i dostarczanie zdarzeń pochodzących z usług platformy Azure do innych miejsc na platformie Azure. Można utworzyć temat usługi [Event Grid](../event-grid/concepts.md) , aby zbierać pewne zdarzenia ze źródła, a następnie Subskrybenci mogą nasłuchiwać w temacie, aby otrzymywać zdarzenia w miarę ich tworzenia.

W tej sekcji utworzysz temat usługi Event Grid, a następnie utworzysz punkt końcowy w usłudze Azure Digital bliźniaczych reprezentacji, który wskazuje (wysyła zdarzenia) do tego tematu. 

W Azure Cloud Shell Uruchom następujące polecenie, aby utworzyć temat siatki zdarzeń:

```azurecli
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Aby uzyskać listę nazw regionów platformy Azure, które mogą być przekazywane do poleceń w interfejsie wiersza polecenia platformy Azure, uruchom następujące polecenie:
> ```azurecli
> az account list-locations -o table
> ```

Dane wyjściowe tego polecenia to informacje dotyczące utworzonego tematu usługi Event Grid.

Następnie Utwórz punkt końcowy usługi Azure Digital bliźniaczych reprezentacji wskazujący poświęcony usłudze Event Grid. Użyj poniższego polecenia, wypełniając pola zastępcze w razie potrzeby:

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Dane wyjściowe tego polecenia to informacje o utworzonym punkcie końcowym.

Możesz również sprawdzić, czy tworzenie punktu końcowego zakończyło się pomyślnie, uruchamiając następujące polecenie, aby wykonać zapytanie dotyczące wystąpienia usługi Azure Digital bliźniaczych reprezentacji dla tego punktu końcowego:

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Poszukaj `provisioningState` pola w danych wyjściowych i sprawdź, czy wartość to "powodzenie". Może również powiedzieć "Inicjowanie obsługi", co oznacza, że punkt końcowy jest nadal tworzony. W takim przypadku poczekaj kilka sekund i ponownie uruchom polecenie, aby sprawdzić, czy zakończyło się pomyślnie.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Zapisz nazwy, które zostały nadaną w temacie usługi Event Grid, oraz punkt końcowy bliźniaczych reprezentacji Digital Azure. Będziesz ich używać później.

### <a name="set-up-route"></a>Konfigurowanie trasy

Następnie utwórz trasę usługi Azure Digital bliźniaczych reprezentacji, która wysyła zdarzenia do właśnie utworzonego punktu końcowego usługi Azure Digital bliźniaczych reprezentacji.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Danymi wyjściowymi tego polecenia są pewne informacje o tworzonej trasie.

>[!NOTE]
>Aby można było skonfigurować trasę zdarzeń, która używa tych punktów końcowych (z poprzedniego kroku), należy zakończyć Inicjowanie obsługi. Jeśli Tworzenie trasy nie powiedzie się, ponieważ punkty końcowe nie są gotowe, odczekaj kilka minut i spróbuj ponownie.

#### <a name="connect-the-function-to-event-grid"></a>Połącz funkcję z Event Grid

Następnie Zasubskrybuj funkcję *ProcessDTRoutedData* platformy Azure w utworzonym wcześniej temacie usługi Event Grid, aby dane telemetryczne mogły być przesyłane z sznurów *thermostat67ych* za pośrednictwem usługi Event Grid do funkcji, która powraca do programu Azure Digital bliźniaczych reprezentacji i odpowiednio aktualizuje siatkę *room21* .

W tym celu utworzysz **subskrypcję Event gridową** z tematu usługi Event Grid do funkcji *ProcessDTRoutedData* platformy Azure jako punktu końcowego.

W [Azure Portal](https://portal.azure.com/)przejdź do tematu usługi Event Grid, wyszukując jego nazwę na górnym pasku wyszukiwania. Wybierz pozycję *+ Subskrypcja zdarzeń*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Kroki tworzenia tej subskrypcji zdarzeń są podobne do zasubskrybowania pierwszej funkcji platformy Azure w celu IoT Hub wcześniejszej części tego samouczka. Tym razem nie musisz określać danych *telemetrycznych urządzenia* jako typu zdarzenia do nasłuchiwania i nawiązać połączenie z inną funkcją platformy Azure.

Na stronie *Tworzenie subskrypcji zdarzeń* Wypełnij pola w następujący sposób (pola wypełnione domyślnie nie są wymienione):
* *szczegóły*  >  subskrypcji zdarzeń **Nazwa**: nadaj nazwę subskrypcji zdarzenia.
* *szczegóły*  >  punktu końcowego **Typ punktu końcowego**: wybierz pozycję *Funkcja platformy Azure* z opcji menu.
* *szczegóły*  >  punktu końcowego **Punkt końcowy**: naciśnij link *Wybierz punkt końcowy* . Spowoduje to otwarcie okna *Wybierz funkcję platformy Azure* :
    - Wypełnij swoją **subskrypcję**, **grupę zasobów**, **funkcję App** and **Function** (*ProcessDTRoutedData*). Niektóre z tych elementów mogą być wypełniane automatycznie po wybraniu subskrypcji.
    - Kliknij przycisk **Potwierdź wybór**.

Wróć na stronę *Tworzenie subskrypcji zdarzeń* , kliknij przycisk **Utwórz**.

### <a name="run-the-simulation-and-see-the-results"></a>Uruchamianie symulacji i wyświetlanie wyników

Teraz można uruchomić symulator urządzenia, aby uruchomić nowy przepływ zdarzeń, który został skonfigurowany. Przejdź do okna programu Visual Studio, w którym jest otwarty projekt _**DeviceSimulator**_ i Uruchom projekt.

Podobnie jak w przypadku uruchomienia symulatora urządzenia wcześniej zostanie otwarte okno konsoli, w którym będą wyświetlane symulowane komunikaty telemetryczne dotyczące temperatury. Te zdarzenia przechodzą przez skonfigurowany wcześniej przepływ, aby zaktualizować *thermostat67ą* sznurek, a następnie przechodząc przez nowo skonfigurowany przepływ, aby zaktualizować dwuosiowy *room21* .

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Nie musisz wykonywać żadnych innych czynności w tej konsoli, ale pozostaw to działanie w trakcie wykonywania następnych kroków.

Aby wyświetlić dane ze strony usługi Azure Digital bliźniaczych reprezentacji, przejdź do okna programu Visual Studio, w którym jest otwarty projekt _**AdtE2ESample**_ , a następnie Uruchom projekt.

W otwartym oknie konsoli projektu uruchom następujące polecenie, aby uzyskać temperaturę raportowaną przez **zarówno** Digital bliźniaczy *thermostat67* , jak i Digital bliźniaczy *room21*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Zaktualizowane temperatury na żywo *z wystąpienia usługi Azure Digital bliźniaczych reprezentacji* są wyświetlane w konsoli co 10 sekund. Należy zauważyć, że temperatura *room21* jest aktualizowana w celu dopasowania do aktualizacji *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

Po sprawdzeniu, że działanie zostało zakończone pomyślnie, można zatrzymać uruchamianie obu projektów. Możesz również zamknąć okna programu Visual Studio, ponieważ samouczek jest teraz gotowy.

## <a name="review"></a>Przegląd

Oto przegląd scenariusza, który został utworzony w tym samouczku.

1. Cyfrowe wystąpienie usługi Azure bliźniaczych reprezentacji cyfrowo reprezentuje piętro, pomieszczenie i termostat (reprezentowane przez **sekcję a** na poniższym diagramie)
2. Dane telemetryczne urządzenia symulowanego są wysyłane do IoT Hub, gdzie funkcja *ProcessHubToDTEvents* systemu Azure nasłuchuje zdarzeń telemetrii. Funkcja *ProcessHubToDTEvents* systemu Azure używa informacji w tych zdarzeniach, aby ustawić właściwość *temperatury* w *thermostat67* (**strzałka B** na diagramie).
3. Zdarzenia zmiany właściwości w usłudze Azure Digital bliźniaczych reprezentacji są kierowane do tematu usługi Event Grid, gdzie funkcja *ProcessDTRoutedData* platformy Azure nasłuchuje zdarzeń. Funkcja *ProcessDTRoutedData* systemu Azure używa informacji w tych zdarzeniach, aby ustawić właściwość *temperatury* w *room21* (**strzałka C** na diagramie).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Ilustracja przedstawiająca pełny scenariusz tworzenia. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C)":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć. 

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Spowoduje to usunięcie grupy zasobów. wystąpienie usługi Azure Digital bliźniaczych reprezentacji; Centrum IoT i Rejestracja urządzenia Hub; temat dotyczący siatki zdarzeń i skojarzonych subskrypcji; i aplikacja Azure Functions, w tym funkcje i powiązane zasoby, takie jak magazyn.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli
az group delete --name <your-resource-group>
```

Następnie usuń rejestrację aplikacji usługi Azure AD utworzoną dla aplikacji klienckiej przy użyciu tego polecenia:

```azurecli
az ad app delete --id <your-application-ID>
```

Na koniec Usuń folder przykładowego projektu pobrany na komputer lokalny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano tworzenie kompleksowego scenariusza, który pokazuje, że usługa Azure Digital bliźniaczych reprezentacji jest obsługiwana przez dane urządzenia na żywo.

Następnie zacznij od dokumentacji koncepcji, aby dowiedzieć się więcej o elementach, z którymi pracujesz w samouczku:

> [!div class="nextstepaction"]
> [*Koncepcje: modele niestandardowe*](concepts-models.md)