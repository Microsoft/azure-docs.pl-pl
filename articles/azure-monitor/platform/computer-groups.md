---
title: Grupy komputerów w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Grupy komputerów w Azure Monitor umożliwiają określanie zakresu zapytań dziennika do określonego zestawu komputerów.  W tym artykule opisano różne metody, których można użyć do tworzenia grup komputerów i ich użycia w zapytaniu dziennika.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 217be627f81406f671118d5290cd5f67f52c01d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86112116"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupy komputerów w zapytaniach dziennika Azure Monitor
Grupy komputerów w Azure Monitor umożliwiają określanie zakresu [zapytań dziennika](../log-query/log-query-overview.md) do określonego zestawu komputerów.  Każda grupa jest wypełniana komputerami przy użyciu kwerendy zdefiniowanej lub przez zaimportowanie grup z różnych źródeł.  Gdy grupa zostanie uwzględniona w zapytaniu dziennika, wyniki są ograniczone do rekordów, które pasują do komputerów w grupie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Grupę komputerów można utworzyć w Azure Monitor przy użyciu dowolnej metody z poniższej tabeli.  Szczegółowe informacje na temat każdej z tych metod znajdują się w poniższych sekcjach. 

| Metoda | Opis |
|:--- |:--- |
| Zapytanie dziennika |Utwórz zapytanie dziennika, które zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Użyj interfejsu API przeszukiwania dzienników, aby programowo utworzyć grupę komputerów na podstawie wyników zapytania dziennika. |
| Active Directory |Automatycznie Skanuj członkostwo w grupie wszystkich komputerów agentów, które są członkami domeny Active Directory i Utwórz grupę w Azure Monitor dla każdej grupy zabezpieczeń. (Tylko komputery z systemem Windows)|
| Configuration Manager | Zaimportuj kolekcje z programu Microsoft Endpoint Configuration Manager i Utwórz grupę w Azure Monitor dla każdej z nich. |
| Program Windows Server Update Services |Automatycznie Skanuj serwery lub klientów programu WSUS pod kątem grup docelowych i Utwórz grupę w Azure Monitor dla każdej z nich. |

### <a name="log-query"></a>Zapytanie dziennika
Grupy komputerów utworzone na podstawie zapytania dziennika zawierają wszystkie komputery zwrócone przez zdefiniowane zapytanie.  To zapytanie jest uruchamiane za każdym razem, gdy zostanie użyta Grupa komputerów, tak aby wszystkie zmiany od momentu utworzenia grupy zostały odzwierciedlone.  

Można użyć dowolnego zapytania dla grupy komputerów, ale musi on zwrócić unikatowy zestaw komputerów za pomocą programu `distinct Computer` .  Poniżej przedstawiono typowe przykładowe zapytanie, którego można użyć jako grupy komputerów.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Aby utworzyć grupę komputerów na podstawie przeszukiwania dzienników w Azure Portal, należy wykonać czynności opisane w poniższej procedurze.

1. Kliknij pozycję **dzienniki** w menu **Azure monitor** w Azure Portal.
1. Utwórz i uruchom zapytanie zwracające komputery, które chcesz umieścić w grupie.
1. Kliknij przycisk **Zapisz** w górnej części ekranu.
1. Zmień opcję **Zapisz jako** na **, a następnie** wybierz pozycję **Zapisz to zapytanie jako grupę komputerów**.
1. Podaj wartości dla każdej właściwości grupy komputerów opisanej w tabeli, a następnie kliknij przycisk **Zapisz**.

W poniższej tabeli opisano właściwości, które definiują grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Nazwa   | Nazwa zapytania do wyświetlania w portalu. |
| Alias funkcji | Unikatowy alias używany do identyfikowania grupy komputerów w zapytaniu. |
| Kategoria       | Kategoria do organizowania zapytań w portalu. |


### <a name="active-directory"></a>Active Directory
W przypadku skonfigurowania Azure Monitor w celu zaimportowania członkostw w grupie Active Directory, analizuje ona członkostwo w grupie wszystkich komputerów przyłączonych do domeny systemu Windows z agentem Log Analytics.  Grupa komputerów jest tworzona w Azure Monitor dla każdej grupy zabezpieczeń w Active Directory, a każdy komputer z systemem Windows jest dodawany do grup komputerów odpowiadających grupom zabezpieczeń, do których są członkami.  To członkostwo jest stale aktualizowane co 4 godziny.  

> [!NOTE]
> Zaimportowane grupy Active Directory zawierają tylko maszyny z systemem Windows.

Konfigurowanie Azure Monitor do importowania Active Directory grup zabezpieczeń z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz pozycję **grupy komputerów**, **Active Directory**, a następnie **zaimportuj członkostwa w grupach Active Directory z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z Active Directory](media/computer-groups/configure-activedirectory.png)

Po zaimportowaniu grup menu wyświetla liczbę komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

### <a name="windows-server-update-service"></a>Windows Server Update Service
W przypadku skonfigurowania Azure Monitor w celu zaimportowania członkostwa w grupach usług WSUS program analizuje członkostwo grupy docelowej na wszystkich komputerach z agentem Log Analytics.  Jeśli używasz funkcji określania wartości docelowej po stronie klienta, każdy komputer, który jest połączony z Azure Monitor i jest częścią dowolnych grup docelowych usług WSUS, ma członkostwo w grupie zaimportowane do Azure Monitor. Jeśli używasz określania wartości docelowej po stronie serwera, Agent Log Analytics powinien być zainstalowany na serwerze programu WSUS, aby informacje o członkostwie w grupie zostały zaimportowane do Azure Monitor.  To członkostwo jest stale aktualizowane co 4 godziny. 

Skonfiguruj Azure Monitor do importowania grup usług WSUS z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz pozycje **grupy komputerów**, **WSUS**, a następnie **Importuj członkostwa w grupach usług WSUS**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usług WSUS](media/computer-groups/configure-wsus.png)

Po zaimportowaniu grup menu wyświetla liczbę komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

### <a name="configuration-manager"></a>Configuration Manager
Podczas konfigurowania Azure Monitor do importowania członkostw w kolekcji Configuration Manager tworzy grupę komputerów dla każdej kolekcji.  Informacje o członkostwie w kolekcji są pobierane co 3 godziny, aby zachować bieżące grupy komputerów. 

Przed zaimportowaniem kolekcji Configuration Manager należy [połączyć Configuration Manager z Azure monitor](collect-sccm.md).  

![Grupy komputerów z programu SCCM](media/computer-groups/configure-sccm.png)

Po zaimportowaniu kolekcji menu wyświetla liczbę komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Można wyświetlić grupy komputerów, które zostały utworzone na podstawie zapytania dziennika lub interfejsu API wyszukiwania dzienników z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz **grupy komputerów** , a następnie **zapisane grupy**.  

Kliknij **znak x** w kolumnie **Usuń** , aby usunąć grupę komputerów.  Kliknij ikonę **Wyświetl członków** w grupie, aby uruchomić przeszukiwanie dzienników grupy zwracające jego elementy członkowskie.  Nie można zmodyfikować grupy komputerów, ale należy ją usunąć, a następnie utworzyć ponownie, używając zmodyfikowanych ustawień.

![Zapisane grupy komputerów](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Używanie grupy komputerów w zapytaniu dziennika
Należy użyć grupy komputerów utworzonej na podstawie zapytania dziennika w zapytaniu, traktując swój alias jako funkcję, zazwyczaj z następującą składnią:

```kusto
Table | where Computer in (ComputerGroup)`
```

Można na przykład użyć poniższego, aby zwrócić UpdateSummary rekordy tylko dla komputerów w grupie komputerów o nazwie Moja komputery.

```kusto
UpdateSummary | where Computer in (mycomputergroup)`
```

Zaimportowane grupy komputerów i ich dołączone komputery są przechowywane w tabeli **komputerów** .  Na przykład następujące zapytanie zwróci listę komputerów w grupie Komputery domeny z Active Directory. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

Następujące zapytanie zwróci rekordy UpdateSummary tylko dla komputerów znajdujących się na komputerach w domenie.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Rekordy grup komputerów
Rekord zostanie utworzony w obszarze roboczym Log Analytics dla każdego członkostwa w grupie komputerów utworzonego na podstawie Active Directory lub usług WSUS.  Te rekordy mają typ Grupa **komputerów** i mają właściwości w poniższej tabeli.  Rekordy nie są tworzone dla grup komputerów opartych na zapytaniach dziennika.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nazwa komputera członkowskiego. |
| `Group` |Nazwa grupy. |
| `GroupFullName` |Pełna ścieżka do grupy, łącznie z nazwą źródłową i źródłową. |
| `GroupSource` |Źródło, z którego zebrano grupę. <br><br>ActiveDirectory<br>Program WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nazwa źródła, z którego pobrano grupę.  W przypadku Active Directory jest to nazwa domeny. |
| `ManagementGroupName` |Nazwa grupy zarządzania agentów SCOM.  W przypadku innych agentów jest to AOI-\<workspace ID\> |
| `TimeGenerated` |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  

