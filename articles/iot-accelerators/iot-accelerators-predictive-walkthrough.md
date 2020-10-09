---
title: Omówienie akceleratora rozwiązania do konserwacji predykcyjnej — Azure | Microsoft Docs
description: Omówienie akceleratora rozwiązania do konserwacji predykcyjnej usługi Azure IoT, który przewiduje punkt, w którym prawdopodobnie wystąpi awaria w scenariuszu biznesowym.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73827411"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do konserwacji predykcyjnej

Akcelerator rozwiązania do konserwacji predykcyjnej to kompleksowe rozwiązanie dotyczące scenariusza biznesowego, w którym przewidywany jest moment prawdopodobnego wystąpienia awarii. Ten akcelerator rozwiązania można aktywnie wykorzystać w celu zoptymalizowania konserwacji. Rozwiązanie łączy usługi Key Accelerator dla akceleratorów rozwiązań Azure IoT, takie jak IoT Hub i [Azure Machine Learning][lnk-machine-learning] obszaru roboczego. Ten obszar roboczy zawiera model oparty na publicznym zestawie przykładowych danych, które umożliwiają prognozowanie pozostałego czasu eksploatacji (RUL, Remaining Useful Life) silnika samolotu. Ponadto w rozwiązaniu w pełni zaimplementowano scenariusz biznesowy IoT jako punkt wyjściowy planowania i wdrażania rozwiązania zgodnego z potrzebami firmy.

Kod akceleratora rozwiązania do konserwacji predykcyjnej [jest dostępny w witrynie GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne tego akceleratora rozwiązania:

![Architektura logiczna][img-architecture]

Niebieskie elementy oznaczają usługi platformy Azure zaprowizowane w regionie, w którym wdrożono akcelerator rozwiązania. Lista regionów, w których można wdrożyć akcelerator rozwiązania, znajduje się na [stronie aprowizacji][lnk-azureiotsolutions].

Zielony element jest aparatem symulowanych samolotów. Więcej informacji na temat tych symulowanych urządzeń można znaleźć w sekcji [Symulowane urządzenia](#simulated-devices).

Szare elementy są składnikami, które implementują możliwości *zarządzania urządzeniami* . Bieżąca wersja akceleratora rozwiązania do konserwacji predykcyjnej nie umożliwia aprowizowania tych zasobów. Aby dowiedzieć się więcej o zarządzaniu urządzeniami, zapoznaj się z [akceleratorem rozwiązania do monitorowania zdalnego][lnk-remote-monitoring].

## <a name="azure-resources"></a>Zasoby platformy Azure

W portalu Azure przejdź do grupy zasobów z nazwą wybranego rozwiązania, aby wyświetlić aprowizowane zasoby.

![Zasoby akceleratora][img-resource-group]

Po przeprowadzeniu aprowizacji akceleratora rozwiązania otrzymasz wiadomość e-mail z linkiem do obszaru roboczego usługi Machine Learning. Możesz również przejść do obszaru roboczego Machine Learning ze strony [akceleratory rozwiązania IoT Microsoft Azure][lnk-azureiotsolutions] . Kafelek jest dostępny na tej stronie, gdy rozwiązanie jest w stanie **Gotowe**.

![Model uczenia maszynowego][img-machine-learning]

## <a name="simulated-devices"></a>Urządzenia symulowane

W akceleratorze rozwiązania symulowane urządzenie jest silnikiem samolotu. W ramach rozwiązania są aprowizowane dwa silniki (dla jednego samolotu). Każdy silnik emituje cztery rodzaje danych telemetrycznych: z czujnika 9, czujnika 11, czujnika 14 i czujnika 15. Czujniki dostarczają dane wymagane przez model usługi Machine Learning do obliczenia pozostałego czasu eksploatacji silnika. Poszczególne symulowane urządzenia wysyłają następujące komunikaty telemetryczne do usługi IoT Hub:

*Liczba cykli*. Cykl jest zakończonym działaniem z okresem od dwóch do dziesięciu godzin. Podczas lotu dane telemetryczne są przechwytywane co pół godziny.

Dane *telemetryczne*. Istnieją cztery czujniki, które rejestrują atrybuty silnika. Czujniki te mają ogólne etykiety: czujnik 9, czujnik 11, czujnik 14 i czujnik 15. Te cztery czujniki wysyłają dane telemetryczne wystarczające do uzyskania przydatnych wyników z modelu pozostałego czasu eksploatacji. Model wykorzystywany w tym akceleratorze rozwiązania został utworzony na podstawie publicznego zestawu danych zawierającego dane z czujników w rzeczywistych silnikach. Aby uzyskać więcej informacji dotyczących tworzenia modelu na podstawie oryginalnego zestawu danych, zobacz [szablon konserwacji predykcyjnej w witrynie Cortana Intelligence Gallery][lnk-cortana-analytics].

Symulowane urządzenia obsługują w rozwiązaniu następujące polecenia wysyłane z centrum IoT:

| Polecenie | Opis |
| --- | --- |
| StartTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Włącza przesyłanie danych telemetrycznych przez urządzenie. |
| StopTelemetry |Umożliwia sterowanie stanem symulacji.<br/>Umożliwia zatrzymanie przesyłania danych telemetrycznych przez urządzenie. |

Usługa IoT Hub udostępnia potwierdzenia poleceń wysyłanych do urządzeń.

## <a name="azure-stream-analytics-job"></a>Zadanie usługi Azure Stream Analytics

**Zadanie: Telemetria** operuje na strumieniu telemetrii urządzenia przychodzącego przy użyciu dwóch instrukcji:

* Pierwsza z nich pobiera wszystkie dane telemetryczne z urządzeń i wysyła je do magazynu obiektów blob. W tym miejscu jest to wizualizacja w aplikacji sieci Web.
* Druga instrukcja oblicza średnie wartości z czujników w ramach przesuwającego się okna czasowego trwającego dwie minuty i wysyła te wartości do **procesora zdarzeń** za pośrednictwem centrum zdarzeń.

## <a name="event-processor"></a>Procesor zdarzeń
**Host procesora zdarzeń** jest uruchamiany w zadaniu sieci Web Azure. **Procesor zdarzeń** przyjmuje średnie wartości z czujników dla ukończonego cyklu. Następnie przekazuje te wartości do nauczonego modelu, który oblicza pozostałego czasu eksploatacji dla aparatu. Interfejs API zapewnia dostęp do modelu w obszarze roboczym Machine Learning, który jest częścią rozwiązania.

## <a name="machine-learning"></a>Usługa Machine Learning
Składnik Machine Learning wykorzystuje model opracowany na podstawie danych zebranych z rzeczywistych silników samolotów. Możesz przejść do obszaru roboczego Machine Learning z kafelka rozwiązania na stronie [azureiotsolutions.com][lnk-azureiotsolutions] . Kafelek jest dostępny, gdy rozwiązanie jest w stanie **Gotowe**.

Model Machine Learning jest dostępny jako szablon, który pokazuje, jak korzystać z danych telemetrycznych zebranych za pomocą usługi IoT Solution Accelerator. Firma Microsoft utworzyła [model regresji][lnk_regression_model] silnika samolotu na podstawie publicznie dostępnych danych<sup> \[ 1 \] </sup>i wskazówki krok po kroku dotyczące korzystania z modelu.

Akcelerator rozwiązania Azure IoT do konserwacji predykcyjnej używa modelu regresji utworzonego na podstawie tego szablonu. Model jest wdrażany w ramach subskrypcji platformy Azure i udostępniany za pomocą automatycznie wygenerowanego interfejsu API. Rozwiązanie obejmuje podzestaw danych testowych dla silników 4 (z 100 łącznie) i 4 (z 21 całkowitych) strumieni danych czujnika. Te dane wystarczają do uzyskania dokładnego wyniku za pomocą nauczonego modelu.

*\[1 \] a. Saxena i K. Goebel (2008). "Zestaw danych symulacji obniżenia wydajności aparatu TurboFan", NASA Ames dotyczących dane repozytorium ( https://c3.nasa.gov/dashlink/resources/139/) , NASA Ames Research Center, Moffett Field pole, CA*

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z kluczowymi składnikami akceleratora rozwiązania do konserwacji predykcyjnej możesz je dostosować.

Możesz również zapoznać się z innymi funkcjami akceleratorów rozwiązań IoT:

* [Często zadawane pytania dotyczące akceleratorów rozwiązań IoT][lnk-faq]
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
