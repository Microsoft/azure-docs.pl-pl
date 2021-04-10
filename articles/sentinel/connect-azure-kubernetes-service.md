---
title: Łączenie dzienników diagnostycznych usługi Azure Kubernetes Service (AKS) z platformą Azure — wskaźnikiem
description: Dowiedz się, jak używać Azure Policy do łączenia dzienników diagnostyki usługi Azure Kubernetes z platformą Azure.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507181"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Łączenie dzienników diagnostyki usługi Azure Kubernetes

Azure Kubernetes Service (AKS) to usługa w pełni zarządzanej aranżacji kontenerów, która umożliwia wdrażanie, skalowanie i Zarządzanie kontenerami platformy Docker oraz aplikacjami opartymi na kontenerach w środowisku klastrowym.

Ten łącznik umożliwia strumieniowe przesyłanie dzienników diagnostycznych usługi Azure Kubernetes Service (AKS) do punktów kontrolnych platformy Azure, dzięki czemu można stale monitorować aktywność we wszystkich wystąpieniach. 

Dowiedz się więcej o [monitorowaniu usługi Azure Kubernetes](../azure-monitor/containers/container-insights-overview.md) oraz o [telemetrii diagnostyki AKS](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby pozyskiwanie dzienników AKS na platformie Azure — wskaźnik:

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Aby użyć Azure Policy do zastosowania zasad przesyłania strumieniowego dziennika do zasobów AKS, musisz mieć przypisaną rolę właściciela dla zakresu przypisania zasad.

## <a name="connect-to-azure-kubernetes-service"></a>Nawiązywanie połączenia z usługą Azure Kubernetes

Ten łącznik używa Azure Policy do zastosowania pojedynczej konfiguracji przesyłania strumieniowego dzienników usługi Azure Kubernetes do kolekcji wystąpień zdefiniowanej jako zakres. Typy dzienników pozyskane z usługi Azure Kubernetes Service można zobaczyć po lewej stronie łącznika w obszarze **typy danych**.

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz pozycję **Azure Kubernetes Service (AKS)** z galerii łączników danych, a następnie wybierz pozycję **Otwórz stronę łącznika** w okienku podglądu.

1. W sekcji **Konfiguracja** na stronie łącznik rozwiń węzeł **Włączanie dzienników diagnostycznych w usłudze Azure KUBERNETES Service (AKS)**.

1. Wybierz przycisk **Kreator uruchamiania Przypisywania Azure Policy** .

    Zostanie otwarty Kreator przypisania zasad, gotowy do utworzenia nowych zasad o nazwie **Deploy-Configure Diagnostic Settings for Azure Kubernetes Service to log Analytics Workspace**.

    1. Na karcie **podstawy** kliknij przycisk z trzema kropkami w obszarze **zakres** , aby wybrać subskrypcję (i opcjonalnie grupę zasobów). Możesz również dodać opis.

    1. Na karcie **Parametry** wybierz obszar roboczy Azure wskaźnikowy z listy rozwijanej **obszar roboczy log Analytics** . Pozostałe pola rozwijane reprezentują dostępne typy dzienników diagnostycznych. Pozostaw oznaczone jako "true" wszystkie typy dzienników, które chcesz pozyskać.

    1. Aby zastosować zasady do istniejących zasobów, wybierz kartę **korygowanie** i zaznacz pole wyboru **Utwórz zadanie korygujące** .

    1. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**. Twoje zasady są teraz przypisane do wybranego zakresu.

> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Azure Kubernetes Service z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
