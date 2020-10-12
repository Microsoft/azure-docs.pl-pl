---
title: Pracuj ze zdarzeniami wskaźnikowych platformy Azure w wielu obszarach roboczych jednocześnie | Microsoft Docs
description: Jak wyświetlać zdarzenia w wielu obszarach roboczych jednocześnie na platformie Azure.
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
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83124187"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Pracuj ze zdarzeniami w wielu obszarach roboczych naraz 

 Aby w pełni wykorzystać możliwości platformy Azure, firma Microsoft zaleca korzystanie z środowiska jednego obszaru roboczego. Istnieją jednak przypadki użycia, które wymagają kilku obszarów roboczych, w niektórych przypadkach — na przykład przez [zarządzanego dostawcę usług zabezpieczeń (MSSP)](./multiple-tenants-service-providers.md) i jego klientów — między wieloma dzierżawcami. **Widok wielu obszarów roboczych** pozwala zobaczyć zdarzenia dotyczące zabezpieczeń i korzystać z nich w wielu obszarach roboczych jednocześnie, nawet między dzierżawcami, co pozwala na zapewnienie pełnego wglądu i kontroli nad czasem reakcji bezpieczeństwa w organizacji.

## <a name="entering-multiple-workspace-view"></a>Wprowadzanie wielu widoków obszaru roboczego

Po otwarciu funkcji wskaźnikowej platformy Azure zostanie wyświetlona lista wszystkich obszarów roboczych, do których masz prawa dostępu, we wszystkich wybranych dzierżawcach i subskrypcjach. Po lewej stronie nazwy każdego obszaru roboczego jest pole wyboru. Kliknięcie nazwy pojedynczego obszaru roboczego spowoduje przeniesienie do tego obszaru roboczego. Aby wybrać wiele obszarów roboczych, kliknij wszystkie odpowiednie pola wyboru, a następnie kliknij przycisk **Widok wielu obszarów roboczych** w górnej części strony.

> [!IMPORTANT]
> Widok wielu obszarów roboczych obsługuje obecnie maksymalnie 10 obszarów roboczych wyświetlanych jednocześnie. 
> 
> Jeśli sprawdzisz więcej niż 10 obszarów roboczych, zostanie wyświetlony komunikat ostrzegawczy.

Należy pamiętać, że na liście obszarów roboczych można zobaczyć katalog, subskrypcję, lokalizację i grupę zasobów skojarzoną z każdym obszarem roboczym. Katalog odpowiada dzierżawcy.

   ![Wybierz wiele obszarów roboczych](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Praca ze zdarzeniami

W **widoku wielu obszarów roboczych**jest dostępny tylko ekran **zdarzenia** . Wygląda i działa w większości sposobów, np. na ekranie zwykłe **zdarzenia** . Istnieje kilka istotnych różnic, chociaż:

   ![Wyświetlanie zdarzeń w wielu obszarach roboczych](./media/multiple-workspace-view/incidents.png)

- Liczniki w górnej części *zdarzenia otwartej*strony, *nowe incydenty*, *w toku*itp. — pokazują numery dla wszystkich wybranych obszarów roboczych zbiorczo.

- Zobaczysz incydenty ze wszystkich wybranych obszarów roboczych i katalogów (dzierżawców) na pojedynczej ujednoliconej liście. Oprócz filtrów na ekranie zwykłe **zdarzenia** można filtrować listę według obszaru roboczego i katalogu.

- Musisz mieć uprawnienia do odczytu i zapisu dla wszystkich obszarów roboczych, z których zostały wybrane zdarzenia. Jeśli masz tylko uprawnienia do odczytu w niektórych obszarach roboczych, zobaczysz komunikat ostrzegawczy, jeśli wybierzesz incydenty w tych obszarach roboczych. Nie będzie można modyfikować tych incydentów ani żadnych innych, które zostały wybrane razem z nimi (nawet jeśli masz uprawnienia do innych użytkowników).

- W przypadku wybrania jednego incydentu kliknij pozycję **Wyświetl pełne szczegóły** lub **Zbadaj**, a następnie w kontekście danych obszaru roboczego zdarzenia nie ma innych.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie pokazano, jak jednocześnie wyświetlać zdarzenia i korzystać z nich w wielu obszarach roboczych usługi Azure wskaźnikowych. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

