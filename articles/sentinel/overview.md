---
title: Co to jest Azure Sentinel? | Microsoft Docs
description: Dowiedz się więcej na temat platformy Azure — informacje o skalowalnych i natywnych rozwiązaniach do zarządzania zdarzeniami z informacjami o zabezpieczeniach (SIEM) i o.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 773cf5cc6755a4f094eb676992364eea07a795ea
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654664"
---
# <a name="what-is-azure-sentinel"></a>Co to jest Azure Sentinel?

Microsoft Azure wskaźnikiem wydajności jest skalowalne i natywne rozwiązanie do **zarządzania zdarzeniami zabezpieczeń (Siem)** i **o (Security Orchestration)** . Platforma Azure — wskaźnik przedstawia inteligentne rozwiązania w zakresie zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia. 

Azure — wskaźnikowy to Twój Widok z oczami w całym przedsiębiorstwie, zmniejszający nacisk coraz bardziej zaawansowanych ataków, zwiększając liczbę alertów i długi czas rozwiązania.

- **Zbieraj dane w skali chmury** między wszystkimi użytkownikami, urządzeniami, aplikacjami i infrastrukturą, zarówno lokalnie, jak i w wielu chmurach. 

- **Wykrywaj wcześniej niewykryte zagrożenia** i Minimalizuj fałszywie dodatnie, korzystając z analizy i niezrównanej analizy zagrożeń firmy Microsoft. 

- **Zbadaj zagrożenia przy użyciu sztucznej analizy** i Wyszukaj podejrzane działania na dużą skalę, wybierając kolejno lata cybernetycznymi zabezpieczenia w firmie Microsoft. 

- **Szybko reaguj na zdarzenia** dzięki wbudowanej aranżacji i automatyzacji typowych zadań.

![Podstawowe możliwości platformy Azure wskaźnikowego](./media/overview/core-capabilities.png)

Kompilowanie w całym zakresie istniejących usług platformy Azure obejmuje natywne, udokumentowane fundacje, takie jak Log Analytics i Logic Apps. Platforma Azure wskaźnikowa wzbogaca badanie i wykrywanie przy użyciu AI oraz zapewnia strumień analizy zagrożeń firmy Microsoft i umożliwia korzystanie z własnej analizy zagrożeń. 

## <a name="connect-to-all-your-data"></a>Połącz ze wszystkimi danymi

Aby przejść do tablicy wskaźnikowej platformy Azure, musisz najpierw [nawiązać połączenie ze źródłami zabezpieczeń](connect-data-sources.md). Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym Microsoft 365 Defender (dawniej Microsoft Threat Protection), a także źródeł Microsoft 365, w tym pakietów Office 365, Azure AD, Microsoft Defender for Identity (dawniej Azure ATP) i Microsoft Cloud App Security i innych. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z systemem Azure. 

![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Skoroszyty

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) na platformie Azure — dane można monitorować, korzystając z integracji z Azure monitor skoroszytów platformy Azure, które zapewniają uniwersalność tworzenia niestandardowych skoroszytów. Gdy skoroszyty są wyświetlane inaczej na platformie Azure — wskaźnikiem, może być przydatne, aby zobaczyć, jak [tworzyć interaktywne raporty za pomocą skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md). System Azure — wskaźnik kontrolny umożliwia tworzenie niestandardowych skoroszytów w danych, a także udostępnia wbudowane szablony skoroszytów umożliwiające szybkie uzyskiwanie szczegółowych informacji na temat danych, gdy tylko nawiążesz połączenie ze źródłem danych.

![Pulpity nawigacyjne](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analiza

Aby ułatwić zredukowanie szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, wskaźnik platformy Azure używa [analizy do skorelowania alertów z zdarzeniami](tutorial-detect-threats-built-in.md). **Zdarzenia** to grupy powiązanych alertów, które wspólnie tworzą możliwe do podjęcia działania, które można zbadać i rozwiązać. Użyj wbudowanych reguł korelacji jako-lub użyj ich jako punktu wyjścia do tworzenia własnych. Usługa Azure Learning udostępnia także reguły uczenia maszynowego, które umożliwiają mapowanie zachowania sieci, a następnie poszukiwanie anomalii w zasobach. Te analizy łączą kropki, łącząc alerty o niskiej wierności dotyczące różnych jednostek w potencjalnych zdarzeniach związanych z zabezpieczeniami o wysokiej wierności.

![Zdarzenia](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>& aranżacji usługi Security Automation

Automatyzuj typowe zadania i [Uprość aranżację zabezpieczeń dzięki usłudze elementy PlayBook](tutorial-respond-threats-playbook.md) , która integruje się z usługami platformy Azure, a także istniejącymi narzędziami. W oparciu o podstawę Azure Logic Apps rozwiązanie do automatyzacji i organizowania wskaźnikowego platformy Azure zapewnia wysoce rozszerzalną architekturę, która umożliwia skalowalną automatyzację w miarę pojawiania się nowych technologii i zagrożeń. Aby kompilować elementy PlayBook z Azure Logic Apps, możesz wybrać pozycję z galerii wbudowanych elementy PlayBook. Obejmują one [200 łączników](../connectors/apis-list.md) dla usług takich jak Azure Functions. Łączniki umożliwiają zastosowanie dowolnej logiki niestandardowej w kodzie, usługi ServiceNow, JIRA, systemu Zendesk, żądaniach HTTP, Microsoft Teams, zapasowych, Windows Defender ATP i Cloud App Security.

Na przykład w przypadku korzystania z systemu biletów usługi ServiceNow można użyć narzędzi dostępnych Azure Logic Apps do automatyzacji przepływów pracy i otworzyć bilet w usługi ServiceNow za każdym razem, gdy zostanie wykryte określone zdarzenie.

![Podręczniki](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Badanie

Obecnie dostępne w wersji zapoznawczej narzędzia do badania w usłudze Azure — [szczegółowe](tutorial-investigate-cases.md) informacje ułatwiają zrozumienie zakresu i znalezienie głównej przyczyny potencjalnego zagrożenia bezpieczeństwa. Możesz wybrać jednostkę na interaktywnym wykresie, aby zadać interesujące pytania dotyczące określonej jednostki, i przejść do tej jednostki i jej połączeń, aby uzyskać dostęp do głównej przyczyny zagrożenia. 

![Badanie](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Wyszukiwanie zagrożeń

Korzystaj z [zaawansowanych narzędzi do wyszukiwania i wykonywania zapytań](hunting.md)na platformie Azure, opartych na strukturze Mitre, która umożliwia proaktywne wyszukiwanie zagrożeń bezpieczeństwa w źródłach danych organizacji przed wyzwoleniem alertu. Po ustaleniu, które zapytanie polowania zawiera szczegółowe informacje na temat możliwych ataków, można także utworzyć niestandardowe reguły wykrywania na podstawie zapytania, a następnie przedstawić te informacje jako alerty w odniesieniu do odpowiedzi na zdarzenia zabezpieczeń. Podczas polowania można tworzyć zakładki dla interesujących zdarzeń, co pozwala na powrót do nich później, udostępnianie ich innym osobom oraz grupowanie ich przy użyciu innych zdarzeń skorelowanych w celu utworzenia atrakcyjnych zdarzeń na potrzeby badania.

![Przegląd funkcji polowania](./media/overview/hunting.png)

## <a name="community"></a>Społeczność

Społeczność wskaźnikowa Azure jest zaawansowanym zasobem do wykrywania zagrożeń i automatyzacji. Nasi analitycy zabezpieczeń firmy Microsoft stale tworzą i dodają nowe skoroszyty, elementy PlayBook, zapytania polowające i nie tylko publikują je w społeczności, aby można było korzystać z tego środowiska. Możesz pobrać przykładową zawartość z [repozytorium](https://aka.ms/asicommunity) prywatnej społeczności GitHub, aby tworzyć niestandardowe skoroszyty, zapytania polowające, Notesy i elementy PlayBook na potrzeby platformy Azure. 

![Eksplorowanie społeczności użytkowników](./media/overview/community.png)

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).