---
title: 'Szybki Start: Konfigurowanie rozwiązania'
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować kompleksowe rozwiązanie IoT przy użyciu Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 04505527428a6bb312c6fb7a12c116947fbd7cf6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310856"
---
# <a name="quickstart-configure-your-iot-solution"></a>Szybki Start: Konfigurowanie rozwiązania IoT

Ten artykuł zawiera informacje na temat przeprowadzania początkowej konfiguracji rozwiązania do ochrony IoT przy użyciu Azure Security Center dla IoT.

## <a name="azure-security-center-for-iot"></a>Azure Security Center dla IoT

Azure Security Center dla IoT oferuje kompleksowe zabezpieczenia kompleksowych rozwiązań IoT opartych na platformie Azure.

Dzięki Azure Security Center dla IoT możesz monitorować całe rozwiązanie IoT na jednym pulpicie nawigacyjnym, obsłużyć wszystkie urządzenia IoT, platformy IoT i zasoby zaplecza na platformie Azure.

Po włączeniu usługi IoT w IoT Hub Azure Security Center w celu automatycznego zidentyfikowania innych usług platformy Azure, również połączona z IoT Hub i związanych z Twoim rozwiązaniem IoT.

Oprócz automatycznego wykrywania relacji można także wybierać i wybierać inne grupy zasobów platformy Azure, które mają być używane w ramach rozwiązania IoT.

Wybrane opcje umożliwiają dodawanie całych subskrypcji, grup zasobów lub pojedynczych zasobów.

Po zdefiniowaniu wszystkich relacji zasobów Azure Security Center dla usługi IoT wykorzystuje Azure Security Center, aby udostępnić zalecenia dotyczące zabezpieczeń i alerty dotyczące tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT, wykonaj następujące czynności:

1. Otwórz **IoT Hub** w Azure Portal.
1. Wybierz i Otwórz **zasoby** z opcji **zabezpieczenia** w menu po lewej stronie.
1. Wybierz pozycję **Edytuj** i wybierz grupy zasobów należące do rozwiązania IoT.
1. Kliknij pozycję **Dodaj**.

Gratulacje! Dodano nową grupę zasobów do rozwiązania IoT.

Azure Security Center usługi IoT teraz monitoruje nowo dodane grupy zasobów i prezentuje odpowiednie zalecenia dotyczące zabezpieczeń i alerty w ramach rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć moduły zabezpieczeń...

> [!div class="nextstepaction"]
> [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
