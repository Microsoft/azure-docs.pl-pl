---
title: Połącz dane z zaatakowaną ochroną ataków Proofpoint (TAP) do usługi Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z Proofpointą dostosowanej ochrony przed atakami (TAP) na platformę Azure.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531466"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Połącz swoje Proofpoint z platformą Azure, korzystając z funkcji platformy Azure

Łącznik Proofpointa ochrona przed atakami (TAP) umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń [PROOFPOINT TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) z platformą Azure — do wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między programem Proofpoint TAP a platformą Azure — umożliwia korzystanie z Azure Functions ściągania danych dzienników przy użyciu interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-proofpoint-tap"></a>Konfigurowanie i łączenie Proofpoint TAP

Azure Functions umożliwia integrację i ściąganie zdarzeń i dzienników bezpośrednio z Proofpoint TAP i przekazywanie ich do usługi Azure wskaźnikowej.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję Łącznik **Proofpoint TAP** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **TAP Proofpoint** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w tabelach **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** i **ProofpointTAPClicksBlocked_CL** .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z programem Proofpoint TAP z usługą Azure wskaźnikiem przy użyciu aplikacji funkcji platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
