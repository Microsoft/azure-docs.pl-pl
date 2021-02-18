---
title: Konfigurowanie zbierania danych dla agenta Azure Monitor (wersja zapoznawcza)
description: Opisuje sposób tworzenia reguły zbierania danych w celu zbierania danych z maszyn wirtualnych przy użyciu agenta Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 6e65b828e2cf86614707c8a859abc0c4f2395899
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616363"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurowanie zbierania danych dla agenta Azure Monitor (wersja zapoznawcza)

Zasady zbierania danych (DCR) definiują dane wprowadzane do Azure Monitor i określają miejsce, w którym powinny być wysyłane. W tym artykule opisano sposób tworzenia reguły zbierania danych w celu zbierania danych z maszyn wirtualnych przy użyciu agenta Azure Monitor.

Aby uzyskać pełny opis reguł zbierania danych, zobacz [reguły zbierania danych w Azure monitor (wersja zapoznawcza)](data-collection-rule-overview.md).

> [!NOTE]
> W tym artykule opisano sposób konfigurowania danych dla maszyn wirtualnych za pomocą agenta Azure Monitor, który jest obecnie w wersji zapoznawczej. Zobacz [Omówienie agentów Azure monitor](agents-overview.md) , aby uzyskać opis agentów, którzy są ogólnie dostępni i jak używać ich do zbierania danych.

## <a name="data-collection-rule-associations"></a>Skojarzenia reguł zbierania danych

Aby zastosować DCR do maszyny wirtualnej, należy utworzyć skojarzenie dla maszyny wirtualnej. Maszyna wirtualna może mieć skojarzenie z wieloma Dcrami, a DCR może być skojarzonych wiele maszyn wirtualnych. Dzięki temu można zdefiniować zestaw DCR, każdy pasujący do określonego wymagania i zastosować je tylko do maszyn wirtualnych, które mają zastosowanie. 

Rozważmy na przykład środowisko z zestawem maszyn wirtualnych z działającą aplikacją biznesową i innymi osobami, na których działa SQL Server. Może istnieć jedna domyślna reguła zbierania danych, która ma zastosowanie do wszystkich maszyn wirtualnych i oddzielnych reguł zbierania danych, które zbierają dane przeznaczone dla aplikacji biznesowych i dla SQL Server. Skojarzenia dla maszyn wirtualnych z regułami zbierania danych wyglądają podobnie jak na poniższym diagramie.

![Diagram przedstawia maszyny wirtualne hostujący aplikację biznesową i SQL Server skojarzone z regułami zbierania danych o nazwie Central-i t-default i aplikacji LOB dla aplikacji biznesowych, a Central-i t-default oraz s q l dla SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Utwórz regułę i skojarzenie w Azure Portal

Za pomocą Azure Portal można utworzyć regułę zbierania danych i skojarzyć maszyny wirtualne w subskrypcji z tą regułą. Agent Azure Monitor zostanie zainstalowany automatycznie, a zarządzana tożsamość utworzona dla każdej maszyny wirtualnej, która nie jest jeszcze zainstalowana.

> [!IMPORTANT]
> Istnieje obecnie znany problem, w którym zasada zbierania danych tworzy tożsamość zarządzaną na maszynie wirtualnej, która ma już tożsamość zarządzaną przez użytkownika, tożsamość przypisana przez użytkownika jest wyłączona.

W menu **Azure monitor** w Azure Portal wybierz pozycję **reguły zbierania danych** z sekcji **Ustawienia** . Kliknij przycisk **Dodaj** , aby dodać nową regułę zbierania danych i przypisanie.

[![Reguły zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Kliknij przycisk **Dodaj** , aby utworzyć nową regułę i zestaw skojarzeń. Podaj **nazwę reguły** i określ **subskrypcję** oraz **grupę zasobów**. Określa miejsce, w którym zostanie utworzony DCR. Maszyny wirtualne i ich skojarzenia mogą znajdować się w dowolnej subskrypcji lub grupie zasobów w dzierżawie.

[![Podstawy reguł zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Na karcie **maszyny wirtualne** Dodaj maszyny wirtualne, które powinny mieć zastosowana reguła zbierania danych. Należy wymienić zarówno maszyny wirtualne platformy Azure, jak i serwery z obsługą usługi Azure Arc w Twoim środowisku. Agent Azure Monitor zostanie zainstalowany na maszynach wirtualnych, które nie są jeszcze zainstalowane.

[![Maszyna wirtualna z regułą zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Na karcie **zbieranie i dostarczanie** kliknij pozycję **Dodaj źródło danych** , aby dodać źródło danych i zestaw docelowy. Wybierz **Typ źródła danych**, a zostaną wyświetlone odpowiednie szczegóły do wybrania. W przypadku liczników wydajności można wybrać ze wstępnie zdefiniowanego zestawu obiektów i ich częstotliwość próbkowania. Dla zdarzeń można wybrać spośród zestawu dzienników lub obiektów oraz poziomu ważności. 

[![Podstawowe źródło danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Aby określić inne dzienniki i liczniki wydajności z [aktualnie obsługiwanych źródeł danych](azure-monitor-agent-overview.md#data-sources-and-destinations) lub filtrować zdarzenia przy użyciu zapytań XPath, wybierz opcję **niestandardowe**. Następnie można określić [wyrażenie XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) dla konkretnych wartości do zebrania. Przykłady można znaleźć w [przykładach DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Niestandardowe źródło danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Na karcie **miejsce docelowe** Dodaj co najmniej jeden obiekt docelowy dla źródła danych. Zdarzenia systemu Windows i źródła danych dziennika systemowego mogą być wysyłane tylko do dzienników Azure Monitor. Liczniki wydajności mogą wysyłać do Azure Monitor metryk i dzienników Azure Monitor.

[![Element docelowy](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Kliknij pozycję **Dodaj źródło danych** , a następnie **Przejrzyj i Utwórz** , aby przejrzeć szczegóły reguły zbierania danych i powiązania z zestawem maszyn wirtualnych. Kliknij przycisk **Utwórz** , aby go utworzyć.

> [!NOTE]
> Po utworzeniu reguły zbierania danych i skojarzeniach dane będą wysyłane do miejsc docelowych, które mogą upłynąć do 5 minut.


## <a name="create-rule-and-association-using-rest-api"></a>Tworzenie reguły i skojarzenia przy użyciu interfejsu API REST

Wykonaj poniższe kroki, aby utworzyć regułę zbierania danych i skojarzenia przy użyciu interfejsu API REST.

1. Ręcznie Utwórz plik DCR przy użyciu formatu JSON pokazanego w [przykładzie DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Utwórz regułę przy użyciu [interfejsu API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Utwórz skojarzenie dla każdej maszyny wirtualnej z regułą zbierania danych przy użyciu [interfejsu API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Utwórz skojarzenie przy użyciu szablonu Menedżer zasobów

Nie można utworzyć reguły zbierania danych przy użyciu szablonu Menedżer zasobów, ale można utworzyć skojarzenie między maszyną wirtualną platformy Azure lub serwerem z obsługą usługi Azure ARC przy użyciu szablonu Menedżer zasobów. Zobacz [przykłady szablonów Menedżer zasobów dla reguł zbierania danych w Azure monitor](../samples/resource-manager-data-collection-rules.md) dla przykładowych szablonów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [agencie Azure monitor](azure-monitor-agent-overview.md).
- Dowiedz się więcej na temat [reguł zbierania danych](data-collection-rule-overview.md).
