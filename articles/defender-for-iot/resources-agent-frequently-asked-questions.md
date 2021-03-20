---
title: Usługa Azure Defender dla agenta IoT — często zadawane pytania
description: Znajdź odpowiedzi na najczęściej zadawane pytania dotyczące usługi Azure Defender dla programu IoT Agent.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: f584f11603e41b63f3c96749ed6c720eb5ce07a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97835061"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Usługa Azure Defender dla agenta IoT — często zadawane pytania

Ten artykuł zawiera listę często zadawanych pytań i odpowiedzi dotyczących agenta usługi Defender for IoT.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Czy muszę zainstalować osadzony Agent zabezpieczeń?

Instalacja agenta na urządzeniach IoT nie jest obowiązkowa, aby można było włączyć usługę Defender for IoT. Można wybrać jedną z następujących trzech opcji, aby uzyskać różne poziomy monitorowania zabezpieczeń i zarządzania zgodnie z wyborem:

- Pasywne, nieinwazyjne (bez agentów) wdrażanie za pomocą czujników NTA (Analiza ruchu sieciowego) do monitorowania i zapewnienia głębokiego wglądu w ryzyko związane z wydajnością w sieci i na urządzeniach
- Zainstaluj agenta zabezpieczeń usługi Defender dla usługi IoT Embedded z modyfikacjami lub bez. Ta opcja zapewnia najwyższy poziom szczegółowych informacji o zabezpieczeniach w zakresie zachowania urządzeń i dostępu.

- Utwórz własnego agenta i zaimplementuj schemat komunikatów zabezpieczeń usługi Defender for IoT. Ta opcja umożliwia korzystanie z usługi Defender dla narzędzi analizy IoT na serwerze agenta zabezpieczeń urządzeń.

- Brak instalacji agenta zabezpieczeń na urządzeniach IoT. Ta opcja umożliwia IoT Hub monitorowanie komunikacji z mniejszymi możliwościami monitorowania i zarządzania zabezpieczeniami.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Co robi usługa Defender for IoT Agent?

Usługa Defender for IoT Agent zapewnia ochronę przed zagrożeniami na poziomie urządzenia w celu skonfigurowania, zachowania i dostępu (przez skanowanie konfiguracji), przetwarzania & łączności. Agent zabezpieczeń usługi Defender for IoT nie skanuje danych ani działań związanych z działalnością biznesową.

Agent zabezpieczeń usługi Defender for IoT to usługa Open Source dostępna w witrynie GitHub w wersji 32-i 64-bitowych systemu Windows i Linux: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jakie są zależności i wymagania wstępne agenta?

Usługa Defender for IoT obsługuje szeroką gamę platform. Zobacz [obsługiwane platformy urządzeń](how-to-deploy-agent.md) , aby sprawdzić pomoc techniczną dla konkretnych urządzeń.

## <a name="which-data-is-collected-by-the-agent"></a>Jakie dane są zbierane przez agenta?

Dane są zbierane przez agenta, dostęp, konfiguracja zapory, lista procesów & linia bazowa systemu operacyjnego.

## <a name="how-much-data-will-the-agent-generate"></a>Ile danych zostanie wygenerowany przez agenta?

Generowanie danych agenta jest sterowane przez urządzenia, aplikacje, typy połączeń i konfigurację agenta klienta. Ze względu na wysoką zmienność między urządzeniami i rozwiązaniami IoT zalecamy najpierw wdrożyć agenta w laboratorium lub w ustawieniach testowych, aby obserwować, poznać i ustawić konkretną konfigurację, która odpowiada Twoim potrzebom, przy jednoczesnym mierzeniu ilości wygenerowanych danych. Po uruchomieniu usługi Usługa Defender for IoT Agent udostępnia zalecenia operacyjne umożliwiające optymalizację przepływności agentów w celu ułatwienia konfiguracji i procesu dostosowywania.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Czy komunikaty agentów używają limitu przydziału z IoT Hub?

Tak. Dane przesyłane przez agenta są uwzględniane w limicie przydziału IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dalej? Zainstalowano agenta i nie są wyświetlane żadne działania ani dzienniki...

1. Sprawdź, czy [typ agenta pasuje do wyoznaczonej platformy systemu operacyjnego urządzenia](how-to-deploy-agent.md)

1. Potwierdź, że [Agent jest uruchomiony na urządzeniu](how-to-agent-configuration.md).

1. Sprawdź, czy [usługa została pomyślnie włączona](quickstart-onboard-iot-hub.md) **w IoT Hub** .

1. Sprawdź, czy urządzenie jest [skonfigurowane w IoT Hub za pomocą modułu Defender for IoT](quickstart-create-security-twin.md).

Jeśli działania lub dzienniki nadal są niedostępne, skontaktuj się z usługą Defender for IoT partner, aby uzyskać dodatkową pomoc.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co się stanie, gdy połączenie internetowe przestanie działać?

Czujniki i agenci kontynuują uruchamianie i przechowywanie danych, o ile urządzenie jest uruchomione. Dane są przechowywane w pamięci podręcznej komunikatów zabezpieczeń zgodnie z konfiguracją rozmiaru. Gdy urządzenie odzyska łączność, komunikaty zabezpieczeń są wznawiane.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Czy Agent może mieć wpływ na wydajność urządzenia lub innego zainstalowanego oprogramowania?

Agent zużywa zasoby maszynowe jako każdą inną aplikację/proces i nie powinien zakłócać normalnej aktywności urządzenia. Użycie zasobów na urządzeniu, na którym jest uruchomiony Agent programu, jest powiązane z konfiguracją i konfiguracjami. Przed podjęciem próby wdrożenia w środowisku produkcyjnym zalecamy przetestowanie konfiguracji agenta w zawartym środowisku oraz współdziałanie z innymi aplikacjami i funkcjami IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Wprowadzam pewne czynności konserwacyjne na urządzeniu. Czy mogę wyłączyć agenta?

Nie można wyłączyć agenta.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Czy istnieje sposób, aby sprawdzić, czy agent działa prawidłowo?

Jeśli Agent przestanie komunikować się lub nie można wysłać komunikatów zabezpieczeń, zostanie wygenerowany alert **dyskretny** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozpoczynania pracy z usługą Defender for IoT, zobacz następujące artykuły:

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Sprawdzanie [wymagań wstępnych systemu](quickstart-system-prerequisites.md)
- Dowiedz się więcej na temat [rozpoczynania pracy z usługą Defender for IoT](getting-started.md)
- Informacje [o alertach zabezpieczeń usługi Defender for IoT](concept-security-alerts.md)
