---
title: Co to jest usługa Azure IoT Edge | Microsoft Docs
description: Omówienie usługi Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 1ba133acda414d9779e2fb10150bbdd57285e9a5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "76511216"
---
# <a name="what-is-azure-iot-edge"></a>Co to jest usługa Azure IoT Edge

Usługa IoT Edge przenosi analizę w chmurze i niestandardową logikę biznesową na urządzenia, aby Twoja organizacja mogła skoncentrować się na szczegółowych informacjach dotyczących działalności zamiast na zarządzaniu danymi. Skalowanie rozwiązania IoT przez pakowanie logiki biznesowej do standardowych kontenerów, następnie można wdrożyć te kontenery na dowolnym urządzeniu i monitorować je w chmurze.

Dzięki analizie wzrasta wartość biznesowa w rozwiązaniach IoT, ale nie wszystkie operacje analizy muszą być wykonywane w chmurze. Jeśli chcesz odpowiedzieć na sytuacje awaryjne tak szybko, jak to możliwe, możesz uruchamiać obciążenia wykrywania anomalii na krawędzi. Aby zmniejszyć koszty przepustowości i uniknąć przesyłania terabajtów danych pierwotnych, można wyczyścić i agregować dane lokalnie, a następnie wysłać wgląd do chmury w celu przeprowadzenia analizy.

Usługa Azure IoT Edge obejmuje trzy składniki:

* **Moduły IoT Edge** są kontenerami, w których działają usługi platformy Azure, usługi innych firm lub własny kod. Moduły są wdrażane na urządzeniach usługi IoT Edge i wykonywane lokalnie na tych urządzeniach.
* **Środowisko uruchomieniowe IoT Edge** działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na poszczególnych urządzeniach.
* **Interfejs oparty na chmurze** umożliwia zdalne monitorowanie IoT Edge urządzeń i zarządzanie nimi.

>[!NOTE]
>Usługa Azure IoT Edge jest dostępna w warstwie Bezpłatna oraz Standardowa usługi IoT Hub. Warstwa Bezpłatna służy wyłącznie do testowania i oceny. Aby uzyskać więcej informacji na temat warstw Podstawowa i Standardowa, zobacz [Jak wybrać właściwą warstwę usługi IoT Hub](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>Moduły usługi IoT Edge

Moduły usługi IoT Edge to jednostki wykonywania, zaimplementowane jako kontenery zgodne z platformą Docker, które uruchamiają logikę biznesową na urządzeniach brzegowych. Wiele modułów można skonfigurować do komunikowania się ze sobą i w ten sposób utworzyć potok przetwarzania danych. Aby zapewnić szczegółowe informacje w trybie offline i na urządzeniach brzegowych, możesz opracować moduły niestandardowe lub spakować niektóre usługi platformy Azure w moduły.

### <a name="artificial-intelligence-at-the-edge"></a>Sztuczna inteligencja na brzegu

Usługa Azure IoT Edge pozwala na wdrożenie kompleksowego przetwarzania zdarzeń, uczenia maszynowego, rozpoznawania obrazów i innych możliwości SI o wysokiej wartości bez samodzielnego ich tworzenia. Usługi platformy Azure, takie jak Azure Functions, Azure Stream Analytics i Azure Machine Learning, mogą być uruchamiane lokalnie za pośrednictwem Azure IoT Edge. Usługi platformy Azure nie są już ograniczone. Każdy może utworzyć moduły SI i udostępnić je społeczności do użycia za pośrednictwem witryny Azure Marketplace.

### <a name="bring-your-own-code"></a>Model dostarczania własnego kodu

Usługa Azure IoT Edge obsługuje również wdrażanie własnego kodu na urządzeniach. Usługa Azure IoT Edge korzysta z tego samego modelu programowania co inne usługi Azure IoT. Ten sam kod można uruchomić na urządzeniu lub w chmurze. Usługa Azure IoT Edge obsługuje zarówno system Linux, jak i Windows, więc możesz tworzyć kod na wybraną platformę. Obsługiwane języki to Java, .NET Core 2.0, Node.js, C i Python, dzięki czemu deweloperzy mogą tworzyć kod w znanym języku i używać istniejącej logiki biznesowej.

## <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe usługi Azure IoT Edge umożliwia stosowanie logiki niestandardowej i logiki w chmurze na urządzeniach usługi IoT Edge. Środowisko uruchomieniowe znajduje się na urządzeniu IoT Edge i wykonuje operacje zarządzania i komunikacji. Środowisko uruchomieniowe wykonuje kilka zadań:

* Instaluje i aktualizuje obciążenia na urządzeniu.
* Zapewnia zachowanie standardów zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Zarządza komunikacją między podrzędnymi urządzeniami liścia a urządzeniem IoT Edge, między modułami na urządzeniu IoT Edge i między urządzeniem IoT Edge i chmurą.

![Środowisko uruchomieniowe usługi IoT Edge wysyła szczegółowe informacje i raporty do usługi IoT Hub](./media/about-iot-edge/runtime.png)

Sposób używania urządzenia usługi Azure IoT Edge zależy od Ciebie. Środowisko uruchomieniowe jest często używane do wdrażania urządzeń AI na bramie, które agregują i przetwarzają dane z innych urządzeń lokalnych, ale ten model wdrażania jest tylko jedną opcją.

Środowisko uruchomieniowe Azure IoT Edge działa na dużym zestawie urządzeń IoT, które umożliwia korzystanie z niego na wiele sposobów. Obsługuje zarówno system operacyjny Linux, jak i Windows oraz tworzy abstrakcję szczegółów sprzętu. Użyj urządzenia mniejszego niż Raspberry Pi 3, jeśli nie przetwarzasz dużej ilości danych lub Użyj serwera przemysłowego do uruchamiania obciążeń intensywnie korzystających z zasobów.

## <a name="iot-edge-cloud-interface"></a>Interfejs chmurowy usługi IoT Edge

Trudno jest zarządzać cyklem życia oprogramowania dla milionów urządzeń IoT, które często różnią się od siebie i są modelami lub geograficznie rozproszonymi. Obciążenia są tworzone i konfigurowane dla konkretnego typu urządzenia, wdrażane na wszystkich urządzeniach i monitorowane, aby wyłapywać nieprawidłowo funkcjonujące urządzenia. Tych działań nie można wykonywać dla poszczególnych urządzeń. Muszą być wykonywane na dużą skalę.

Usługa Azure IoT Edge bezproblemowo integruje się z akceleratorami rozwiązań Azure IoT w celu zapewnienia jednej warstwy kontroli na potrzeby rozwiązania. Usługi w chmurze umożliwiają:

* Tworzenie i konfigurowanie obciążeń do uruchamiania na określonym typie urządzenia.
* Wysyłanie obciążeń do zestawu urządzeń.
* Monitorowanie obciążeń działających na urządzeniach w terenie.

![Telemetria i akcje urządzeń są koordynowane za pomocą chmury](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj te koncepcje w praktyce, [wdrażając usługę IoT Edge na symulowanym urządzeniu](quickstart.md).
