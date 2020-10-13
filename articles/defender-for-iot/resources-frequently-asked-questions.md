---
title: Usługa Defender for IoT — często zadawane pytania
description: Znajdź odpowiedzi na najczęściej zadawane pytania dotyczące funkcji i usługi Azure Defender dla IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939758"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Usługa Azure Defender dla IoT — często zadawane pytania

Ten artykuł zawiera listę często zadawanych pytań i odpowiedzi dotyczących usługi Defender for IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>Czy platforma Azure zapewnia pomoc techniczną dla zabezpieczeń IoT?

Platforma Azure oferuje zintegrowany widok służący do monitorowania zabezpieczeń IoT i zarządzania nimi w ramach ogólnego rozwiązania zabezpieczającego za pomocą Azure Security Center. Jeśli jesteś deweloperem aplikacji, możesz użyć widoku IoT Hub, aby zarządzać zabezpieczeniami aplikacji IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co to jest wyjątkowa wartość na platformie Azure dla zabezpieczeń IoT?

Usługa Defender for IoT umożliwia przedsiębiorstwom rozszeranie istniejącego widoku zabezpieczeń cybernetycznymi na całe rozwiązanie IoT. Platforma Azure zapewnia kompleksowy wgląd w Twoje rozwiązanie biznesowe, umożliwiając podejmowanie działań i decyzji związanych z prowadzoną działalnością w oparciu o stan zabezpieczeń przedsiębiorstwa i zebrane dane. Połączone zabezpieczenia za pomocą usługi Azure IoT, Azure IoT Edge i Azure Security Center umożliwiają tworzenie potrzebnych rozwiązań przy użyciu żądanych zabezpieczeń.

## <a name="who-is-defender-for-iot-made-for"></a>Dla kogo został wykonany usługa Defender for IoT?

Usługa Defender for IoT jest zintegrowana z usługą Azure IoT Hub Security i umożliwia zarządzanie codziennymi operacjami bezpieczeństwa rozwiązań biznesowej. Usługa Defender for IoT jest również zintegrowana z funkcjami Azure Security Center i zapewnia zintegrowany widok do monitorowania zabezpieczeń IoT i zarządzania nim w ramach ogólnego rozwiązania zabezpieczeń.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Jak usługa Defender for IoT jest porównywana z konkursem?

Inne rozwiązania zapewniają zestaw funkcji, które umożliwiają klientom tworzenie własnych rozwiązań, usługa Defender for IoT zapewnia unikatowe kompleksowe rozwiązanie do obsługi zabezpieczeń IoT, które zapewnia szeroki wgląd w zabezpieczenia wszystkich powiązanych zasobów platformy Azure. System Azure umożliwia szybkie wdrażanie i pełną integrację z usługą IoT Hub module bliźniaczych reprezentacji w celu zapewnienia łatwej integracji z istniejącymi narzędziami do zarządzania urządzeniami.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Czy muszę mieć Azure Security Center klienta, aby korzystać z tej usługi?

Nie, ale jest to zalecane. Bez Azure Security Center usługa Defender for IoT otrzymuje ograniczone dane połączonego zasobu i oferuje ograniczoną analizę potencjalnych zagrożeń i potencjalnych ataków.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Czy muszę być klientem usługi Azure IoT?

Tak. Usługa Defender for IoT opiera się na łączności i infrastrukturze Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Czy muszę zainstalować agenta?

Instalacja agenta na urządzeniach IoT nie jest obowiązkowa, aby można było włączyć usługę Defender for IoT. Można wybrać jedną z następujących trzech opcji, aby uzyskać różne poziomy monitorowania zabezpieczeń i zarządzania zgodnie z wyborem:

1. Zainstaluj usługę Defender for IoT Security Agent z modyfikacjami lub bez nich. Ta opcja zapewnia najwyższy poziom szczegółowych informacji o zabezpieczeniach w zakresie zachowania urządzeń i dostępu.

1. Utwórz własnego agenta i zaimplementuj schemat komunikatów zabezpieczeń usługi Defender for IoT. Ta opcja umożliwia korzystanie z usługi Defender dla narzędzi analizy IoT na serwerze agenta zabezpieczeń urządzeń.

1. Brak instalacji agenta zabezpieczeń na urządzeniach IoT. Ta opcja umożliwia IoT Hub monitorowanie komunikacji z mniejszymi możliwościami monitorowania i zarządzania zabezpieczeniami.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Co robi usługa Defender for IoT Agent?

Usługa Defender for IoT Agent zapewnia ochronę przed zagrożeniami na poziomie urządzenia w celu skonfigurowania, zachowania i dostępu (przez skanowanie konfiguracji), przetwarzania & łączności. Agent zabezpieczeń usługi Defender for IoT nie skanuje danych ani działań związanych z działalnością biznesową.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Gdzie mogę uzyskać usługę Defender dla usługi IoT Security Agent?

Agent zabezpieczeń usługi Defender for IoT to usługa Open Source dostępna w witrynie GitHub w wersji 32-i 64-bitowych systemu Windows i Linux: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Gdzie jest zainstalowany agent usługi Defender for IoT?

Szczegółowe informacje o instalacji i wdrożeniu agentów można znaleźć w witrynie GitHub: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jakie są zależności i wymagania wstępne agenta?

Usługa Defender for IoT obsługuje szeroką gamę platform. Zobacz [obsługiwane platformy urządzeń](how-to-deploy-agent.md) , aby sprawdzić pomoc techniczną dla konkretnych urządzeń.

## <a name="which-data-is-collected-by-the-agent"></a>Jakie dane są zbierane przez agenta?

Dane są zbierane przez agenta, dostęp, konfiguracja zapory, lista procesów & linia bazowa systemu operacyjnego.

## <a name="how-much-data-will-the-agent-generate"></a>Ile danych zostanie wygenerowany przez agenta?

Generowanie danych agenta jest sterowane przez urządzenia, aplikacje, typy połączeń i konfigurację agenta klienta. Ze względu na wysoką zmienność między urządzeniami i rozwiązaniami IoT zalecamy najpierw wdrożyć agenta w laboratorium lub w ustawieniach testowych, aby obserwować, poznać i ustawić konkretną konfigurację, która odpowiada Twoim potrzebom, przy jednoczesnym mierzeniu ilości wygenerowanych danych. Po uruchomieniu usługi Usługa Defender for IoT Agent udostępnia zalecenia operacyjne umożliwiające optymalizację przepływności agentów w celu ułatwienia konfiguracji i procesu dostosowywania.

## <a name="how-can-i-control-my-billing"></a>Jak mogę kontrolować Moje rozliczenia?

Usługa Defender for IoT udostępnia konfigurowalne skanowania agentów, bufory danych oraz możliwość tworzenia alertów niestandardowych, które zwiększają lub zmniejszają ilość danych wygenerowanych przez agenta.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Czy komunikaty agentów używają limitu przydziału z IoT Hub?

Tak. Dane przesyłane przez agenta są uwzględniane w limicie przydziału IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dalej? Zainstalowano agenta i nie są wyświetlane żadne działania ani dzienniki...

1. Sprawdź, czy [typ agenta pasuje do wyoznaczonej platformy systemu operacyjnego urządzenia](how-to-deploy-agent.md)

1. Potwierdź, że [Agent jest uruchomiony na urządzeniu](how-to-agent-configuration.md).

1. Sprawdź, czy [usługa została pomyślnie włączona](quickstart-onboard-iot-hub.md) **w IoT Hub** .

1. Sprawdź, czy urządzenie jest [skonfigurowane w IoT Hub za pomocą modułu Defender for IoT](quickstart-create-security-twin.md).

Jeśli działania lub dzienniki nadal są niedostępne, skontaktuj się z usługą Defender for IoT partner, aby uzyskać dodatkową pomoc.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co się stanie, gdy połączenie internetowe przestanie działać?

Agent nadal uruchamia i przechowuje dane tak długo, jak urządzenie jest uruchomione. Dane są przechowywane w pamięci podręcznej komunikatów zabezpieczeń zgodnie z konfiguracją rozmiaru. Gdy urządzenie odzyska łączność, komunikaty zabezpieczeń są wznawiane.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Jeśli urządzenie zostanie uruchomione ponownie, czy Agent zabezpieczeń odzyska samoodzyskiwanie?

Agent zabezpieczeń został zaprojektowany w celu automatycznego ponownego uruchomienia po każdym ponownym uruchomieniu urządzenia.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Czy Agent może mieć wpływ na wydajność urządzenia lub innego zainstalowanego oprogramowania?

Agent zużywa zasoby maszynowe jako każdą inną aplikację/proces i nie powinien zakłócać normalnej aktywności urządzenia. Użycie zasobów na urządzeniu, na którym jest uruchomiony Agent programu, jest powiązane z konfiguracją i konfiguracjami. Przed podjęciem próby wdrożenia w środowisku produkcyjnym zalecamy przetestowanie konfiguracji agenta w zawartym środowisku oraz współdziałanie z innymi aplikacjami i funkcjami IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Wprowadzam pewne czynności konserwacyjne na urządzeniu. Czy mogę wyłączyć agenta?

Nie można wyłączyć agenta.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Czy istnieje sposób, aby sprawdzić, czy agent działa prawidłowo?

Jeśli Agent przestanie komunikować się lub nie można wysłać komunikatów zabezpieczeń, zostanie wygenerowany alert **dyskretny** .

## <a name="can-i-create-my-own-alerts"></a>Czy mogę tworzyć własne alerty?

Tak. Możesz ustawić dostosowany alert dla wstępnie ustalonego zestawu zachowań, takich jak adres IP i otwarte porty. Zobacz [Tworzenie niestandardowych alertów](quickstart-create-custom-alerts.md) , aby dowiedzieć się więcej o alertach niestandardowych i sposobach ich tworzenia.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Gdzie mogę zobaczyć dzienniki? Czy mogę dostosować dzienniki?

- Wyświetlanie alertów i zaleceń przy użyciu połączonego obszaru roboczego Log Analytics. Skonfiguruj rozmiar i czas trwania magazynu w obszarze roboczym.

- Dane pierwotne z agenta zabezpieczeń mogą być również przechowywane na koncie Log Analytics. Przed zmianą konfiguracji tej opcji należy wziąć pod uwagę rozmiar, czas trwania, wymagania dotyczące magazynu i powiązane koszty.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Dlaczego należy dodać usługę Defender for IoT do tożsamości modułu? Do czego służy?

Moduł Defender for IoT służy do konfigurowania agentów i zarządzania nimi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozpoczynania pracy z usługą Defender for IoT, zobacz następujące artykuły:

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Weryfikowanie [wymagań wstępnych usługi](service-prerequisites.md)
- Dowiedz się więcej na temat [rozpoczynania pracy](getting-started.md)
- Informacje [o alertach zabezpieczeń usługi Defender for IoT](concept-security-alerts.md)
