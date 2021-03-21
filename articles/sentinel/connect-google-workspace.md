---
title: Łączenie danych usługi Google Workspace (G Suite) ze wskaźnikiem na platformie Azure | Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych usługi Google Workspace (G Suite) do pozyskiwania zdarzeń aktywności obszaru roboczego Google na platformie Azure. Wyświetlaj dane obszaru roboczego Google w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745261"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Połącz obszar roboczy Google z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Google Workspace jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik danych usługi [Google Workspace (dawniej G Suite)](https://workspace.google.com/) zapewnia możliwość pozyskiwania zdarzeń aktywności obszaru roboczego Google za pomocą interfejsu API REST. Łącznik zapewnia widoczność tych [zdarzeń](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) w SOC, który ułatwia badanie potencjalnych zagrożeń bezpieczeństwa, analizowanie użycia współpracy przez zespół, diagnozowanie problemów z konfiguracją, śledzenie osób, które logują się i kiedy, analizowanie aktywności administratorów, zrozumienie sposobu tworzenia i udostępniania zawartości oraz przeglądanie większej liczby zdarzeń w organizacji.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zbierać dane z usługi Google Workspace, musisz mieć następujące uprawnienia i poświadczenia:

- Uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Uprawnienia do odczytu kluczy współużytkowanych w obszarze roboczym. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Uprawnienia do odczytu i zapisu w celu Azure Functions tworzenia aplikacja funkcji. [Dowiedz się więcej o Azure Functions](../azure-functions/index.yml).

- Poświadczenie **GooglePickleString** jest wymagane dla interfejsu API REST. [Dowiedz się więcej o interfejsie API usługi Google REST](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Dowiedz się, jak uzyskać poświadczenia](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Konfigurowanie i łączenie obszaru roboczego Google

Usługa Google Workspace umożliwia integrację i eksportowanie dzienników bezpośrednio do funkcji wskaźnikowej platformy Azure przy użyciu aplikacja funkcji platformy Azure.

> [!NOTE]
> Ten łącznik używa Azure Functions do nawiązywania połączenia z interfejsem API usługi Google Reports w celu ściągnięcia zdarzeń aktywności w usłudze Azure wskaźnikowej. Może to skutkować dodatkowymi kosztami pozyskiwania danych. Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/) .

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych**. 

1. Wybierz opcję **Google Workspace (G Suite) (wersja zapoznawcza)** z galerii łączników, a następnie wybierz pozycję **Otwórz stronę łącznika**.

1. Wykonaj kroki opisane w sekcji **Konfiguracja** na stronie łącznik.

## <a name="validate-connectivity-and-find-your-data"></a>Weryfikowanie łączności i znajdowanie danych

Może upłynąć do 20 minut, dopóki nie będzie można zobaczyć zebranych danych na platformie Azure.

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **dzienniki niestandardowe** w następujących tabelach:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. [Wykonaj następujące kroki](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) , aby utworzyć alias funkcji **GWorkspaceActivityReports** Kusto.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z obszarem roboczym Google z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
