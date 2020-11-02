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
ms.date: 10/28/2020
ms.author: yelevin
ms.openlocfilehash: b769703f7e7054571a3b65a021c0123d8ae07078
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148239"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Włącz analizę zachowań użytkowników i jednostek (UEBA) na platformie Azure — wskaźnik 

> [!IMPORTANT]
>
> - Funkcja UEBA jest teraz **ogólnie dostępna** w następującej lokalizacje geograficzne i regionach na platformie Azure:
>    - Stany Zjednoczone Geografia
>    - Region Zachodni Europa
>    - Geografia Australia
>
> - We wszystkich innych lokalizacje geograficzne i regionach ta funkcja jest pozostawać w **publicznej wersji zapoznawczej** i jest udostępniana bez umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć lub wyłączyć tę funkcję (te wymagania wstępne nie są wymagane do korzystania z tej funkcji):

- Użytkownik musi być członkiem Azure Active Directory organizacji, a nie użytkownikiem-gościem.

- Użytkownik musi mieć przypisane role **administratora globalnego** lub **administratora zabezpieczeń** w usłudze Azure AD.

- Użytkownik musi mieć przypisaną co najmniej jedną z następujących **ról platformy Azure** ( [Dowiedz się więcej o funkcji RBAC na platformie Azure](roles.md)):
    - **Współautor Azure wskaźnikowego** na poziomie obszaru roboczego lub grupy zasobów.
    - **Log Analytics współautor** na poziomach grupy zasobów lub subskrypcji.

- Obszar roboczy nie może mieć żadnych zastosowanych blokad zasobów platformy Azure. [Dowiedz się więcej o blokowaniu zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Jak włączyć analizy zachowań użytkowników i jednostek

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **zachowanie jednostki** .

1. Pod nagłówkiem **Włącz** **opcję Włącz.**

1. Kliknij przycisk **Wybierz źródła danych** .

1. W okienku **Wybór źródła danych** zaznacz pola wyboru obok źródeł danych, dla których chcesz włączyć UEBA, a następnie wybierz pozycję **Zastosuj** .

    > [!NOTE]
    >
    > W dolnej połowie okienka **wyboru źródła danych** zostanie wyświetlona lista nieobsługiwanych źródeł danych UEBA. 
    >
    > Po włączeniu usługi UEBA będziesz mieć możliwość nawiązywania połączenia z nowymi źródłami danych, aby włączyć je do UEBA bezpośrednio z okienka Łącznik danych, jeśli są one UEBA.

1. Wybierz pozycję **Przejdź do wyszukiwania jednostek** . Spowoduje to przejście do okienka wyszukiwanie jednostek, które od teraz będzie widoczne w przypadku wybrania **zachowania jednostki** z głównego menu wskaźnikowego platformy Azure.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób włączania i konfigurowania funkcji analizy zachowań użytkowników i jednostek (UEBA) na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
