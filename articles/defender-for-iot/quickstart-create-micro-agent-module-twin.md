---
title: Tworzenie sznurka modułu programu Defender IoT Micro Agent (wersja zapoznawcza)
titleSuffix: Azure Defender for IoT
description: Dowiedz się, jak utworzyć poszczególne DefenderIotMicroAgent modułów bliźniaczych reprezentacji dla nowych urządzeń.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7935db0a78db59abaf2e9983c5ebb56bb4365a7e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810184"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Tworzenie sznurka modułu programu Defender IoT Micro Agent (wersja zapoznawcza)

Można utworzyć pojedynczy moduł **DefenderIotMicroAgent** bliźniaczych reprezentacji dla nowych urządzeń. Można również tworzyć wsadowe bliźniaczych reprezentacji modułów dla wszystkich urządzeń w IoT Hub. 

## <a name="device-twins"></a>Bliźniaczych reprezentacji urządzenia 

W przypadku rozwiązań IoT utworzonych na platformie Azure bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zarządzaniu urządzeniami i automatyzacją procesów. 

Usługa Defender for IoT umożliwia w pełni integrację z istniejącą platformą zarządzania urządzeniami IoT. Pełna integracja, umożliwia zarządzanie stanem zabezpieczeń urządzenia i umożliwia korzystanie z wszystkich istniejących możliwości sterowania urządzeniami. Integrację uzyskuje się, korzystając z mechanizmu IoT Hubej przędzy. 

Dowiedz się więcej o koncepcji [bliźniaczych reprezentacji urządzeń](../iot-hub/iot-hub-devguide-device-twins.md)   na platformie Azure IoT Hub. 

## <a name="security-module-twins"></a>Bliźniaczych reprezentacji modułu zabezpieczeń 

Usługa Defender for IoT używa sznurka modułu zabezpieczeń dla każdego urządzenia. Sznury modułu zabezpieczeń zawiera wszystkie informacje istotne dla zabezpieczeń urządzeń dla każdego konkretnego urządzenia w rozwiązaniu. Właściwości zabezpieczeń urządzeń są konfigurowane za pomocą dedykowanych sznurów modułu zabezpieczeń w celu zapewnienia bezpieczniejszej komunikacji, do włączania aktualizacji i konserwacji, które wymagają mniejszej ilości zasobów. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Zrozumienie modułu DefenderIotMicroAgent bliźniaczych reprezentacji 

Bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zakresie zarządzania urządzeniami i automatyzacji procesów dla rozwiązań IoT wbudowanych w platformę Azure.

Usługa Defender for IoT oferuje pełną integrację istniejącej platformy zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzeń i korzystanie z istniejących możliwości sterowania urządzeniami. Możesz zintegrować usługę Defender for IoT, korzystając z mechanizmu IoT Hubej sieci dwuosiowej.  

Aby dowiedzieć się więcej na temat ogólnej koncepcji modułu bliźniaczych reprezentacji w usłudze Azure IoT Hub, zobacz [IoT Hub module bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md).

Usługa Defender for IoT używa mechanizmu sieci dwuosiowej i utrzymuje jednoosiowy moduł zabezpieczeń o nazwie `DefenderIotMicroAgent` dla każdego z urządzeń. 

Aby w pełni korzystać ze wszystkich funkcji usługi Defender for IoT, należy utworzyć, skonfigurować i używać modułu zabezpieczeń bliźniaczych reprezentacji dla każdego urządzenia w usłudze. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Tworzenie sznurka modułu DefenderIotMicroAgent 

**DefenderIotMicroAgent** module bliźniaczych reprezentacji można utworzyć, ręcznie edytując każdy sznur modułu w celu uwzględnienia określonych konfiguracji dla każdego urządzenia. 

Aby ręcznie utworzyć nową sznurek modułu **DefenderIotMicroAgent** dla urządzenia: 

1. W IoT Hub Znajdź i wybierz urządzenie, na którym chcesz utworzyć dwuosiowy moduł zabezpieczeń. 

1. Wybierz pozycję **Dodaj tożsamość modułu**. 

1. W polu **Nazwa tożsamości modułu**   wprowadź wartość  `DefenderIotMicroAgent` . 

1. Wybierz pozycję **Zapisz**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Weryfikowanie tworzenia sznurka modułu 

Aby sprawdzić, czy dla określonego urządzenia istnieje sznurek modułu zabezpieczeń: 

1. W IoT Hub platformy Azure wybierz pozycję **urządzenia IoT**   z menu **eksplorators**   . 

1. Wprowadź identyfikator urządzenia lub wybierz opcję w polu **zapytanie dotyczące urządzenia** i wybierz pozycję zapytanie o **urządzenia**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Wybierz pozycję zapytanie dotyczące urządzeń, aby wyświetlić listę urządzeń.":::

1. Wybierz urządzenie i Otwórz stronę **szczegóły urządzenia** . 

1. Wybierz menu **tożsamości modułów**   i sprawdź obecność modułu **DefenderIotMicroAgent** na liście tożsamości modułów skojarzonych z urządzeniem.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Wybierz pozycję tożsamości modułów z karty.":::

## <a name="next-steps"></a>Następne kroki 

Przejdź do następnego artykułu, aby dowiedzieć się, jak [zbadać zalecenia dotyczące zabezpieczeń](quickstart-investigate-security-recommendations.md).
