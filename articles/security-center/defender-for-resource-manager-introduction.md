---
title: Usługa Azure Defender dla Menedżer zasobów — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla Menedżer zasobów
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9591dae058a19cea73d88513b7c4ff4ab8f88045
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797747"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Wprowadzenie do usługi Azure Defender dla Menedżer zasobów

[Azure Resource Manager](../azure-resource-manager/management/overview.md) to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i tagi, można zabezpieczyć oraz zorganizować zasoby po wdrożeniu.

Warstwa zarządzania chmurą to kluczowa usługa połączona ze wszystkimi zasobami w chmurze. Z tego względu jest to również potencjalny obiekt docelowy dla osób atakujących. W związku z tym zaleca się ścisłe monitorowanie warstwy zarządzania zasobami przez zespoły operacji zabezpieczeń. 

Usługa Azure Defender dla Menedżer zasobów automatycznie monitoruje operacje zarządzania zasobami w organizacji, niezależnie od tego, czy są one wykonywane za pomocą Azure Portal, interfejsów API REST platformy Azure, interfejsu wiersza polecenia platformy Azure lub innych klientów programistycznych platformy Azure. Usługa Azure Defender uruchamia zaawansowaną analizę zabezpieczeń, aby wykrywać zagrożenia i ostrzegać o podejrzanych działaniach.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Wpisaną|**Usługa Azure Defender dla Menedżer zasobów** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Jakie korzyści ma usługa Azure Defender dla Menedżer zasobów?

Usługa Azure Defender dla Menedżer zasobów chroni przed problemami, w tym:

- **Podejrzane operacje związane z zarządzaniem zasobami**, takie jak operacje z podejrzanych adresów IP, wyłączanie ochrony przed złośliwym kodem i podejrzanych skryptów działających w ROZSZERZENIACH maszyn wirtualnych
- **Korzystanie z zestawów narzędzi wykorzystujących luki w zabezpieczeniach** , takich jak mikroserii lub PowerZure
- **Ruchy boczne** z warstwy zarządzania platformy Azure do płaszczyzny danych zasobów platformy Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagram przeglądu Azure Resource Manager":::

Pełna lista alertów dostarczonych przez usługę Azure Defender for Menedżer zasobów znajduje się na [stronie odwołania do alertów](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Jak zbadać alerty z usługi Azure Defender dla Menedżer zasobów

Alerty zabezpieczeń z usługi Azure Defender dla Menedżer zasobów są oparte na zagrożeniach wykrytych przez monitorowanie Azure Resource Manager operacji. Usługa Azure Defender używa wewnętrznych źródeł dzienników Azure Resource Manager oraz dziennika aktywności platformy Azure, a także do rejestrowania zdarzeń na poziomie subskrypcji na platformie Azure.

Dowiedz się więcej o [dzienniku aktywności platformy Azure](../azure-monitor/platform/activity-log.md).

Aby zbadać alerty zabezpieczeń z usługi Azure Defender dla Menedżer zasobów:

1. Otwórz dziennik aktywności platformy Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Jak otworzyć Dziennik aktywności platformy Azure":::

1. Filtruj zdarzenia do:
    - Subskrypcja wymieniona w alercie
    - Przedział czasu wykrytych działań
    - Powiązane konto użytkownika (jeśli dotyczy)

1. Poszukaj podejrzanych działań.

> [!TIP]
> W celu lepszego, bogatszego środowiska dochodzeniowego Przesyłaj strumieniowo dzienniki aktywności platformy Azure do usługi Azure, zgodnie z opisem w temacie [łączenie danych z dziennika aktywności platformy Azure](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla Menedżer zasobów. W przypadku pokrewnego materiału zapoznaj się z następującym artykułem: 

- Alerty zabezpieczeń mogą być generowane przez Security Center lub odebrane przez Security Center z różnych produktów zabezpieczeń. Aby wyeksportować wszystkie te alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub innych zewnętrznych narzędzi, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Włączanie usługi Azure Defender](security-center-pricing.md#enable-azure-defender)