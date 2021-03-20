---
title: Łączenie Zimperium z usługą Azure — wskaźnik ochrony przed zagrożeniami Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z usługą Azure wskaźnikowej ochrony przed zagrożeniami Zimperium.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097453"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Łączenie ochrony przed zagrożeniami programu Zimperium Mobile z platformą Azure — wskaźnikiem


> [!IMPORTANT]
> Łącznik danych Zimperium Mobile Threat obrony w usłudze Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Łącznik Zimperium Mobile Threat obrony umożliwia łączenie dziennika zagrożeń Zimperium z platformą Azure wskaźnikowego, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i ulepszać badanie. Dzięki temu możesz uzyskać więcej informacji na temat środowiska mobilnego zagrożenia w organizacji i zwiększyć możliwości operacji zabezpieczeń.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurowanie i łączenie Zimperium ochrony przed zagrożeniami mobilnymi

Ochrona przed zagrożeniami w usłudze Zimperium Mobile umożliwia integrację i eksportowanie dzienników bezpośrednio do **usługi Azure wskaźnikowej**.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję łączniki danych i wybierz pozycję **Zimperium Mobile Threat obrony**.
2. Wybierz pozycję **Otwórz stronę łącznika**.
3. Postępuj zgodnie z instrukcjami na stronie łącznika **Zimperium Mobile Threat obrony** , podsumowanie w następujący sposób.
 1. W zConsole kliknij pozycję **Zarządzaj** na pasku nawigacyjnym.
 2. Kliknij kartę **Integrations** (Integracje).
 3. Kliknij przycisk **raportowanie zagrożeń** , a następnie przycisk **Dodaj integracje** .
 4. Utwórz integrację, wybierając pozycję **Microsoft Azure wskaźnikiem** z dostępnych integracji i wprowadź identyfikator obszaru roboczego i klucz podstawowy, aby nawiązać połączenie z platformą Azure.
 5. Dostępna jest również opcja wyboru poziomu filtru dla danych zagrożeń do wypchnięcia do usługi Azure wskaźnikowej. 

4. Aby uzyskać dodatkowe informacje, zapoznaj się z [portalem obsługi klienta Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze CustomLogs ZimperiumThreatLog_CL i ZimperiumMitigationLog_CL.

Aby użyć odpowiedniego schematu w Log Analytics na potrzeby obrony Zimperium Mobile Threat, Wyszukaj ZimperiumThreatLog_CL i ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z witryną Zimperium Mobile Threat obrony z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).

- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

Aby dowiedzieć się więcej na temat Zimperium, zobacz następujące tematy:

- [Zimperium](https://zimperium.com)

- [Blog dotyczący zabezpieczeń mobilnych Zimperium](https://blog.zimperium.com)

- [Portal obsługi klienta Zimperium](https://support.zimperium.com)

