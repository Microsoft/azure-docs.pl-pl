---
title: Użyj Azure Security Center, aby zabezpieczyć hosty platformy Docker i chronić kontenery
description: Jak chronić hosty platformy Docker i sprawdzić, czy są one zgodne z testem porównawczym usługi CIS Docker
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916238"
---
# <a name="harden-your-docker-hosts"></a>Wzmacnianie zabezpieczeń hostów platformy Docker

Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub na innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze [wzorcem usługi Docker dla usługi Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. Po znalezieniu niepożądanych konfiguracji Security Center generuje zalecenia dotyczące zabezpieczeń. Użyj **strony zalecenia dotyczące** Security Center, aby wyświetlić zalecenia i rozwiązać problemy.

Po znalezieniu luk w zabezpieczeniach są one pogrupowane w ramach jednego zalecenia.

>[!NOTE]
> Te testy porównawcze usług CIS nie będą uruchamiane w wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez program.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Wymagane role i uprawnienia:|**Czytnik** w obszarze roboczym, z którym łączy się host|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identyfikowanie i korygowanie luk w zabezpieczeniach w konfiguracji platformy Docker

1. W menu Security Center Otwórz stronę **zalecenia** .

1. Odfiltruj do **luk w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować** i wybrać zalecenie.

    Na stronie rekomendacja są wyświetlane zasoby, których dotyczy usterka (hosty platformy Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Zalecenie dotyczące korygowania luk w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów ":::

1. Aby wyświetlić i skorygować kontrolę SIC, że określony host nie powiódł się, wybierz hosta, który chcesz zbadać. 

    > [!TIP]
    > Po rozpoczęciu pracy na stronie spisu zasobów i osiągnięciu tego zalecenia w tym miejscu ybierz przycisk **Przejmij akcję** na stronie rekomendacja.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Użyj przycisku akcji, aby uruchomić Log Analytics":::

    Log Analytics otwarty z niestandardową operacją gotową do uruchomienia. Domyślne zapytanie niestandardowe zawiera listę wszystkich ocenionych reguł zakończonych niepowodzeniem, a także wskazówki ułatwiające rozwiązanie problemów.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Strona Log Analyticsa z zapytaniem wyświetlającym wszystkie niepowodzenie kontrolek CIS":::

1. Dostosowywanie parametrów zapytania w razie potrzeby.

1. Jeśli masz pewność, że polecenie jest odpowiednie i gotowe dla hosta, wybierz pozycję **Uruchom**.


## <a name="next-steps"></a>Następne kroki

Ograniczanie funkcjonalności platformy Docker to tylko jeden aspekt funkcji zabezpieczeń kontenerów Security Center. 

Więcej informacji o [zabezpieczeniach kontenera znajduje się w Security Center](container-security.md).