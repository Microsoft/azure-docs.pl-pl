---
title: 'Szybki Start: Tworzenie sznurka modułu zabezpieczeń'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć sznurek modułu Defender for IoT do użycia z usługą Azure Defender dla IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 3e48ed870bf405a82441678b5e8c01d199e7ebff
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835010"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Szybki Start: Tworzenie sznurka modułu azureiotsecurity

W tym przewodniku szybki start wyjaśniono, jak utworzyć pojedynczy moduł _azureiotsecurity_ bliźniaczych reprezentacji dla nowych urządzeń, lub Batch Create module bliźniaczych reprezentacji dla wszystkich urządzeń w IoT Hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Zrozumienie modułu azureiotsecurity bliźniaczych reprezentacji

W przypadku rozwiązań IoT utworzonych na platformie Azure bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zarządzaniu urządzeniami i automatyzacją procesów.

Usługa Defender for IoT oferuje pełną integrację z istniejącą platformą zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzeń oraz korzystanie z istniejących możliwości sterowania urządzeniami.
Integrację usługi Defender for IoT uzyskuje się, korzystając z mechanizmu IoT Hubej przędzy.

Aby dowiedzieć się więcej na temat ogólnej koncepcji modułu bliźniaczych reprezentacji w usłudze Azure IoT Hub, zobacz [IoT Hub module bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md) .

Usługa Defender for IoT korzysta z mechanizmu wieloosiowego modułu i utrzymuje dwuosiowy moduł zabezpieczeń o nazwie _azureiotsecurity_ dla każdego urządzenia.

Sznury modułowe zabezpieczeń zawiera wszystkie informacje dotyczące zabezpieczeń urządzeń dla każdego z urządzeń.

Aby w pełni korzystać z funkcji Defender for IoT, należy utworzyć, skonfigurować i używać tych bliźniaczych reprezentacji modułów zabezpieczeń dla każdego urządzenia w usłudze.

## <a name="create-azureiotsecurity-module-twin"></a>Tworzenie sznurka modułu azureiotsecurity

moduł _azureiotsecurity_ module bliźniaczych reprezentacji można utworzyć na dwa sposoby:

1. [Skrypt wsadowy modułu](https://aka.ms/iot-security-github-create-module) — automatycznie tworzy sznurki modułowe dla nowych urządzeń lub urządzeń bez użycia sznurka modułu przy użyciu konfiguracji domyślnej.
1. Ręcznie Edytuj poszczególne sznury modułów z określonymi konfiguracjami dla każdego urządzenia.

>[!NOTE]
> Użycie metody Batch nie spowoduje zastąpienia istniejącego modułu azureiotsecurity bliźniaczych reprezentacji. Za pomocą metody Batch tworzony jest tylko nowy moduł bliźniaczych reprezentacji dla urządzeń, które nie mają jeszcze sznurka modułu zabezpieczeń.

Zapoznaj się z tematem [Konfiguracja agenta](how-to-agent-configuration.md) , aby dowiedzieć się, jak zmodyfikować lub zmienić konfigurację wieloosiowego modułu.

Aby ręcznie utworzyć nową sznurek modułu _azureiotsecurity_ dla urządzenia, użyj następujących instrukcji:

1. W IoT Hub Znajdź i wybierz urządzenie, dla którego chcesz utworzyć dwuosiowy moduł zabezpieczeń.
1. Kliknij urządzenie, a następnie **Dodaj tożsamość modułu**.
1. W polu **Nazwa tożsamości modułu** wprowadź **azureiotsecurity**.

1. Kliknij pozycję **Zapisz**.

## <a name="verify-creation-of-a-module-twin"></a>Weryfikowanie tworzenia sznurka modułu

Aby sprawdzić, czy dla określonego urządzenia istnieje sznurek modułu zabezpieczeń:

1. W IoT Hub platformy Azure wybierz pozycję **urządzenia IoT** z menu **eksplorators** .
1. Wprowadź identyfikator urządzenia lub wybierz opcję w **polu zapytanie dotyczące urządzenia** , a następnie kliknij pozycję **zapytanie o urządzenia**.
    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Zapytanie dotyczące urządzeń":::
1. Wybierz urządzenie lub kliknij je dwukrotnie, aby otworzyć stronę szczegóły urządzenia.
1. Wybierz menu **tożsamości modułów** i potwierdzenie istnienia modułu **azureiotsecurity** na liście tożsamości modułów skojarzonych z urządzeniem.
    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Moduły skojarzone z urządzeniem":::

Aby dowiedzieć się więcej o dostosowywaniu właściwości usługi Defender for IoT bliźniaczych reprezentacji, zobacz [Konfiguracja agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zbadać zalecenia dotyczące zabezpieczeń...

> [!div class="nextstepaction"]
> [Badanie rekomendacji dotyczących zabezpieczeń](quickstart-investigate-security-recommendations.md)