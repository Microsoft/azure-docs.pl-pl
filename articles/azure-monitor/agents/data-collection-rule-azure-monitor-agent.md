---
title: Konfigurowanie zbierania danych dla agenta Azure Monitor (wersja zapoznawcza)
description: Opisuje sposób tworzenia reguły zbierania danych w celu zbierania danych z maszyn wirtualnych przy użyciu Azure Monitor agenta.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478373"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurowanie zbierania danych dla agenta Azure Monitor (wersja zapoznawcza)

Reguły zbierania danych (DCR) definiują dane Azure Monitor i określają, dokąd mają być wysyłane. W tym artykule opisano sposób tworzenia reguły zbierania danych w celu zbierania danych z maszyn wirtualnych przy użyciu Azure Monitor danych.

Aby uzyskać pełny opis reguł zbierania danych, zobacz Reguły zbierania danych [w Azure Monitor (wersja zapoznawcza).](data-collection-rule-overview.md)

> [!NOTE]
> W tym artykule opisano sposób konfigurowania danych dla maszyn wirtualnych przy użyciu agenta Azure Monitor, który jest obecnie w wersji zapoznawczej. Zobacz [Omówienie Azure Monitor agentów,](agents-overview.md) aby uzyskać opis ogólnie dostępnych agentów oraz sposób ich używania do zbierania danych.

## <a name="data-collection-rule-associations"></a>Skojarzenia reguł zbierania danych

Aby zastosować dcr do maszyny wirtualnej, należy utworzyć skojarzenie dla maszyny wirtualnej. Maszyna wirtualna może mieć skojarzenie z wieloma kontrolerami domeny, a z nią może być skojarzonych wiele maszyn wirtualnych. Dzięki temu można zdefiniować zestaw kontrolerów domeny, z których każdy pasuje do określonego wymagania, i zastosować je tylko do maszyn wirtualnych, na których mają zastosowanie. 

Rozważmy na przykład środowisko z zestawem maszyn wirtualnych z aplikacją biznesową i innymi uruchomionymi SQL Server. Możesz mieć jedną domyślną regułę zbierania danych, która ma zastosowanie do wszystkich maszyn wirtualnych i oddzielnych reguł zbierania danych, które zbierają dane specjalnie dla aplikacji biznesowych i dla SQL Server. Skojarzenia maszyn wirtualnych z regułami zbierania danych będą wyglądać podobnie do poniższego diagramu.

![Diagram przedstawiający maszyny wirtualne hostujących aplikacje biznesowe i SQL Server skojarzone z regułami zbierania danych o nazwach central-i t-default i lob-app dla aplikacji biznesowych oraz central-i t-default i s q l dla SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Tworzenie reguły i skojarzenia w Azure Portal

Za pomocą tej Azure Portal utworzyć regułę zbierania danych i skojarzyć maszyny wirtualne w ramach subskrypcji z regułą. Agent Azure Monitor zostanie automatycznie zainstalowany i zostanie utworzona tożsamość zarządzana dla wszystkich maszyn wirtualnych, które jeszcze go nie mają.

> [!IMPORTANT]
> Obecnie występuje znany problem: jeśli reguła zbierania danych tworzy tożsamość zarządzaną na maszynie wirtualnej, która ma już tożsamość zarządzaną przypisaną przez użytkownika, tożsamość przypisana przez użytkownika jest wyłączona.

W **Azure Monitor** menu w Azure Portal wybierz pozycję Reguły zbierania **danych** w **sekcji** Ustawienia. Kliknij **przycisk Dodaj,** aby dodać nową regułę i przypisanie zbierania danych.

[![Reguły zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Kliknij **przycisk Dodaj,** aby utworzyć nową regułę i zestaw skojarzeń. Podaj nazwę **reguły i** określ **subskrypcję i** **grupę zasobów.** Określa, gdzie zostanie utworzony dcr. Maszyny wirtualne i ich skojarzenia mogą być w dowolnej subskrypcji lub grupie zasobów w dzierżawie.

[![Podstawy reguły zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Na karcie **Maszyny wirtualne** dodaj maszyny wirtualne, które powinny mieć zastosowaną regułę zbierania danych. Na liście powinny być Azure Arc maszyny wirtualne platformy Azure i serwery z włączoną obsługą sieci. Agent Azure Monitor zostanie zainstalowany na maszynach wirtualnych, na których jeszcze go nie zainstalowano.

[![Maszyny wirtualne reguły zbierania danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Na karcie **Zbieranie i dostarczanie** kliknij pozycję Dodaj źródło **danych,** aby dodać źródło danych i zestaw docelowy. Wybierz typ **źródła danych**. Zostaną wyświetlone odpowiednie szczegóły do wybrania. W przypadku liczników wydajności można wybrać wstępnie zdefiniowany zestaw obiektów i ich częstotliwość próbkowania. W przypadku zdarzeń można wybrać z zestawu dzienników lub obiektów oraz poziom ważności. 

[![Podstawowe źródło danych](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Aby określić inne dzienniki i liczniki wydajności z aktualnie obsługiwanych [źródeł](azure-monitor-agent-overview.md#data-sources-and-destinations) danych lub filtrować zdarzenia przy użyciu zapytań XPath, wybierz pozycję **Niestandardowy**. Następnie można określić wartość [XPath dla ](https://www.w3schools.com/xml/xpath_syntax.asp) dowolnych określonych wartości do zebrania. Przykłady można znaleźć w przykładach w [przykładzie dcr.](data-collection-rule-overview.md#sample-data-collection-rule)

[![Źródło danych niestandardowe](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Na karcie **Miejsce** docelowe dodaj co najmniej jedną lokalizację docelową dla źródła danych. Źródła danych zdarzeń systemu Windows i dziennika systemowego mogą wysyłać tylko do Azure Monitor dzienników. Liczniki wydajności można wysyłać do Azure Monitor metryk i Azure Monitor dzienników.

[![Element docelowy](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Kliknij **pozycję Dodaj źródło danych,** a następnie pozycję Przejrzyj **i** utwórz, aby przejrzeć szczegóły reguły zbierania danych i skojarzenia z zestawem maszyn wirtualnych. Kliknij **przycisk Utwórz,** aby go utworzyć.

> [!NOTE]
> Po utworzeniu reguły zbierania danych i skojarzeń do wysłania danych do miejsc docelowych może upłynieć do 5 minut.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Ograniczanie zbierania danych za pomocą niestandardowych zapytań XPath
Ponieważ opłaty są naliczane za wszystkie dane zebrane w obszarze roboczym usługi Log Analytics, należy zbierać tylko te dane, których potrzebujesz. Przy użyciu podstawowej konfiguracji w Azure Portal, masz tylko ograniczoną możliwość filtrowania zdarzeń do zbierania. W przypadku dzienników aplikacji i systemu są to wszystkie dzienniki o określonej ważności. W przypadku dzienników zabezpieczeń są to wszystkie dzienniki powodzenia inspekcji lub wszystkie dzienniki niepowodzeń inspekcji.

Aby określić dodatkowe filtry, należy użyć opcji Konfiguracja niestandardowa i określić element XPath, który odfiltrowuje zdarzenia, których nie ma. Wpisy XPath są zapisywane w postaci `LogName!XPathQuery` . Na przykład możesz chcieć zwrócić tylko zdarzenia z dziennika zdarzeń aplikacji o identyfikatorze zdarzenia 1035. Zapytanie XPathQuery dla tych zdarzeń to `*[System[EventID=1035]]` . Ponieważ chcesz pobrać zdarzenia z dziennika zdarzeń aplikacji, ścieżka XPath będzie `Application!*[System[EventID=1035]]`

Zobacz [ograniczenia programu XPath 1.0,](/windows/win32/wes/consuming-events#xpath-10-limitations) aby uzyskać listę ograniczeń w programie XPath obsługiwanym przez dziennik zdarzeń systemu Windows.

> [!TIP]
> Użyj polecenia cmdlet programu PowerShell z parametrem , aby przetestować `Get-WinEvent` `FilterXPath` poprawność zapytania XPathQuery. Poniższy skrypt przedstawia przykład.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Jeśli są zwracane zdarzenia, zapytanie jest prawidłowe.
> - Jeśli zostanie wyświetlony komunikat Nie znaleziono żadnych zdarzeń spełniających określone kryteria *wyboru.* Zapytanie może być prawidłowe, ale na komputerze lokalnym nie ma pasujących zdarzeń.
> - Jeśli zostanie wyświetlony komunikat *Określone zapytanie jest nieprawidłowe,* składnia zapytania jest nieprawidłowa. 

W poniższej tabeli przedstawiono przykłady filtrowania zdarzeń przy użyciu niestandardowej ścieżki XPath.

| Opis |  XPath |
|:---|:---|
| Zbieraj tylko zdarzenia systemowe o identyfikatorze zdarzenia = 4648 |  `System!*[System[EventID=4648]]`
| Zbieraj tylko zdarzenia systemowe o identyfikatorze zdarzenia = 4648 i nazwie procesu consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Zbierz wszystkie zdarzenia krytyczne, błędy, ostrzeżenia i informacje z dziennika zdarzeń systemu z wyjątkiem zdarzenia o identyfikatorze = 6 (załadowany sterownik) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Zbierz wszystkie zdarzenia zabezpieczeń dotyczące powodzenia i niepowodzeń z wyjątkiem zdarzenia o identyfikatorze 4624 (pomyślne logowanie) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Tworzenie reguły i skojarzenia przy użyciu interfejsu API REST

Wykonaj poniższe kroki, aby utworzyć regułę zbierania danych i skojarzenia przy użyciu interfejsu API REST.

1. Ręcznie utwórz plik DCR przy użyciu formatu JSON pokazanego w [przykładowym formacie DCR.](data-collection-rule-overview.md#sample-data-collection-rule)

2. Utwórz regułę przy użyciu interfejsu [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Utwórz skojarzenie każdej maszyny wirtualnej z regułą zbierania danych przy użyciu interfejsu [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Tworzenie skojarzenia przy użyciu Resource Manager szablonu

Skojarzenie między maszyną wirtualną platformy Azure lub serwerem z Azure Arc można utworzyć przy użyciu Resource Manager szablonu. Zobacz [Resource Manager przykłady szablonów dla reguł zbierania danych](./resource-manager-data-collection-rules.md) w Azure Monitor przykładowych szablonów.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na [temat Azure Monitor agentów.](azure-monitor-agent-overview.md)
- Dowiedz się więcej o [regułach zbierania danych.](data-collection-rule-overview.md)
