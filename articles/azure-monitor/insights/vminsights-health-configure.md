---
title: Konfigurowanie monitorowania w Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)
description: Opisuje sposób modyfikowania domyślnego monitorowania Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza) przy użyciu Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686986"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Konfigurowanie monitorowania w Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)
Azure Monitor dla maszyn wirtualnych kondycja gościa umożliwia wyświetlanie kondycji maszyny wirtualnej zdefiniowanej przez zestaw pomiarów wydajności, które są próbkowane w regularnych odstępach czasu. W tym artykule opisano sposób modyfikowania domyślnego monitorowania przy użyciu Azure Portal. Opisano w nim również podstawowe pojęcia dotyczące monitorów, które są wymagane do [skonfigurowania monitorowania przy użyciu reguły zbierania danych](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Otwórz konfigurację monitora
Otwórz pozycję Monitoruj konfigurację bin Azure Portal, wybierając monitor, a następnie kartę **Konfiguracja** .

[![Konfiguracja szczegółów monitora](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Włączanie lub wyłączanie monitorów
Oba monitory jednostkowe i monitory zagregowane mają ustawienie **stanu monitora kondycji** , które umożliwia włączenie lub wyłączenie monitora. Gdy monitor jest włączony, jego kondycja jest wyświetlana i służy do ustawiania kondycji maszyny wirtualnej. Gdy monitor jest wyłączony, jego kondycja nie jest obliczana i nie jest używana do ustawiania kondycji maszyny wirtualnej.

| Ustawienie | Opis |
|:---|:---|
| Enabled (Włączony) | Monitor jest włączony niezależnie od ustawienia jego elementu nadrzędnego. |
| Disabled | Monitor jest wyłączony niezależnie od ustawienia jego elementu nadrzędnego. |
| Taki sam jak nadrzędny | Monitor zostanie włączony lub wyłączony w zależności od ustawienia jego elementu nadrzędnego. |

Gdy monitor zostanie wyłączony, wszystkie kryteria są wyświetlane jako niedostępne, jak pokazano w poniższym przykładzie.

![Wyłączony monitor](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>Włącz lub Wyłącz maszynę wirtualną
Można wyłączyć monitorowanie dla maszyny wirtualnej, aby tymczasowo zatrzymać wszystkie monitory. Można wyłączyć monitorowanie dla maszyny wirtualnej na przykład podczas przeprowadzania konserwacji.

| Ustawienie | Opis |
|:---|:---|
| Enabled (Włączony)  | Zostanie wyświetlony stan kondycji komputera. |
| Disabled | Stan kondycji komputera jest pokazywany jako **wyłączony**. Alerty nie są tworzone. |

## <a name="health-state-logic"></a>Logika stanu kondycji
Logika stanu kondycji dla monitora jednostkowego definiuje kryteria ustawiania stanu kondycji. Można określić liczbę stanów kondycji monitora i wartość progową dla poszczególnych stanów kondycji.

![Przykładowe kryteria kondycji](media/vminsights-health-configure/sample-health-criteria.png)

Monitory agregujące nie określają żadnej logiki stanu kondycji. Ich stan kondycji jest ustawiany przez monitory jednostkowe zgodnie z ich zestawieniem kondycji.

Każdy monitor jednostkowy ma dwa lub trzy stany kondycji. Każdy z nich będzie zawsze mieć prawidłowy stan i opcjonalnie stan ostrzegawczy, stan krytyczny lub oba te elementy. Ostrzeżenia i Stany krytyczne każdy wymagają unikatowych kryteriów, które definiują, kiedy Monitor jest ustawiony na ten stan. Stan prawidłowy nie ma kryteriów, ponieważ ten stan jest ustawiony, gdy nie są spełnione kryteria dla innych stanów.

W poniższym przykładzie użycie procesora CPU jest ustawione na następujące stany kondycji:

- Krytyczne, jeśli jego wartość przekracza 90%.
- Ostrzeżenie, jeśli jego wartość przekracza lub równa 80%.
- Dobra kondycja, jeśli jego poniżej 80%. Jest to implikowane, ponieważ jest to sytuacja, w której nie obowiązują żadne inne warunki.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie monitorów na dużą skalę przy użyciu reguł zbierania danych.](vminsights-health-configure-dcr.md)