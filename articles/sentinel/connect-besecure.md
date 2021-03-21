---
title: Nawiązywanie połączenia poza danymi beSECURE zabezpieczeń w usłudze Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych beSECURE z zabezpieczeniami do ściągania dzienników beSECURE do usługi Azure wskaźnikowej. Wyświetlaj dane beSECURE w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541170"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Łączenie poza beSECUREem zabezpieczeń z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik beSECURE Security jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik "beSECURE Security" umożliwia łatwe łączenie dzienników własnych rozwiązań zabezpieczeń beSECURE z badaniem wskaźnikowym platformy Azure w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między programem beSECURE i platformą Azure ze wskaźnikami korzysta z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-besecure"></a>Konfigurowanie i łączenie beSECURE

beSECURE można zintegrować z dziennikami i eksportować je bezpośrednio do platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **poza zabezpieczeniami beSECURE (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Wykonaj poniższe kroki, aby skonfigurować rozwiązanie beSECURE do wysyłania wyników skanowania, stanu skanowania i dzienników dziennika inspekcji do usługi Azure wskaźnikowej.

    **Dostęp do menu integracja:**
    1. Kliknij opcję menu "więcej"

    1. Wybierz serwer

    1. Wybierz integrację

    1. Włączanie usługi Azure Sentinel

    **Podaj beSECURE z ustawieniami wskaźnikiem na platformie Azure:**

      Skopiuj wartości *Identyfikator obszaru roboczego* i *klucz podstawowy* ze strony łącznika wskaźnikowego platformy Azure, wklej je w konfiguracji beSECURE, a następnie kliknij przycisk **Modyfikuj**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Identyfikator obszaru roboczego i klucz podstawowy}":::

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w co najmniej jednej z następujących tabel:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Aby wykonywać zapytania dotyczące dzienników beSECURE w regułach analizy, zapytań polowających, dochodzeń lub w dowolnym miejscu w punkcie kontrolnym platformy Azure, wprowadź jedną z powyższych nazw tabel w górnej części okna zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia beSECURE z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
