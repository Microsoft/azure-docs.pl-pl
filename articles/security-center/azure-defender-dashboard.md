---
title: Pulpit nawigacyjny usługi Azure Defender i jej funkcje
description: Dowiedz się więcej o funkcjach pulpitu nawigacyjnego usługi Azure Defender.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096908"
---
# <a name="the-azure-defender-dashboard"></a>Pulpit nawigacyjny usługi Azure Defender

Pulpit nawigacyjny usługi Azure Defender oferuje następujące informacje:

- Wgląd w pokrycie usługi Azure Defender dla różnych typów zasobów
- Linki do konfigurowania funkcji zaawansowanej ochrony przed zagrożeniami
- Stan dołączania i Instalacja agenta
- Alerty wykrywania zagrożeń usługi Azure Defender 

Aby uzyskać dostęp do pulpitu nawigacyjnego usługi Azure Defender, wybierz pozycję **Azure Defender** w sekcji Zabezpieczenia chmury w menu Security Center.

## <a name="whats-shown-on-the-dashboard"></a>Co jest wyświetlane na pulpicie nawigacyjnym?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Przykład pulpitu nawigacyjnego usługi Azure Defender" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Pulpit nawigacyjny zawiera następujące sekcje:

1. **Pokrycie na platformie Azure Defender** — tutaj możesz zobaczyć typy zasobów, które znajdują się w subskrypcji i kwalifikują się do ochrony przez usługę Azure Defender. Wszędzie tam, gdzie ma to zastosowanie, będziesz mieć możliwość uaktualnienia. Jeśli chcesz uaktualnić wszystkie możliwe kwalifikujące się zasoby, wybierz pozycję **Uaktualnij wszystko**.

2. **Alerty zabezpieczeń** — gdy usługa Azure Defender wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert. Te alerty opisują szczegóły zasobów, których to dotyczy, sugerowane kroki zaradcze, a w niektórych przypadkach opcja wyzwalająca aplikację logiki w odpowiedzi. Wybranie dowolnego miejsca w tym grafie spowoduje otwarcie **strony alerty zabezpieczeń**.

3. **Ochrona zaawansowana** — usługa Azure Defender obejmuje wiele zaawansowanych funkcji ochrony przed zagrożeniami dla maszyn wirtualnych, baz danych SQL, kontenerów, aplikacji sieci Web, sieci i innych. W tej sekcji zaawansowanej ochrony można zobaczyć stan zasobów w wybranych subskrypcjach dla każdej z tych ochrony. Wybierz dowolne z nich, aby przejść bezpośrednio do obszaru konfiguracji dla tego typu ochrony.

4. **Insights** — to stopniowe przewijanie wiadomości, sugerowane odczyty i alerty o wysokim priorytecie dają Security Center wgląd w kwestie dotyczące zabezpieczeń, które są istotne dla Ciebie i Twojej subskrypcji. Niezależnie od tego, czy jest to lista CVEs o wysokiej ważności odnalezionych na maszynach wirtualnych za pomocą narzędzia do analizy luk w zabezpieczeniach, czy też nowego wpisu w blogu, który jest członkiem zespołu Security Center, znajdziesz go w okienku Insights (szczegółowe informacje) na **pulpicie nawigacyjnym usługi Azure Defender**.




## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o pulpicie nawigacyjnym usługi Azure Defender. 

Aby uzyskać więcej informacji na temat usługi Azure Defender, zobacz [wprowadzenie do usługi Azure Defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](enable-azure-defender.md)