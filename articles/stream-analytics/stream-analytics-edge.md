---
title: Azure Stream Analytics na urządzeniach IoT Edge
description: Twórz zadania brzegowe w Azure Stream Analytics i wdrażaj je na urządzeniach z Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: 7a084b2d0582f53d4372ba3332194629ad29a4ec
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041873"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach IoT Edge
 
Usługa Azure Stream Analytics (ASA) w usłudze IoT Edge umożliwia deweloperom wdrażanie analizy bliskiej czasowi rzeczywistemu bliżej urządzeń Internetu rzeczy, aby w pełni wykorzystać dane wygenerowane na urządzeniach. Usługę Azure Stream Analytics zaprojektowano pod kątem małych opóźnień, odporności, wydajnego wykorzystywania przepustowości i zgodności. Przedsiębiorstwa mogą teraz wdrażać logikę sterowania blisko operacji przemysłowych i uzupełniać analizę danych big data realizowaną w chmurze.  

Azure Stream Analytics na urządzeniach IoT Edge działa w ramach struktury [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) . Po utworzeniu zadania w usłudze ASA można wdrożyć je i zarządzać nimi za pomocą IoT Hub.

## <a name="scenarios"></a>Scenariusze
![Diagram wysokiego poziomu IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Polecenie i kontrola o małym opóźnieniu** : na przykład systemy bezpieczeństwa produkcji muszą reagować na dane operacyjne z bardzo małym opóźnieniem. Dzięki usłudze ASA na IoT Edge można analizować dane czujników niemal w czasie rzeczywistym i wydawać polecenia w przypadku wykrycia anomalii w celu zatrzymania alertów komputera lub wyzwalacza.
*   **Ograniczona łączność z chmurą** : systemy o znaczeniu krytycznym, takie jak zdalny sprzęt górniczy, połączone statki lub przechodzenie na przybrzeżne, muszą analizować i reagować na dane nawet wtedy, gdy łączność z chmurą jest nieprzerwana. W systemie ASA logika przesyłania strumieniowego działa niezależnie od łączności sieciowej i można wybrać operacje wysyłane do chmury w celu dalszej obróbki lub magazynu.
* **Ograniczona przepustowość** : ilość danych wytworzonych przez aparaty Jet lub połączone samochody mogą być tak duże, że dane muszą być przefiltrowane lub wstępnie przetworzone przed wysłaniem ich do chmury. Przy użyciu ASA można filtrować lub agregowanie danych, które muszą być wysyłane do chmury.
* **Zgodność** : zgodność z przepisami może wymagać, aby pewne dane były lokalnie anonimowe lub zagregowane przed wysłaniem ich do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Zadania brzegowe w Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co to jest zadanie "Edge"?

Zadania programu ASA Edge są uruchamiane w kontenerach wdrożonych na [Azure IoT Edge urządzeniach](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Składają się one z dwóch części:
1.  Część w chmurze odpowiedzialna za definicję zadania: Użytkownicy definiują dane wejściowe, wyjściowe, zapytania i inne ustawienia (zdarzenia poza kolejnością itp.) w chmurze.
2.  Moduł uruchomiony na urządzeniach IoT. Zawiera aparat ASA i otrzymuje definicję zadania z chmury. 

Funkcja ASA używa IoT Hub do wdrażania zadań brzegowych na urządzeniach. Więcej informacji na temat [wdrażania IoT Edge można znaleźć tutaj](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Zadanie Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instrukcje instalacji
Kroki wysokiego poziomu opisano w poniższej tabeli. Więcej szczegółów znajduje się w poniższych sekcjach.

| Krok | Uwagi |
| --- | --- |
| **Tworzenie kontenera magazynu** | Kontenery magazynu są używane do zapisywania definicji zadania, gdzie są dostępne dla urządzeń IoT. <br>  Można ponownie użyć dowolnego istniejącego kontenera magazynu. |
| **Tworzenie zadania ASA Edge** | Utwórz nowe zadanie, wybierz pozycję **Edge** jako **środowisko hostingu** . <br> Te zadania są tworzone/zarządzane z chmury i uruchamiane na własnych urządzeniach IoT Edge. |
| **Skonfiguruj środowisko IoT Edge na urządzeniach** | Instrukcje dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).|
| **Wdrażanie ASA na urządzeniach IoT Edge** | Definicja zadania ASA zostanie wyeksportowana do kontenera magazynu utworzonego wcześniej. |

W [tym samouczku krok po kroku](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) można wdrożyć pierwsze zadanie ASA na IoT Edge. Poniższy film wideo powinien pomóc w zrozumieniu procesu uruchamiania Stream Analytics zadania na urządzeniu z usługą IoT Edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
Kontener magazynu jest wymagany w celu wyeksportowania skompilowanego zapytania ASA i konfiguracji zadania. Służy do konfigurowania obrazu platformy Docker z określonym zapytaniem. 
1. Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) , aby utworzyć konto magazynu na podstawie Azure Portal. Możesz zachować wszystkie opcje domyślne, aby użyć tego konta z ASA.
2. Na nowo utworzonym koncie magazynu Utwórz kontener magazynu obiektów blob:
    1. Kliknij obiekt **BLOB** , a następnie pozycję **+ Container** . 
    2. Wprowadź nazwę i Zachowaj kontener jako **prywatny** .

#### <a name="create-an-asa-edge-job"></a>Tworzenie zadania ASA Edge
> [!Note]
> Ten samouczek koncentruje się na tworzeniu zadania ASA przy użyciu Azure Portal. Możesz również [użyć wtyczki programu Visual Studio, aby utworzyć zadanie ASA Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. W Azure Portal Utwórz nowe "zadanie Stream Analytics". [Link bezpośredni do utworzenia nowego zadania asa w tym miejscu](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na ekranie Tworzenie wybierz pozycję **Edge** jako **środowisko hostingu** (zobacz poniższy obraz).

   ![Utwórz zadanie Stream Analytics na krawędzi](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definicja zadania
    1. **Zdefiniuj strumienie wejściowe** . Zdefiniuj jeden lub kilka strumieni danych wejściowych dla danego zadania.
    2. Zdefiniuj dane referencyjne (opcjonalnie).
    3. **Zdefiniuj strumienie wyjściowe** . Zdefiniuj jeden lub kilka strumieni wyjściowych dla zadania. 
    4. **Zdefiniuj zapytanie** . Zdefiniuj zapytanie ASA w chmurze przy użyciu edytora wbudowanego. Kompilator automatycznie sprawdza składnię z włączoną funkcją ASA Edge. Możesz również przetestować zapytanie, przekazując przykładowe dane. 

4. Ustaw informacje o kontenerze magazynu w menu **ustawienia IoT Edge** .

5. Ustaw ustawienia opcjonalne
    1. **Porządkowanie zdarzeń** . Zasady niezwiązane z kolejnością można skonfigurować w portalu. Dokumentacja jest dostępna [tutaj](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Ustawienia regionalne** . Ustaw format internalization.



> [!Note]
> Po utworzeniu wdrożenia program ASA Eksportuje definicję zadania do kontenera magazynu. Ta definicja zadania pozostaje taka sama w czasie trwania wdrożenia. W związku z tym, jeśli chcesz zaktualizować zadanie uruchomione na krawędzi, musisz edytować zadanie w ASA, a następnie utworzyć nowe wdrożenie w IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Skonfiguruj środowisko IoT Edge na urządzeniach
Zadania brzegowe można wdrożyć na urządzeniach z systemem Azure IoT Edge.
W tym celu należy wykonać następujące czynności:
- Utwórz Centrum IoT Hub.
- Zainstaluj program Docker i środowisko uruchomieniowe IoT Edge na urządzeniach brzegowych.
- Ustaw urządzenia jako **urządzenia IoT Edge** w IoT Hub.

Te kroki są opisane w dokumentacji IoT Edge dla [systemu Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) lub [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Wdrożenie ASA na urządzeniach, na których IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Dodawanie ASA do wdrożenia
- W Azure Portal Otwórz IoT Hub, przejdź do **IoT Edge** i kliknij urządzenie docelowe dla tego wdrożenia.
- Wybierz pozycję **Ustaw moduły** , a następnie wybierz pozycję **+ Dodaj** i wybierz pozycję **Azure Stream Analytics module** .
- Wybierz utworzoną subskrypcję i zadanie ASA Edge. Kliknij pozycję Zapisz.
![Dodawanie modułu ASA do wdrożenia](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> W tym kroku usługa ASA tworzy folder o nazwie "EdgeJobs" w kontenerze magazynu (jeśli jeszcze nie istnieje). Dla każdego wdrożenia tworzony jest nowy podfolder w folderze "EdgeJobs".
> Po wdrożeniu zadania do IoT Edge urządzenia ASA tworzy sygnaturę dostępu współdzielonego (SAS) dla pliku definicji zadania. Klucz sygnatury dostępu współdzielonego jest bezpiecznie przesyłany do urządzeń IoT Edge przy użyciu sznurka urządzenia. Wygaśnięcie tego klucza wynosi trzy lata od dnia jego utworzenia. Po zaktualizowaniu zadania IoT Edge, sygnatura dostępu współdzielonego ulegnie zmianie, ale wersja obrazu nie ulegnie zmianie. Po **zaktualizowaniu** programu postępuj zgodnie z przepływem pracy wdrożenia, a na urządzeniu zostanie zarejestrowane powiadomienie o aktualizacji.


Aby uzyskać więcej informacji na temat wdrożeń IoT Edge, zobacz na [tej stronie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurowanie tras
IoT Edge umożliwia deklaratywne kierowanie komunikatów między modułami oraz między modułami i IoT Hub. Pełna składnia została opisana [tutaj](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nazwy wejść i wyjść utworzonych w zadaniu ASA mogą służyć jako punkty końcowe dla routingu.  

###### <a name="example"></a>Przykład

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Ten przykład przedstawia trasy dla scenariusza opisanego na poniższej ilustracji. Zawiera ono zadanie brzegowe o nazwie " **ASA** " z danymi wejściowymi o nazwie " **temperatura** " i wyjście o nazwie " **alert** ".
![Przykładowy diagram routingu komunikatów](media/stream-analytics-edge/edge-message-routing-example.png)

Ten przykład definiuje następujące trasy:
- Każdy komunikat z **tempSensor** jest wysyłany do modułu o nazwie **ASA** do wejścia o nazwie **temperatura** ,
- Wszystkie dane wyjściowe modułu **ASA** są wysyłane do IoT Hub połączonej z tym urządzeniem ($upstream),
- Wszystkie dane wyjściowe modułu **ASA** są wysyłane do punktu końcowego **kontroli** **tempSensor** .


## <a name="technical-information"></a>Informacje techniczne
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Bieżące ograniczenia dotyczące IoT Edge zadań w porównaniu do zadań w chmurze
Celem jest zaplanowanie między zadaniami IoT Edge i zadaniami w chmurze. Obsługiwane są większość funkcji języka zapytań SQL, co umożliwia uruchamianie tej samej logiki w chmurze i IoT Edge.
Jednak następujące funkcje nie są jeszcze obsługiwane w przypadku zadań brzegowych:
* Funkcje zdefiniowane przez użytkownika (UDF) w języku JavaScript. System UDF jest dostępny w [języku C# dla zadań IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (wersja zapoznawcza).
* Agregacje zdefiniowane przez użytkownika (UDA).
* Funkcje usługi Azure ML.
* Użycie więcej niż 14 wartości zagregowanych w jednym kroku.
* Format AVRO dla operacji wejścia/wyjścia. W tej chwili obsługiwane są tylko woluminy CSV i JSON.
* Następujące operatory SQL:
    * PODZIEL NA PARTYCJE
    * GetMetadataPropertyValue
* Zasady późnego przybycia

### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące środowiska uruchomieniowego i sprzętu
Aby uruchamiać ASA na IoT Edge, potrzebne są urządzenia, które mogą uruchamiać [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA i Azure IoT Edge używają kontenerów **platformy Docker** , aby zapewnić przenośne rozwiązanie działające w wielu systemach operacyjnych hosta (Windows, Linux).

ASA na IoT Edge są udostępniane jako obrazy systemu Windows i Linux, uruchomione na architekturze x86-64 lub ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Dane wejściowe i wyjściowe
#### <a name="input-and-output-streams"></a>Strumienie wejściowe i wyjściowe
Zadania usługi ASA Edge mogą pobierać dane wejściowe i wyjściowe z innych modułów uruchomionych na IoT Edge urządzeniach. Aby nawiązać połączenie z i do określonych modułów, można ustawić konfigurację routingu w czasie wdrażania. Więcej informacji znajduje się w [dokumentacji dotyczącej kompozycji modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

W przypadku danych wejściowych i wyjściowych obsługiwane są formaty CSV i JSON.

Dla każdego strumienia danych wejściowych i wyjściowych tworzonego w ramach zadania ASA w wdrożonym module zostanie utworzony odpowiedni punkt końcowy. Te punkty końcowe mogą być używane w trasach wdrożenia.

W tej chwili jedynymi obsługiwanymi typami danych wejściowych strumienia i przesyłania strumieniowego są centra brzegowe. Wejście odwołania obsługuje typ pliku referencyjnego. Inne dane wyjściowe można osiągnąć przy użyciu zadania w chmurze podrzędnego. Na przykład zadanie Stream Analytics hostowane w programie Edge wysyła dane wyjściowe do centrum brzegowego, a następnie wysyła dane wyjściowe do IoT Hub. Możesz użyć drugiego zadania Azure Stream Analytics hostowanego w chmurze z danymi wejściowymi z IoT Hub i danych wyjściowych do Power BI lub innego typu wyjściowego.



##### <a name="reference-data"></a>Dane referencyjne
Dane referencyjne (nazywane również tabelami odnośników) to zbiór danych, który jest statyczny lub wolno zmieniany. Służy do przeszukiwania lub do skorelowania ze strumieniem danych. Aby korzystać z danych referencyjnych w zadaniu Azure Stream Analytics, zazwyczaj użyjesz [sprzężenia danych referencyjnych](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. Aby uzyskać więcej informacji, zobacz [Korzystanie z danych referencyjnych dla odnośników w Stream Analytics](stream-analytics-use-reference-data.md).

Obsługiwane są tylko lokalne dane referencyjne. Gdy zadanie zostanie wdrożone na urządzeniu IoT Edge, ładuje dane referencyjne ze ścieżki pliku zdefiniowanej przez użytkownika.

Aby utworzyć zadanie z danymi referencyjnymi na krawędzi:

1. Utwórz nowe dane wejściowe dla zadania.

2. Wybierz **dane referencyjne** jako **Typ źródła** .

3. Przygotuj plik danych referencyjnych na urządzeniu. W przypadku kontenera systemu Windows Umieść plik dane referencyjne na dysku lokalnym i Udostępnij dysk lokalny z kontenerem Docker. W przypadku kontenera systemu Linux Utwórz wolumin platformy Docker i uzupełnij plik danych do woluminu.

4. Ustaw ścieżkę pliku. W przypadku systemu operacyjnego hosta systemu Windows i kontenera systemu Windows użyj ścieżki bezwzględnej: `E:\<PathToFile>\v1.csv` . W przypadku systemu operacyjnego hosta Windows i systemu Linux oraz kontenera systemu operacyjnego Linux i Linux użyj ścieżki w woluminie: `<VolumeName>/file1.txt` .

![Nowe dane wejściowe danych referencyjnych dla zadania Azure Stream Analytics na IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Dane referencyjne w IoT Edge aktualizacji są wyzwalane przez wdrożenie. Po wyzwoleniu moduł ASA wybiera zaktualizowane dane bez zatrzymywania uruchomionego zadania.

Istnieją dwa sposoby aktualizowania danych referencyjnych:
* Zaktualizuj ścieżkę danych referencyjnych w ramach zadania ASA z Azure Portal.
* Zaktualizuj wdrożenie IoT Edge.

## <a name="license-and-third-party-notices"></a>Licencja i uwagi innych firm
* [Azure Stream Analytics na urządzeniach IoT Edge licencji](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Powiadomienie innych firm dotyczące Azure Stream Analytics na urządzeniach IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informacje o obrazie modułu Azure Stream Analytics 

Informacje o tej wersji zostały ostatnio zaktualizowane w dniu 2019-06-27:

- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platformach
      - Architektura: amd64
      - system operacyjny: Linux
 
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platformach
      - Architektura: ARM
      - system operacyjny: Linux
 
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platformach
      - Architektura: arm64
      - system operacyjny: Linux
      
      
## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dalszą pomoc, wypróbuj&stronie pytań i odpowiedzi [dla Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji na temat usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [IoT Edge — samouczek](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Opracowywanie zadań Stream Analytics Edge przy użyciu narzędzi Visual Studio Tools](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementowanie ciągłej integracji/ciągłego dostarczania Stream Analytics przy użyciu interfejsów API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
