---
title: Łączenie dzienników usługi Dynamics 365 z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak korzystać z łącznika działania Common Data Service Dynamics 365 (CD) w celu uzyskania informacji o bieżących działaniach administratora, użytkownika i pomocy technicznej.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98104196"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Połącz dzienniki aktywności Dynamics 365 z platformą Azure — wskaźnikiem

Łącznik działania Common Data Service [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) (CD) zapewnia wgląd w działania administratora, użytkownika i pomocy technicznej, a także zdarzenia rejestrowania usługi Microsoft Social Engagement. Łącząc dzienniki programu Dynamics 365 CRM z maszyną badawczą platformy Azure, można wyświetlać te dane w skoroszytach, używać ich do tworzenia niestandardowych alertów i korzystać z nich w celu ulepszania procesu badania. Ten nowy łącznik usługi Azure wskaźnikowego zbiera dane z dysków CD z pakietem Dynamics Management API. Aby dowiedzieć się więcej o działaniach dotyczących systemu Dynamics CD poddawanych inspekcji w usłudze elektrowni, zapoznaj się z tematem [Włączanie i używanie rejestrowania aktywności](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> Łącznik działania Common Data Service Dynamics 365 (CD) jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Wymagana jest [licencja produkcyjna systemu Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Ten łącznik nie jest dostępny dla środowisk piaskownicy.
    - Do rejestrowania aktywności wymagana jest subskrypcja Microsoft 365 Enterprise [E3 lub E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) .

- Aby pobrać dane z interfejsu API zarządzania pakietu Office:
    - Musisz być administratorem globalnym w dzierżawie.

    - [Rejestrowanie inspekcji pakietu Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) musi być włączone w [Centrum zabezpieczeń i zgodności z pakietem Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Włączanie łącznika danych działań Dynamics 365

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz pozycję **Dynamics 365 (wersja zapoznawcza)**, a następnie wybierz pozycję **Otwórz stronę łącznika** w okienku podglądu.

1. Na karcie **instrukcje** w obszarze **Konfiguracja** kliknij pozycję **Połącz**. 

    Po aktywowaniu łącznika potrwa około 30 minut, aż będzie można zobaczyć dane przychodzące do grafu. 

    Zgodnie z [dziennikiem inspekcji pakietu Office w centrum zgodności](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)może upłynąć do 30 minut lub nawet 24 godziny po wystąpieniu zdarzenia w celu zwrócenia odpowiedniego rekordu dziennika inspekcji w wynikach.

1. Dzienniki inspekcji systemu Microsoft Dynamics można znaleźć w `Dynamics365Activity` tabeli. Zobacz [odwołanie do schematu](/azure/azure-monitor/reference/tables/dynamics365activity)tabeli.

## <a name="querying-the-data"></a>Wykonywanie zapytań dotyczących danych

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **dzienniki**.

1. Uruchom następujące zapytanie, aby sprawdzić, czy dzienniki dotarły do:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Następne kroki
W tym dokumencie pokazano, jak połączyć dane działań Dynamics 365 z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.
