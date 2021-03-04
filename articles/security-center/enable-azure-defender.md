---
title: Włączanie zintegrowanej ochrony obciążeń Azure Security Center
description: Dowiedz się, jak włączyć usługę Azure Defender, aby zwiększyć ochronę Azure Security Center do zasobów hybrydowych i wielochmurowych
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108919"
---
# <a name="quickstart-enable-azure-defender"></a>Szybki Start: Włączanie usługi Azure Defender

Aby dowiedzieć się więcej o korzyściach płynących z usługi Azure Defender, zobacz [Security Center Free vs Azure Defender](security-center-pricing.md).

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby przewodników Szybki Start i samouczków Security Center należy włączyć usługę Azure Defender. 

Możesz chronić całą subskrypcję platformy Azure za pomocą usługi Azure Defender, a ochrona będzie dziedziczona przez wszystkie zasoby w ramach subskrypcji.

Dostępna jest bezpłatna 30-dniowa wersja próbna. Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i według regionu, zobacz [Security Center Cennik](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Włączanie usługi Azure Defender z poziomu Azure Portal

Aby włączyć wszystkie funkcje Security Center w tym funkcje ochrony przed zagrożeniami, należy włączyć usługę Azure Defender w ramach subskrypcji zawierającej odpowiednie obciążenia. Włączenie tej opcji na poziomie obszaru roboczego nie pozwala na dostęp do programu VM, adaptacyjne kontrolki aplikacji i wykrywanie sieci dla zasobów platformy Azure. Ponadto jedyne plany usługi Azure Defender dostępne na poziomie obszaru roboczego to usługa Azure Defender dla serwerów i usługi Azure Defender dla serwerów SQL na komputerach.

- **Usługę Azure Defender dla kont magazynu** można włączyć na poziomie subskrypcji lub na poziomie zasobu
- **Usługę Azure Defender dla bazy danych SQL** można włączyć na poziomie subskrypcji lub na poziomie zasobu
- Ochronę przed zagrożeniami dla **Azure Database for MariaDB/MySQL/PostgreSQL** można włączyć tylko na poziomie zasobów

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Aby włączyć usługę Azure Defender w Twoich subskrypcjach i obszarach roboczych:

- Aby włączyć usługę Azure Defender w jednej subskrypcji:

    1. W menu głównym Security Center wybierz pozycję **cennik & ustawienia**.
    1. Wybierz subskrypcję lub obszar roboczy, który chcesz chronić.
    1. Wybierz pozycję **Azure Defender on** , aby przeprowadzić uaktualnienie.
    1. Wybierz pozycję **Zapisz**.

    > [!TIP]
    > Należy zauważyć, że każdy plan w usłudze Azure Defender jest naliczany osobno i może być ustawiony indywidualnie lub wyłączony. Na przykład możesz chcieć wyłączyć usługę Azure Defender dla App Service w subskrypcjach, które nie mają skojarzonego planu Azure App Service. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Strona cennika Security Center w portalu":::

- Aby włączyć usługę Azure Defender dla wielu subskrypcji lub obszarów roboczych:

    1. Na pasku bocznym Security Center wybierz opcję **wprowadzenie**.

        Karta **Uaktualnij** zawiera listę subskrypcji i obszarów roboczych kwalifikujących się do dołączenia.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Karta Uaktualnij na stronie Wprowadzenie"::: 

    1. Z listy **wybierz subskrypcje i obszary robocze, aby włączyć usługę Azure Defender** , wybierz subskrypcje i obszary robocze do uaktualnienia, a następnie wybierz pozycję **Uaktualnij** , aby włączyć usługę Azure Defender.

       - Jeśli wybierzesz subskrypcje i obszary robocze, które nie są uprawnione do korzystania z wersji próbnej, następnym krokiem spowoduje ich uaktualnienie, a opłaty zostaną naliczone.
       - W przypadku wybrania obszaru roboczego, który kwalifikuje się do korzystania z bezpłatnej wersji próbnej, następnym krokiem będzie rozpoczęcie korzystania z wersji próbnej.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Uaktualnij wszystkie wybrane obszary robocze i subskrypcje na stronie pierwsze kroki":::


## <a name="next-steps"></a>Następne kroki

Po włączeniu usługi Azure Defender należy włączyć automatyczne zbieranie danych przez wymaganych agentów i rozszerzeń opisanych w temacie [Automatyczne Inicjowanie obsługi administracyjnej agentów i rozszerzeń z Azure Security Center](security-center-enable-data-collection.md).