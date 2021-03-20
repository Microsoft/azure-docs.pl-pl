---
title: Monitorowanie kondycji zasobów Azure CDN | Microsoft Docs
description: Dowiedz się, jak monitorować kondycję zasobów Azure CDN przy użyciu Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778173"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorowanie kondycji zasobów usługi Azure CDN
  
Azure CDN Kondycja zasobów jest podzbiorem [usługi Azure Resource Health](../service-health/resource-health-overview.md).  Usługa Azure Resource Health służy do monitorowania kondycji zasobów sieci CDN i uzyskiwania odpowiednich wskazówek dotyczących rozwiązywania problemów.

>[!IMPORTANT] 
>Azure CDN kondycji zasobów obecnie konta dotyczące kondycji globalnych funkcji dostarczania i interfejsu API usługi CDN.  Azure CDN kondycji zasobów nie weryfikuje poszczególnych punktów końcowych usługi CDN.
>
>Sygnały sygnalizujące Azure CDN kondycję zasobów mogą wynosić do 15 minut.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak znaleźć Azure CDN kondycji zasobów

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego profilu CDN.

2. Kliknij przycisk **Ustawienia** .

    ![Przycisk Ustawienia](./media/cdn-resource-health/cdn-profile-settings.png)

3. W obszarze *Pomoc techniczna i rozwiązywanie problemów* kliknij pozycję **kondycja zasobu**.

    ![Kondycja zasobów usługi CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Możesz również znaleźć zasoby usługi CDN wymienione na kafelku *Kondycja zasobów* w bloku *Pomoc i obsługa techniczna* .  Aby szybko uzyskać *Pomoc i obsłużyć wsparcie* , kliknij kółko **?** w prawym górnym rogu portalu.
>
> ![Pomoc i obsługa techniczna](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Komunikaty specyficzne dla Azure CDN

Stany dotyczące kondycji zasobów Azure CDN można znaleźć poniżej.

|Komunikat | Zalecana akcja |
|---|---|
|Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany | Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany.|
|Niestety, usługa zarządzania sieci CDN jest aktualnie niedostępna | Wróć tutaj, aby sprawdzić aktualizacje stanu; Jeśli problem będzie się powtarzać po upływie oczekiwanego czasu rozwiązania problemu, skontaktuj się z pomocą techniczną.|
|Niestety, na punkty końcowe sieci CDN mogą mieć wpływ trwające problemy z niektórymi z naszych dostawców sieci CDN | Wróć tutaj, aby sprawdzić aktualizacje stanu; Skorzystaj z narzędzia do rozwiązywania problemów, aby dowiedzieć się, jak przetestować źródło i punkt końcowy usługi CDN; Jeśli problem będzie się powtarzać po upływie oczekiwanego czasu rozwiązania problemu, skontaktuj się z pomocą techniczną. |
|Niestety, w przypadku zmiany konfiguracji punktu końcowego sieci CDN występują opóźnienia propagacji | Wróć tutaj, aby sprawdzić aktualizacje stanu; Jeśli zmiany konfiguracji nie są w pełni rozmnożone w oczekiwanym czasie, skontaktuj się z pomocą techniczną.|
|Niestety, wystąpiły problemy z ładowaniem portalu dodatkowego | Wróć tutaj, aby sprawdzić aktualizacje stanu; Jeśli problem będzie się powtarzać po upływie oczekiwanego czasu rozwiązania problemu, skontaktuj się z pomocą techniczną.|
Niestety, występują problemy z niektórymi z naszych dostawców sieci CDN | Wróć tutaj, aby sprawdzić aktualizacje stanu; Jeśli problem będzie się powtarzać po upływie oczekiwanego czasu rozwiązania problemu, skontaktuj się z pomocą techniczną. |

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z omówieniem usługi Azure Resource Health](../service-health/resource-health-overview.md)
- [Rozwiązywanie problemów z kompresją usługi CDN](./cdn-troubleshoot-compression.md)
- [Rozwiązywanie problemów z błędami 404](./cdn-troubleshoot-endpoint.md)