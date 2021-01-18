---
title: Połącz lepsze zabezpieczenia mobilnej ochrony przed zagrożeniami (MTD) z platformą Azure Microsoft Docs
description: Dowiedz się, jak korzystać z lepszego łącznika danych obrony przed zagrożeniami (MTD) w celu ściągania dzienników MTD do usługi Azure wskaźnikowej. Wyświetlaj dane MTD w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541562"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Połącz lepsze zabezpieczenia mobilnej ochrony przed zagrożeniami (MTD) z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> LEPSZY łącznik programu Mobile Threat obron (MTD) jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

ULEPSZONy łącznik usługi Mobile Threat obron (MTD) umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń MTD z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Integracja między lepszą obroną programu Mobile Threat i platformą Azure wskaźnikiem umożliwia korzystanie z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Konfigurowanie i łączenie lepszej ochrony przed zagrożeniami mobilnymi

LEPSZA MTD można zintegrować i wyeksportować dzienniki bezpośrednio do usługi Azure wskaźnikowej.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **lepsza ochrona przed zagrożeniami (mtd) (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika i na [tej stronie z lepszej MTD dokumentacji](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) , aby zakończyć integrację z lepszą konsolą MTD.

    Po zażądaniu wprowadzenia wartości **identyfikatora obszaru roboczego** i **klucza podstawowego** skopiuj je ze strony łącznika Azure wskaźnikowego i wklej je do lepszej konfiguracji MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Identyfikator obszaru roboczego i klucz podstawowy}":::

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w co najmniej jednej z następujących tabel:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Aby wykonać zapytanie dotyczące LEPSZYch dzienników MTD w regułach analizy, zapytaniach o łowiectwe lub w dowolnym miejscu w punkcie kontrolnym platformy Azure, wprowadź jedną z powyższych nazw tabel w górnej części okna zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia lepszej ochrony przed zagrożeniami mobilnymi (MTD) z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
