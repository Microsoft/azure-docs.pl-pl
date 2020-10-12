---
title: Korzystanie z analizy zachowań jednostek do wykrywania zaawansowanych zagrożeń | Microsoft Docs
description: Włącz analizę zachowań użytkowników i jednostek w usłudze Azure wskaźnikowych i skonfiguruj źródła danych
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
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997117"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Włącz analizę zachowań użytkowników i jednostek (UEBA) na platformie Azure — wskaźnik 



## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć przypisane role **administratora globalnego** lub **administratora zabezpieczeń** w usłudze Azure AD, aby można było włączyć lub wyłączyć UEBA, ale nie uruchamiać go.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Jak włączyć analizy zachowań użytkowników i jednostek

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **zachowanie jednostki (wersja zapoznawcza)**.

1. Pod nagłówkiem **Włącz** **opcję Włącz.**

1. Kliknij przycisk **Wybierz źródła danych** .

1. W okienku **Wybór źródła danych** zaznacz pola wyboru obok źródeł danych, dla których chcesz włączyć UEBA, a następnie wybierz pozycję **Zastosuj**.

    > [!NOTE]
    >
    > W dolnej połowie okienka **wyboru źródła danych** zostanie wyświetlona lista nieobsługiwanych źródeł danych UEBA. 
    >
    > Po włączeniu usługi UEBA będziesz mieć możliwość nawiązywania połączenia z nowymi źródłami danych, aby włączyć je do UEBA bezpośrednio z okienka Łącznik danych, jeśli są one UEBA.

1. Wybierz pozycję **Przejdź do wyszukiwania jednostek**. Spowoduje to przejście do okienka wyszukiwanie jednostek, które od teraz będzie widoczne w przypadku wybrania **zachowania jednostki** z głównego menu wskaźnikowego platformy Azure.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób włączania i konfigurowania funkcji analizy zachowań użytkowników i jednostek (UEBA) na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
