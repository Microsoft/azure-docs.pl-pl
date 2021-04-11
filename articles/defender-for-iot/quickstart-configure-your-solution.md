---
title: 'Szybki Start: Dodawanie zasobów platformy Azure do rozwiązania IoT'
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować kompleksowe rozwiązanie IoT przy użyciu usługi Azure Defender for IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 6a90e8c3007f7b3448fd3f1b6e4fa46ba861081b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384584"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Szybki Start: Konfigurowanie rozwiązania Azure Defender for IoT

Ten artykuł zawiera wyjaśnienie, jak przeprowadzić początkową konfigurację rozwiązania do zabezpieczeń IoT przy użyciu usługi Defender dla IoT.

## <a name="prerequisites"></a>Wymagania wstępne

- Brak

## <a name="what-is-defender-for-iot"></a>Co to jest usługa Defender for IoT?

Usługa Defender for IoT oferuje kompleksowe zabezpieczenia dla rozwiązań IoT opartych na platformie Azure.

Usługa Defender for IoT umożliwia monitorowanie całego rozwiązania IoT na jednym pulpicie nawigacyjnym, a także obsługę wszystkich urządzeń IoT, platform IoT i zasobów zaplecza na platformie Azure.

Po włączeniu IoT Hub usługa Defender for IoT automatycznie identyfikuje inne usługi platformy Azure, połączyło się również z IoT Hub i związane z rozwiązaniem IoT.

Oprócz automatycznego wykrywania relacji można także wybierać i wybierać inne grupy zasobów platformy Azure, które mają być używane w ramach rozwiązania IoT.

Wybrane opcje umożliwiają dodawanie całych subskrypcji, grup zasobów lub pojedynczych zasobów.

Po zdefiniowaniu wszystkich relacji zasobów usługa Defender for IoT używa usługi Defender do udostępniania zaleceń i alertów dotyczących zabezpieczeń dla tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT:

1. Otwórz **IoT Hub** w Azure Portal.

1. W obszarze **zabezpieczenia** wybierz pozycję **Przegląd** , a następnie pozycję **Ustawienia**, a następnie wybierz opcję **monitorowane zasoby**.

1. Wybierz pozycję **Edytuj** i wybierz monitorowane zasoby należące do rozwiązania IoT.

1. Wybierz pozycję **Dodaj**.

Gratulacje! Dodano nową grupę zasobów do rozwiązania IoT.

Usługa Defender for IoT monitoruje teraz nowo dodane grupy zasobów i prezentuje odpowiednie zalecenia dotyczące zabezpieczeń i alerty w ramach rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć usługę Defender-IoT-Micro-Agents...

> [!div class="nextstepaction"]
> [Tworzenie Defender-IoT-Micro-Agents](quickstart-create-security-twin.md)
