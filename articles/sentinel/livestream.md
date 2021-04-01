---
title: Korzystanie z łowiectwa transmisję strumieniową na platformie Azure wskaźnikowej do wykrywania zagrożeń | Microsoft Docs
description: W tym artykule opisano, jak używać transmisję strumieniową łowiectwa na platformie Azure, aby śledzić dane.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2020
ms.author: yelevin
ms.openlocfilehash: 14928d3c94ced8d1cd0c12e76428be73b68b91d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84783167"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Wykrywanie zagrożeń przy użyciu transmisji strumieniowej na żywo wyszukiwania zagrożeń w usłudze Azure Sentinel

Za pomocą transmisję strumieniową łowiectwa można tworzyć interakcyjne sesje umożliwiające testowanie nowo utworzonych zapytań w miarę wystąpienia zdarzeń, otrzymywanie powiadomień z sesji po znalezieniu dopasowania i w razie potrzeby dochodzeń. Sesję transmisję strumieniową można szybko utworzyć przy użyciu dowolnego zapytania Log Analytics.

- **Testuj nowo utworzone zapytania jako wystąpienia zdarzeń**
    
    Zapytania można testować i dostosowywać bez żadnych konfliktów z bieżącymi regułami, które są aktywnie stosowane do zdarzeń. Po potwierdzeniu, że nowe zapytania działają zgodnie z oczekiwaniami, można je łatwo podwyższyć do niestandardowych reguł alertów, wybierając opcję podniesienia poziomu sesji do alertu.

- **Otrzymuj powiadomienia o wystąpieniu zagrożeń**
    
    Źródła danych zagrożeń można porównać z zagregowanymi danymi dzienników i otrzymywać powiadomienia o wystąpieniu dopasowania. Źródła danych zagrożeń to ciągłe strumienie danych, które są związane z potencjalnymi lub obecnymi zagrożeniami, więc powiadomienie może wskazywać na potencjalne zagrożenie dla organizacji. Utwórz sesję transmisję strumieniową zamiast niestandardowej reguły alertu, jeśli chcesz otrzymywać powiadomienia o potencjalnym problemie bez nadmiaru utrzymania niestandardowej reguły alertu.

- **Badania uruchamiania**
    
    Jeśli istnieje aktywne badanie, które obejmuje zasób, taki jak Host lub użytkownik, można wyświetlić określone (lub dowolne) działanie w danych dziennika, które występuje w tym elemencie zawartości. Możesz otrzymywać powiadomienia o wystąpieniu tego działania.


## <a name="create-a-livestream-session"></a>Tworzenie sesji transmisję strumieniową

Sesję transmisję strumieniową można utworzyć na podstawie istniejącego zapytania polowania lub utworzyć sesję od podstaw.

1. W Azure Portal przejdź do polowania dotyczącego   >  **zarządzania zagrożeniami** wskaźnikiem  >  .

1. Aby utworzyć sesję transmisję strumieniową na podstawie zapytania polowania:
    
    1. Na karcie **zapytania** Znajdź zapytanie polowające do użycia.
    1. Kliknij prawym przyciskiem myszy zapytanie i wybierz polecenie **Dodaj do transmisję strumieniową**. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Utwórz sesję transmisję strumieniową na podstawie zapytania polowania wskaźnikowego platformy Azure](./media/livestream/livestream-from-query.png)

1. Aby utworzyć sesję transmisję strumieniową od podstaw: 
    
    1. Wybierz kartę **transmisję strumieniową**
    1. Kliknij pozycję **+ Nowy transmisję strumieniową**.
    
1. W okienku **transmisję strumieniową** :
    
    - Jeśli rozpoczęto transmisję strumieniową z zapytania, przejrzyj zapytanie i wprowadź wszelkie zmiany, które chcesz wprowadzić.
    - Jeśli rozpoczęto transmisję strumieniową od podstaw, Utwórz zapytanie. 

1. Wybierz opcję **Odtwórz** na pasku poleceń.
    
    Pasek stanu na pasku poleceń wskazuje, czy sesja transmisję strumieniową jest uruchomiona, czy wstrzymana. W poniższym przykładzie jest uruchomiona sesja:
    
    > [!div class="mx-imgBorder"]
    > ![Utwórz sesję transmisję strumieniową z poziomu polowania wskaźnikowego platformy Azure](./media/livestream/livestream-session.png)

1. Wybierz pozycję **Zapisz** na pasku poleceń.
    
    Dopóki nie wybierzesz opcji **Wstrzymaj**, sesja będzie nadal działać do momentu wylogowania się z Azure Portal.

## <a name="view-your-livestream-sessions"></a>Wyświetlanie sesji transmisję strumieniową

1. W Azure Portal przejdź do karty **wskaźnik**  >  **zagrożeń zarządzania Threat**  >    >  **transmisję strumieniową** .

1. Wybierz sesję transmisję strumieniową, którą chcesz wyświetlić lub edytować. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Utwórz sesję transmisję strumieniową na podstawie zapytania polowania wskaźnikowego platformy Azure](./media/livestream/livestream-tab.png)
    
    Wybrana sesja transmisję strumieniową zostanie otwarta, aby można było odtwarzać, wstrzymywać, edytować i tak dalej.

## <a name="receive-notifications-when-new-events-occur"></a>Odbieraj powiadomienia, gdy wystąpią nowe zdarzenia

Ponieważ powiadomienia transmisję strumieniową o nowych zdarzeniach używają powiadomień Azure Portal, te powiadomienia są wyświetlane po każdym użyciu Azure Portal. Na przykład:

![Powiadomienie Azure Portal dla transmisję strumieniową](./media/livestream/notification.png)

Wybierz powiadomienie, aby otworzyć okienko **transmisję strumieniową** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Podnieś poziom sesji transmisję strumieniową do alertu

Można podwyższyć poziom sesji transmisję strumieniową do nowego alertu, wybierając **Podnieś poziom do alertu** na pasku poleceń na odpowiedniej sesji transmisję strumieniową:

> [!div class="mx-imgBorder"]
> ![Podnieś poziom sesji transmisję strumieniową do alertu](./media/livestream/elevate-to-alert.png)

Ta akcja powoduje otwarcie Kreatora tworzenia reguły, który jest wstępnie wypełniony kwerendą skojarzoną z sesją transmisję strumieniową.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z łowiectwa transmisję strumieniową na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z notesów do uruchamiania zautomatyzowanych kampanii łowieckich](notebooks.md)
