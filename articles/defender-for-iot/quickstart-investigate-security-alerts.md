---
title: 'Szybki Start: badanie alertów zabezpieczeń'
description: Zrozumienie, przechodzenie do szczegółów i badanie alertów zabezpieczeń usługi Defender na urządzeniach IoT.
ms.topic: quickstart
ms.date: 07/30/2020
ms.openlocfilehash: 2eb4a10372680348536231aa0333c43199b8d883
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780994"
---
# <a name="quickstart-investigate-security-alerts"></a>Szybki Start: badanie alertów zabezpieczeń

Zaplanowane badanie i korygowanie alertów wystawionych przez usługę Defender for IoT to najlepszy sposób zapewnienia zgodności i ochrony w ramach rozwiązania IoT.

W tym przewodniku szybki start zapoznajemy informacje dostępne w każdym alercie zabezpieczeń IoT i wyjaśnisz, jak przeanalizować i użyć szczegółowych informacji o każdym alercie i powiązanym urządzeniu, aby odpowiedzieć i skorygować. 

Zaczynajmy. 


## <a name="investigate-new-security-alerts"></a>Zbadaj nowe alerty zabezpieczeń

Na liście alertów zabezpieczeń IoT Hub są wyświetlane wszystkie zagregowane alerty zabezpieczeń dla IoT Hub. 

1. W Azure Portal Otwórz **IoT Hub** , który chcesz zbadać pod kątem nowych alertów.
1. Z menu **zabezpieczenia** wybierz pozycję **alerty**. Wszystkie alerty zabezpieczeń dla IoT Hub będą wyświetlane, a alerty z **nową** flagą oznaczają alerty z ostatnich 24 godzin.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Zbadaj nowe alerty zabezpieczeń IoT przy użyciu nowej flagi alertu":::
1. Wybierz i Otwórz dowolny alert z listy, aby otworzyć Szczegóły alertu i przejść do szczegółów dotyczących alertów. 

## <a name="security-alert-details"></a>Szczegóły alertu zabezpieczeń

Otwarcie każdego zagregowanego alertu powoduje wyświetlenie szczegółowych opisów alertów, czynności zaradczych, identyfikatora urządzenia dla każdego urządzenia, które wyzwoliły alert, a także ważności alertu i bezpośredniego dostępu do badania przy użyciu Log Analytics. 

1. Wybierz i Otwórz dowolny alert zabezpieczeń z   >    >  listy **alerty** zabezpieczeń IoT Hub. 
1. Przejrzyj **Opis** alertu, **ważność**, **Źródło wykrywania**, **szczegóły urządzenia** wszystkich urządzeń, które wystawiły ten alert w okresie agregacji.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Przechodzenie do szczegółów i przeglądanie szczegółów poszczególnych urządzeń w zagregowanym alercie "::: 
1. Po przejrzeniu specyficznych alertów należy użyć instrukcji **ręcznego rozwiązywania problemów** , aby pomóc skorygować i/lub rozwiązać problem, który spowodował wygenerowanie alertu. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Postępuj zgodnie z ręcznymi krokami korygowania, aby pomóc w rozwiązaniu lub skorygowaniu alertów zabezpieczeń urządzenia":::

1. Jeśli wymagane jest dalsze badanie, **Zbadaj alerty w log Analytics** przy użyciu linku. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Aby kontynuować badanie alertu, Użyj linku badanie przy użyciu usługi log Analytics dostępnego na ekranie":::

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat typów alertów programu Defender i możliwych dostosowań...

> [!div class="nextstepaction"]
> [Informacje o alertach zabezpieczeń IoT](concept-security-alerts.md)
