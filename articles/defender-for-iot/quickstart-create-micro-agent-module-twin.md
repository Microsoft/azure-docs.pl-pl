---
title: 'Szybki Start: Tworzenie sznurka modułu Defender IoT Micro Agent (wersja zapoznawcza)'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć poszczególne DefenderIotMicroAgent modułów bliźniaczych reprezentacji dla nowych urządzeń.
ms.date: 1/20/2021
ms.topic: quickstart
ms.openlocfilehash: e1bcaa76e9bfbe417bfd0d4b539b8e0c7ff95c7b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384515"
---
# <a name="quickstart-create-a-defender-iot-micro-agent-module-twin-preview"></a>Szybki Start: Tworzenie sznurka modułu Defender IoT Micro Agent (wersja zapoznawcza)

Można utworzyć pojedynczy moduł **DefenderIotMicroAgent** bliźniaczych reprezentacji dla nowych urządzeń. Można również tworzyć wsadowe bliźniaczych reprezentacji modułów dla wszystkich urządzeń w IoT Hub. 

## <a name="prerequisites"></a>Wymagania wstępne

- Brak

## <a name="device-twins"></a>Bliźniaczych reprezentacji urządzenia 

W przypadku rozwiązań IoT utworzonych na platformie Azure bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zarządzaniu urządzeniami i automatyzacją procesów. 

Usługa Defender for IoT umożliwia w pełni integrację z istniejącą platformą zarządzania urządzeniami IoT. Pełna integracja, umożliwia zarządzanie stanem zabezpieczeń urządzenia i umożliwia korzystanie z wszystkich istniejących możliwości sterowania urządzeniami. Integrację uzyskuje się, korzystając z mechanizmu IoT Hubej przędzy. 

Dowiedz się więcej o koncepcji [bliźniaczych reprezentacji urządzeń](../iot-hub/iot-hub-devguide-device-twins.md)   na platformie Azure IoT Hub. 

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent bliźniaczych reprezentacji 

Usługa Defender for IoT używa sznurów Defender-IoT-Micro-Agent dla każdego urządzenia. Sznurek usługi Defender-IoT-Micro-Agent zawiera wszystkie informacje istotne dla zabezpieczeń urządzeń dla każdego konkretnego urządzenia w rozwiązaniu. Właściwości zabezpieczeń urządzeń są konfigurowane za pomocą dedykowanych sznurów Defender-IoT-Micro-Agent do bezpieczniejszej komunikacji, do włączania aktualizacji i konserwacji, które wymagają mniejszej ilości zasobów. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Zrozumienie modułu DefenderIotMicroAgent bliźniaczych reprezentacji 

Bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zakresie zarządzania urządzeniami i automatyzacji procesów dla rozwiązań IoT wbudowanych w platformę Azure.

Usługa Defender for IoT oferuje pełną integrację istniejącej platformy zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzeń i korzystanie z istniejących możliwości sterowania urządzeniami. Możesz zintegrować usługę Defender for IoT, korzystając z mechanizmu IoT Hubej sieci dwuosiowej.  

Aby dowiedzieć się więcej na temat ogólnej koncepcji modułu bliźniaczych reprezentacji w usłudze Azure IoT Hub, zobacz [IoT Hub module bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md).

Usługa Defender for IoT używa mechanizmu sieci dwuosiowej i utrzymuje sznurek usługi Defender-IoT-Micro-Agent o nazwie `DefenderIotMicroAgent` dla każdego urządzenia. 

Aby w pełni korzystać ze wszystkich funkcji usługi Defender for IoT, należy utworzyć, skonfigurować i użyć usługi Defender-IoT-Micro-Agent bliźniaczych reprezentacji dla każdego urządzenia w usłudze. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Tworzenie sznurka modułu DefenderIotMicroAgent 

**DefenderIotMicroAgent** module bliźniaczych reprezentacji można utworzyć, ręcznie edytując każdy sznur modułu w celu uwzględnienia określonych konfiguracji dla każdego urządzenia. 

Aby ręcznie utworzyć nową sznurek modułu **DefenderIotMicroAgent** dla urządzenia: 

1. W IoT Hub Znajdź i wybierz urządzenie, na którym chcesz utworzyć sznurek Defender-IoT-Micro-Agent. 

1. Wybierz pozycję **Dodaj tożsamość modułu**. 

1. W polu **Nazwa tożsamości modułu**   wprowadź wartość  `DefenderIotMicroAgent` . 

1. Wybierz pozycję **Zapisz**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Weryfikowanie tworzenia sznurka modułu 

Aby sprawdzić, czy dla określonego urządzenia istnieje niezależna usługa Defender-IoT-Micro-Agent: 

1. W IoT Hub platformy Azure wybierz pozycję **urządzenia IoT**   z menu **eksplorators**   . 

1. Wprowadź identyfikator urządzenia lub wybierz opcję w polu **zapytanie dotyczące urządzenia** i wybierz pozycję zapytanie o **urządzenia**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Wybierz pozycję zapytanie dotyczące urządzeń, aby wyświetlić listę urządzeń.":::

1. Wybierz urządzenie i Otwórz stronę **szczegóły urządzenia** . 

1. Wybierz menu **tożsamości modułów**   i sprawdź obecność modułu **DefenderIotMicroAgent** na liście tożsamości modułów skojarzonych z urządzeniem.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Wybierz pozycję tożsamości modułów z karty.":::

## <a name="next-steps"></a>Następne kroki 

> [!div class="nextstepaction"]
> [Zbadaj zalecenia dotyczące zabezpieczeń](quickstart-investigate-security-recommendations.md)
