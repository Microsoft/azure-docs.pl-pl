---
title: Raport analizy zagrożeń usługi Azure Security Center | Microsoft Docs
description: Ta strona ułatwia korzystanie z raportów analizy zagrożeń Azure Security Center podczas badania, aby znaleźć więcej informacji na temat alertów zabezpieczeń
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: ec6d227059c3f4fd1285f224e13169a2479bc65f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438237"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Raport analizy zagrożeń Azure Security Center

Na tej stronie wyjaśniono, jak raporty analizy zagrożeń Azure Security Center mogą pomóc dowiedzieć się więcej na temat zagrożenia, które spowodowało wyzwolenie alertu zabezpieczeń.


## <a name="what-is-a-threat-intelligence-report"></a>Czym jest raport analizy zagrożeń?

Security Center Ochrona przed zagrożeniami polega na monitorowaniu informacji o zabezpieczeniach z zasobów platformy Azure, sieci i połączonych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Aby uzyskać więcej informacji, zobacz [jak Azure Security Center wykrywa zagrożenia i reaguje na](security-center-alerts-overview.md#detect-threats)nie.

Gdy Security Center identyfikuje zagrożenie, wyzwala [alert zabezpieczeń](security-center-managing-and-responding-alerts.md), który zawiera szczegółowe informacje dotyczące zdarzenia, w tym sugestie dotyczące korygowania. Aby pomóc zespołom reagowania na incydenty badają i korygują zagrożenia, Security Center zawiera raporty analizy zagrożeń zawierające informacje o wykrytych zagrożeniach. Raport zawiera informacje, takie jak:

* tożsamość lub powiązania osoby atakującej (jeśli takie informacje są dostępne);
* cele osoby atakującej;
* bieżące i wcześniejsze kampanie ataków (jeśli takie informacje są dostępne);
* Osoby atakujące taktykę, narzędzia i procedury
* skojarzone wskaźniki naruszenia (IoC), np. adresy URL i skróty plików;
* wiktymologia, czyli informacje dotyczące branży i obszaru geograficznego, które mogą być pomocne w ustaleniu, czy zasoby na platformie Azure są zagrożone;
* informacje dotyczące ograniczania i usuwania zagrożeń.

> [!NOTE]
> Ilość informacji w poszczególnych raportach będzie różna. Poziom szczegółowości zależy od aktywności i powszechności złośliwego oprogramowania.

Usługa Security Center obejmuje trzy typy raportów zagrożeń, które mogą różnić się w zależności od ataku. Dostępne raporty:

* **Raport grupy działań**: zawiera głębokie omówieniachy osoby atakujące, ich cele i taktykę.
* **Raport kampanii**: koncentruje się na szczegółach określonych kampanii ataków.
* **Raport z podsumowaniem zagrożenia**: obejmuje wszystkie elementy z poprzednich dwóch raportów.

Informacje tego typu są przydatne podczas procesu reagowania na zdarzenia, w przypadku którego istnieje ciągłe badanie dotyczące źródła ataku, motywacji ataku oraz czynności, które należy wykonać w celu ograniczenia tego problemu w przyszłości.



## <a name="how-to-access-the-threat-intelligence-report"></a>Jak uzyskać dostęp do raportu analizy zagrożeń?

1. Na pasku bocznym Security Center Otwórz stronę **alerty zabezpieczeń** .
1. Wybierz Alert. 
    Zostanie otwarta strona szczegóły alertów z dodatkowymi informacjami na temat alertu. Poniżej znajduje się Strona szczegółów alertu **wykrytych wskaźników oprogramowania wymuszającego okup** .

    [![Strona szczegółów alertu wykrytych wskaźników oprogramowania wymuszającego okup](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Wybierz łącze do raportu, a plik PDF zostanie otwarty w domyślnej przeglądarce.

    [![Strona szczegółów alertów o potencjalnie niebezpiecznej akcji](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Opcjonalnie można pobrać raport PDF. 

    >[!TIP]
    > Ilość informacji dostępnych dla każdego alertu zabezpieczeń zależy od typu alertu.



## <a name="next-steps"></a>Następne kroki

Na tej stronie wyjaśniono, jak otwierać Raporty analizy zagrożeń podczas badania alertów zabezpieczeń. Aby uzyskać powiązane informacje, zobacz następujące strony:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md). Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Obsługa zdarzeń związanych z zabezpieczeniami w Azure Security Center](security-center-incident.md)