---
title: Łączenie dzienników pakietu Office 365 z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak korzystać z łącznika dziennika pakietu Office 365 w celu uzyskania informacji na temat bieżących działań użytkowników i administratorów w programach Exchange, Teams i SharePoint, w tym w usłudze OneDrive.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: d6b59de048cdf00d352c4f488ecb51bfdf83640f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89178930"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Łączenie dzienników pakietu Office 365 z usługą Azure wskaźnikowego

Łącznik dziennika [pakietu Office 365](https://docs.microsoft.com/office/) zawiera informacje na temat działań związanych z trwającym użytkownikiem i administratorami w **programach Exchange** i **SharePoint** (w tym **OneDrive**), a teraz także w **zespołach** . Te informacje obejmują szczegółowe informacje o akcjach, takie jak pobieranie plików, wysłane żądania dostępu, zmiany w grupach zdarzeń, operacje skrzynek pocztowych, zdarzenia zespołów (takie jak rozmowy, zespoły, elementy członkowskie i kanały), a także szczegółowe informacje o użytkowniku, który wykonał akcje. Połączenie dzienników pakietu Office 365 z platformą Azure wskaźnikowego pozwala na wyświetlanie i analizowanie tych danych w skoroszytach, wykonywanie zapytań dotyczących tworzenia niestandardowych alertów i wprowadzanie ich w celu usprawnienia procesu badania, dzięki czemu możesz uzyskać dokładniejsze informacje o zabezpieczeniach pakietu Office 365.

> [!IMPORTANT]
> **Rozszerzenie** łącznika dziennika pakietu Office 365 jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w swojej dzierżawie.

- Wdrożenie pakietu Office 365 musi znajdować się w tej samej dzierżawie, w której znajduje się obszar roboczy wskaźnik platformy Azure.

> [!IMPORTANT]
> - Aby łącznik mógł uzyskać dostęp do danych za pomocą interfejsu API działania zarządzania pakietem Office 365, należy włączyć **ujednolicone rejestrowanie inspekcji** na potrzeby wdrożenia pakietu Office 365. W zależności od typu posiadanej licencji pakietu Office 365/Microsoft 365 może być ona domyślnie niewłączona. Zapoznaj się z [Centrum zabezpieczeń i zgodności pakietu Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) , aby sprawdzić stan ujednoliconego rejestrowania inspekcji zgodnie z typem licencji.
> - Można również ręcznie włączać, wyłączać i sprawdzać bieżący stan rejestrowania ujednoliconej inspekcji pakietu Office 365. Aby uzyskać instrukcje, zobacz [Włączanie lub wyłączanie wyszukiwania w dzienniku inspekcji pakietu Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Jak wspomniano powyżej, a jak widać na stronie łącznika w obszarze **typy danych**, łącznik pakietu 365 Office do badania platformy Azure (w tym OneDrive) obecnie obsługuje pozyskiwanie dzienników inspekcji tylko z programów Microsoft Exchange i SharePoint ( **w**tym z usługi Azure). Jednak istnieją pewne rozwiązania zewnętrzne, jeśli interesują Cię [inne dane pakietu Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) w wskaźniku platformy Azure. 

## <a name="enable-the-office-365-log-connector"></a>Włącz łącznik dziennika pakietu Office 365

### <a name="instructions-tab"></a>Karta instrukcje

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz pozycję **Office 365**, a następnie wybierz pozycję **Otwórz stronę łącznika** w okienku podglądu.

1. W sekcji **Konfiguracja**oznaczona etykietą zaznacz pola wyboru dzienników aktywności pakietu Office 365, które chcesz połączyć z platformą Azure, a następnie kliknij przycisk **Zastosuj zmiany**. 

   > [!NOTE]
   > Jeśli wcześniej połączono wiele dzierżawców z platformą Azure, przy użyciu starszej wersji łącznika pakietu Office 365, która obsługuje ten program, będziesz mieć możliwość wyświetlania i modyfikowania dzienników zbieranych od poszczególnych dzierżawców. Nie będzie można dodać kolejnych dzierżawców, ale można usunąć wcześniej dodanych dzierżawców.

### <a name="next-steps-tab"></a>Karta następne kroki

- Zapoznaj się z zalecanymi skoroszytami, przykładami zapytań i szablonami reguł analizy, które są powiązane z łącznikiem dziennika **pakietu Office 365** , aby uzyskać wgląd w dane dziennika programu SharePoint, usługi OneDrive, programu Exchange i zespołów.

- Aby ręcznie zbadać dane dziennika pakietu Office 365 w **dziennikach**, wprowadź `OfficeActivity` w pierwszym wierszu okna zapytania.
   - Aby odfiltrować zapytanie dla określonego typu dziennika, wprowadź `| where OfficeWorkload == "<logtype>"` w drugim wierszu zapytania, gdzie *\<logtype\>* ma wartość `SharePoint` , `OneDrive` , `Exchange` lub `MicrosoftTeams` .

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia między pakietem Office 365 a wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.

