---
title: Szybka odpowiedź na platformie Azure DDoS
description: Dowiedz się, jak angażować ekspertów DDoS podczas aktywnego ataku na potrzeby wyspecjalizowanej pomocy technicznej.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8e860bf47420f2b58c44df695da7761bcc2aa0ce
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521785"
---
# <a name="azure-ddos-rapid-response"></a>Szybka odpowiedź na platformie Azure DDoS

Podczas aktywnego dostępu Azure DDoS Protection klienci standardowi mają dostęp do zespołu szybkiego reagowania na DDoS (DRR), który może pomóc w zbadaniu ataku podczas przeprowadzania ataku i analizy po ataku.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony programu Azure DDoS Standard](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Kiedy należy angażować DRR

DRR należy podejmować tylko wtedy, gdy: 

- W trakcie ataku DDoS, jeśli okaże się, że wydajność chronionego zasobu jest poważnie obniżona, lub zasób nie jest dostępny. 
- Podejrzewasz, że zasób jest objęty DDoS atakiem, ale usługa DDoS Protection nie ogranicza skutecznego ataku.
- Planujesz zdarzenie wirusowe, które znacząco zwiększy ruch sieciowy.
- W przypadku ataków mających krytyczny wpływ na działalność biznesową.

## <a name="engage-drr-during-an-active-attack"></a>Zaangażuj DRR podczas aktywnego ataku

1. Z Azure Portal podczas tworzenia nowego żądania obsługi wybierz **typ problemu** jako techniczny.
2. Wybierz pozycję **Usługa** jako **Ochrona DDoS**.
3. Wybierz zasób z menu rozwijanego zasób. _Musisz wybrać plan DDoS połączony z siecią wirtualną, która jest chroniona przez DDoS Protection Standard, aby DRR._

    ![Wybierz zasób](./media/ddos-rapid-response/choose-resource.png)

4. Na stronie następny **problem** wybierz **ważność** jako wpływ krytyczny i **typ problemu** na "w trakcie ataku".

    ![PSeverity i typ problemu](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Uzupełnij dodatkowe szczegóły i prześlij żądanie pomocy technicznej.

DRR jest zgodna z modelem wsparcia Azure Rapid Response. Aby uzyskać więcej informacji na temat szybkiej odpowiedzi [, zobacz zakres pomocy technicznej i czas odpowiedzi](https://azure.microsoft.com/en-us/support/plans/response/) .

Aby dowiedzieć się więcej, Przeczytaj [dokumentację standardową DDoS Protection](./ddos-protection-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przetestować symulacje](test-through-simulations.md).
- Dowiedz się [, jak wyświetlać i konfigurować telemetrię ochrony DDoS](telemetry.md).
- Dowiedz się [, jak wyświetlać i konfigurować rejestrowanie diagnostyczne DDoS](diagnostic-logging.md).
