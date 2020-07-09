---
title: Łączenie dzienników pakietu Office 365 z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak korzystać z łącznika dziennika pakietu Office 365 w celu uzyskania informacji na temat bieżących działań użytkowników i administratorów w programach Exchange i SharePoint, w tym w usłudze OneDrive.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559339"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Łączenie dzienników pakietu Office 365 z usługą Azure wskaźnikowego

Łącznik dziennika [pakietu Office 365](https://docs.microsoft.com/office/) wprowadza do informacji o wskaźniku platformy Azure na bieżąco o działaniach związanych z użytkownikami i administratorami w **programach Exchange** i **SharePoint** (w tym **OneDrive**). Te informacje obejmują szczegółowe informacje o akcjach, takie jak pobieranie plików, wysłane żądania dostępu, zmiany w zdarzeniach grupy i operacje skrzynek pocztowych, a także szczegółowe informacje o użytkowniku, który wykonał akcje. Połączenie dzienników pakietu Office 365 z platformą Azure wskaźnikowego pozwala na wyświetlanie i analizowanie tych danych w skoroszytach, wykonywanie zapytań dotyczących tworzenia niestandardowych alertów i wprowadzanie ich w celu usprawnienia procesu badania, dzięki czemu możesz uzyskać dokładniejsze informacje o zabezpieczeniach pakietu Office 365.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w swojej dzierżawie.

- Wdrożenie pakietu Office 365 musi znajdować się w tej samej dzierżawie, w której znajduje się obszar roboczy wskaźnik platformy Azure.

> [!IMPORTANT]
> - Aby łącznik mógł uzyskać dostęp do danych za pomocą interfejsu API działania zarządzania pakietem Office 365, należy włączyć **ujednolicone rejestrowanie inspekcji** na potrzeby wdrożenia pakietu Office 365. W zależności od typu posiadanej licencji pakietu Office 365/Microsoft 365 może być ona domyślnie niewłączona. Zapoznaj się z [Centrum zabezpieczeń i zgodności pakietu Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) , aby sprawdzić stan ujednoliconego rejestrowania inspekcji zgodnie z typem licencji.
> - Można również ręcznie włączać, wyłączać i sprawdzać bieżący stan rejestrowania ujednoliconej inspekcji pakietu Office 365. Aby uzyskać instrukcje, zobacz [Włączanie lub wyłączanie wyszukiwania w dzienniku inspekcji pakietu Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Jak wspomniano powyżej, a jak widać na stronie łącznika w obszarze **typy danych**, łącznik pakietu 365 Office dla usługi Azure wskaźnikiem w ramach programu Microsoft Jednak istnieją pewne rozwiązania zewnętrzne, jeśli interesują Cię [dane z zespołów](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) lub [innych danych pakietu Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) do platformy Azure. 

## <a name="enable-the-office-365-log-connector"></a>Włącz łącznik dziennika pakietu Office 365

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Na liście **Łączniki danych** kliknij pozycję **Office 365**, a następnie przycisk **Otwórz stronę łącznika** w prawym dolnym rogu.

1. W sekcji **Konfiguracja**oznaczona etykietą zaznacz pola wyboru dzienników aktywności pakietu Office 365, które chcesz połączyć z platformą Azure, a następnie kliknij przycisk **Zastosuj zmiany**. 

   > [!NOTE]
   > Jeśli wcześniej połączono wiele dzierżawców z platformą Azure, przy użyciu starszej wersji łącznika pakietu Office 365, która obsługuje ten program, będziesz mieć możliwość wyświetlania i modyfikowania dzienników zbieranych od poszczególnych dzierżawców. Nie będzie można dodać kolejnych dzierżawców, ale można usunąć wcześniej dodanych dzierżawców.

1. Aby zbadać dane dziennika pakietu Office 365 w Log Analytics, wpisz `OfficeActivity` pierwszy wiersz okna zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia między pakietem Office 365 a wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.

