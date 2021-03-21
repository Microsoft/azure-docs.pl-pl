---
title: Łączenie Cloud App Security danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika Microsoft Cloud App Security (MCAS) do przesyłania strumieniowego alertów i Cloud Discovery dzienników z MCAS do platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97835112"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z Microsoft Cloud App Security 

Łącznik [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) umożliwia strumieniowe przesyłanie alertów i [dzienników Cloud Discovery](/cloud-app-security/tutorial-shadow-it) z MCAS do platformy Azure. Dzięki temu możesz uzyskać wgląd w aplikacje w chmurze, uzyskać zaawansowaną analizę, aby identyfikować i zwalczać dotyczące środowiskach oraz kontrolować sposób podróży danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w obszarze roboczym.
- Użytkownik musi mieć uprawnienia administratora globalnego lub administratora zabezpieczeń w dzierżawie obszaru roboczego.
- Aby przesłać strumieniowo Cloud Discovery dzienniki do usługi Azure wskaźnikowej, [Włącz platformę Azure jako Siem w Microsoft Cloud App Security](/cloud-app-security/siem-sentinel).

> [!IMPORTANT]
> Pozyskiwanie dzienników Cloud Discovery jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Połącz z Cloud App Security

Jeśli masz już Cloud App Security, upewnij się, że jest [włączona w sieci](/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli Cloud App Security zostanie wdrożona i pozyskuje dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**. Z listy łączników kliknij kafelek **Microsoft Cloud App Security** , a następnie przycisk **Otwórz stronę łącznika** w prawym dolnym rogu.

1. Wybierz dzienniki, które chcesz przesłać do usługi Azure wskaźnikowej; Możesz wybrać **alerty** i **dzienniki Cloud Discovery** (wersja zapoznawcza). 

1. Kliknij przycisk **Zastosuj zmiany**.

1. Możesz wybrać, czy alerty od Cloud App Security mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie incydentów — zalecane!** wybierz opcję **włączone** , aby włączyć domyślną regułę analizy, która automatycznie tworzy zdarzenia z alertów. Następnie można edytować tę regułę w obszarze **Analiza** na karcie  **aktywne reguły** .

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Cloud App Security, wpisz `SecurityAlert` w oknie zapytania. W obszarze schemat dzienników Cloud Discovery wpisz `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery pomaga wykrywać i identyfikować trendy poprzez agregowanie połączeń użytkowników podstawowych do aplikacji w chmurze.
>
> Ponieważ Cloud Discovery dane są agregowane dla każdego dnia, należy pamiętać, że dane z ostatnich 24 godzin nie zostaną odzwierciedlone na platformie Azure. W przypadku, gdy badanie niskiego poziomu wymaga większej ilości danych, należy to zrobić bezpośrednio w urządzeniu źródłowym lub usłudze, w której znajdują się dane pierwotne.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Microsoft Cloud App Security z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](./tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.