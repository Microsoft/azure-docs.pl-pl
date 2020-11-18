---
title: Azure Monitor dla maszyn wirtualnych alertów dotyczących kondycji gościa (wersja zapoznawcza)
description: Opisuje alerty utworzone przez Azure Monitor dla maszyn wirtualnych kondycję gościa, w tym sposób włączania i konfigurowania powiadomień.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 9bbb0be321e78317c52586454de3d49cb2f878c1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686890"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>Azure Monitor dla maszyn wirtualnych alertów dotyczących kondycji gościa (wersja zapoznawcza)
Azure Monitor dla maszyn wirtualnych kondycja gościa umożliwia wyświetlanie kondycji maszyny wirtualnej zdefiniowanej przez zestaw pomiarów wydajności, które są próbkowane w regularnych odstępach czasu. Alert można utworzyć, gdy maszyna wirtualna lub monitor zmieni stan w złej kondycji. Można wyświetlać te alerty i zarządzać nimi z [tymi utworzonymi przez reguły alertów w Azure monitor](../platform/alerts-overview.md) i zdecydować się na ich aktywne powiadamianie o utworzeniu nowego alertu.

## <a name="configure-alerts"></a>Konfigurowanie alertów
Nie można utworzyć jawnej reguły alertu dla Azure Monitor dla maszyn wirtualnych kondycji gościa, gdy ta funkcja jest w wersji zapoznawczej. Domyślnie alerty zostaną utworzone dla każdej maszyny wirtualnej, ale nie dla każdego monitora.  Oznacza to, że jeśli monitor zmieni stan, który nie ma wpływu na bieżący stan maszyny wirtualnej, nie zostanie utworzony żaden alert, ponieważ stan maszyny wirtualnej nie zmienił się. 

Można wyłączyć alerty dla określonej maszyny wirtualnej lub dla określonego monitora na maszynie wirtualnej z poziomu ustawienia **stanu alertu** w konfiguracji maszyny wirtualnej w Azure Portal. Aby uzyskać szczegółowe informacje na temat konfigurowania monitorów w Azure Portal, zobacz [konfigurowanie monitorowania w Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-configure.md) . Zobacz [konfigurowanie monitorowania w Azure monitor dla maszyn wirtualnych kondycji gościa przy użyciu reguł zbierania danych (wersja zapoznawcza)](vminsights-health-configure-dcr.md) , aby uzyskać szczegółowe informacje na temat konfigurowania monitorów w zestawie maszyn wirtualnych.

## <a name="alert-severity"></a>Ważność alertu
Ważność alertu utworzonego przez kondycję gościa jest bezpośrednio mapowana na ważność maszyny wirtualnej lub monitora wyzwalającego alert.

| Stan monitora | Ważność alertu |
|:---|:---|
| Krytyczne | Sev1 |
| Ostrzeżenie  | Sev2 |
| Dobra kondycja  | Sev4 |

## <a name="alert-lifecycle"></a>Cykl życia alertu
Dla każdej maszyny wirtualnej zostanie utworzony [alert platformy Azure](../platform/alerts-overview.md) w dowolnym momencie, gdy zmieni się on w stan **ostrzegawczy** lub **krytyczny** . Wyświetl alert z poziomu **alertów** w menu **Azure monitor** lub w menu maszyny wirtualnej w Azure Portal.

Jeśli alert jest już w stanie **uruchomienia** , gdy stan maszyny wirtualnej ulegnie zmianie, drugi alert nie zostanie utworzony, ale ważność tego samego alertu zostanie zmieniona w celu dopasowania go do stanu maszyny wirtualnej. Na przykład jeśli maszyna wirtualna zmieni stan na **krytyczny** , jeśli alert **ostrzegawczy** był już w stanie **uruchomienia** , ważność tego alertu zostanie zmieniona na **Sev1**. Jeśli maszyna wirtualna zmieni stan na **ostrzegawczy** , gdy alert **Sev1** był już w stanie **uruchomienia** , ważność tego alertu zostanie zmieniona na **Sev2**. Jeśli maszyna wirtualna zostanie przeniesiona z powrotem do stanu **dobrej kondycji** , alert zostanie rozwiązany z ważnością zmieniono na **Sev4**.

## <a name="viewing-alerts"></a>Wyświetlanie alertów
Wyświetlanie alertów utworzonych przez Azure Monitor dla maszyn wirtualnych kondycję Gościa z innymi [alertami w Azure Portal](../platform/alerts-overview.md#alerts-experience). Możesz wybrać **alerty** z menu **Azure monitor** , aby wyświetlić alerty dla wszystkich monitorowanych zasobów, lub wybrać **alerty** z menu maszyny wirtualnej, aby wyświetlić alerty dotyczące tylko tej maszyny wirtualnej.

## <a name="alert-properties"></a>Właściwości alertu

### <a name="properties-in-the-azure-portal"></a>Właściwości w Azure Portal
Właściwości alertu wyświetlanego w Azure Portal są opisane w poniższej tabeli.

| Właściwość | Opis |
|:---|:---|
| Stan monitora przed utworzeniem alertu | Stan monitora lub maszyny wirtualnej przed pierwszym uruchomieniem tego alertu. |
| Stan monitora po utworzeniu alertu | Stan monitora lub maszyny wirtualnej, gdy alert został wyzwolony po raz pierwszy. Jest to stan, który spowodował uruchomienie alertu. |
| Aby dowiedzieć się więcej o przejściu stanu po utworzeniu alertu | Link do strony kondycji maszyny wirtualnej, na której można zobaczyć dokładne przejście stanu. To przejście stanu reprezentuje wystąpienie, gdy monitor po raz pierwszy poszedł ze stanu **dobrej** kondycji w stanie nieprawidłowym. |

Na przykład monitor przechodzi od **zdrowego** do **krytycznego** w czasie, a nowy Alert jest uruchamiany z **Sev1**. Następnie od **krytycznego** do **ostrzegawczego** w czasie T1 i ważności alertu zostanie zaktualizowany do **Sev2**. Jest on w **dobrej kondycji** , a alert zostanie rozwiązany.

Właściwości alertu będą mieć te wartości podczas tej całej sekwencji.

- Stan monitora przed utworzeniem alertu: dobra kondycja
- Stan monitora po utworzeniu alertu: krytyczny
- Aby dowiedzieć się więcej o przejściu stanu po utworzeniu alertu: łącze nawigacji do przejścia stanu zakończyło się w czasie T0.


### <a name="properties-in-notifications"></a>Właściwości w powiadomieniach
Właściwości alertu zawartego w powiadomieniach są opisane w poniższej tabeli.

| Właściwość | Opis |
|:---|:---|
| Poprzedni stan monitora | Stan monitora lub maszyny wirtualnej przed zmianą stanu. Jeśli aktualizacja ważności alertu wyzwala to powiadomienie, ta właściwość reprezentuje stan, który był tuż przed aktualizacją ważności. |
| Bieżący stan monitora | Stan monitora lub maszyny wirtualnej po zmianie stanu. Jeśli aktualizacja ważności alertu wyzwala to powiadomienie, ta właściwość reprezentuje nowy stan. |
| Aby dowiedzieć się więcej na temat tego przejścia stanu | Link do strony kondycji maszyny wirtualnej, na której można zobaczyć dokładne przejście stanu. To przejście stanu reprezentuje wystąpienie, gdy monitor zmienił stan, który wyzwolił to powiadomienie. |

Korzystając z powyższego przykładu, powiadomienia będą mieć w każdej chwili następujące właściwości.

Powiadomienie odebrane w czasie T0
- Poprzedni stan monitora: dobra kondycja
- Bieżący stan monitora: krytyczny
- Aby dowiedzieć się więcej na temat tego przejścia stanu: łącze nawigacji do przejścia stanu zaszło w czasie T0.

Powiadomienie odebrane w czasie T1
- Poprzedni stan monitora: krytyczny
- Bieżący stan monitora: Ostrzeżenie
- Aby dowiedzieć się więcej o tym przeniesieniu stanu: w czasie T1 wystąpił link nawigacji do przejścia stanu.

Powiadomienie odebrane w czasie T2
- Poprzedni stan monitora: Ostrzeżenie
- Bieżący stan monitora: dobra kondycja
- Aby dowiedzieć się więcej o tym przeniesieniu stanu: łącze nawigacji do przejścia stanu zaszło w czasie T2.

## <a name="configure-notifications"></a>Konfigurowanie powiadomień
Aby otrzymywać powiadomienia o alertach wyzwalanych przez kondycję gościa, Utwórz [grupę akcji](../platform/action-groups.md) , aby zdefiniować różne akcje do wykonania, takie jak wysyłanie wiadomości SMS lub uruchamianie aplikacji logiki. Następnie Utwórz [regułę akcji](../platform/alerts-action-rules.md) , która określa zakres monitorów i maszyn wirtualnych i używa tej grupy akcji.

W menu **Monitoruj** w Azure Portal wybierz pozycję **alerty**.  Wybierz kolejno pozycje **Zarządzaj akcje** i **reguły akcji (wersja zapoznawcza)**. 

![Nowa reguła akcji](media/vminsights-health-alerts/action-rule-new.png)

Kliknij pozycję **Nowa reguła akcji** , aby utworzyć nową regułę. Kliknij pozycję **Wybierz** obok pozycji zakres i wybierz subskrypcję, grupę zasobów lub co najmniej jedną konkretną maszynę wirtualną. Powiadomienie zostanie wyzwolone tylko dla maszyn wirtualnych należących do zakresu.

![Zakres reguły akcji](media/vminsights-health-alerts/action-rule-scope.png)

Kliknij przycisk **Dodaj** obok pozycji **Filtr**. Utwórz filtr, w którym **Usługa monitora jest równa usłudze VM Insights — kondycja**. Dodaj inne filtry, aby określić określone alerty, które powinny wyzwalać powiadomienie. Na przykład można użyć **ważności** , aby dopasować alerty ze wszystkich monitorów zgodnych z określoną ważnością.

![Filtr reguły akcji](media/vminsights-health-alerts/action-rule-filter.png)

W obszarze **Definiuj w tym zakresie** wybierz pozycję **Grupa akcji** , a następnie wybierz grupę akcji do skojarzenia z monitorem. Nadaj regule nazwę i wybierz grupę zasobów, w której ma zostać zapisana. Kliknij przycisk **Utwórz** , aby utworzyć regułę.

![Reguła akcji](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Następne kroki

- [Włączaj kondycję gościa w Azure Monitor dla maszyn wirtualnych i dołączaj agentów.](vminsights-health-enable.md)
- [Skonfiguruj monitory przy użyciu Azure Portal.](vminsights-health-configure.md)
- [Skonfiguruj monitory przy użyciu reguł zbierania danych.](vminsights-health-configure-dcr.md)