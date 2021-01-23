---
title: Alerty dziennika aktywności w Azure Monitor
description: Otrzymywanie powiadomień za pośrednictwem wiadomości SMS, elementu webhook, wiadomości SMS, poczty e-mail i innych informacji w przypadku wystąpienia określonych zdarzeń w dzienniku aktywności.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 8a30c0a0527f98cc00f7888299c09f1f26c3dd09
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735218"
---
# <a name="alerts-on-activity-log"></a>Alerty dotyczące dziennika aktywności

## <a name="overview"></a>Omówienie

Alerty dziennika aktywności są alertami, które uaktywniają się, gdy wystąpi nowe [zdarzenie dziennika aktywności](activity-log-schema.md) zgodne z warunkami określonymi w alercie. Reguła alertu będzie uruchamiana na podstawie kolejności i ilości zdarzeń zarejestrowanych w [dzienniku aktywności platformy Azure](platform-logs-overview.md). Reguły alertów dziennika aktywności to zasoby platformy Azure, dzięki czemu można je tworzyć przy użyciu szablonu Azure Resource Manager. Można je także tworzyć, aktualizować lub usuwać w Azure Portal. W tym artykule przedstawiono pojęcia dotyczące alertów dziennika aktywności. Aby uzyskać więcej informacji na temat tworzenia i używania reguł alertów dziennika aktywności, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](alerts-activity-log.md).

> [!NOTE]
> * **Nie można** tworzyć alertów dla zdarzeń w kategorii alertów dziennika aktywności.
> * Alerty dziennika aktywności z kategorią zabezpieczeń można definiować również w [nowym uaktualnionym przepływie](../../security-center/continuous-export.md?tabs=azure-portal) do [usługi ServiceNow](../../security-center/export-to-siem.md)

Zazwyczaj tworzysz alerty dziennika aktywności, aby otrzymywać powiadomienia, gdy:

* Określone operacje odbywają się na zasobach w ramach subskrypcji platformy Azure, często w zakresie określonych grup zasobów lub zasobów. Na przykład możesz chcieć otrzymywać powiadomienia, gdy dowolna maszyna wirtualna w myProductionResourceGroup zostanie usunięta. Możesz również otrzymywać powiadomienia o tym, czy nowe role są przypisane do użytkownika w ramach subskrypcji.
* Wystąpił zdarzenie kondycji usługi. Zdarzenia dotyczące kondycji usługi obejmują powiadomienie o zdarzeniach dotyczących zdarzeń konserwacji, które dotyczą zasobów w ramach subskrypcji.

Prostą analogową dla zrozumienie warunków, w których można tworzyć reguły alertów w dzienniku aktywności, jest Eksplorowanie lub filtrowanie zdarzeń za pośrednictwem [dziennika aktywności w Azure Portal](./activity-log.md#view-the-activity-log). W Dzienniku działania Azure Monitor jeden może odfiltrować lub znaleźć wymagane zdarzenie, a następnie utworzyć alert przy użyciu przycisku **Dodaj alert dziennika aktywności** .

W obu przypadkach alert dziennika aktywności jest monitorowany tylko dla zdarzeń w subskrypcji, w której jest tworzony alert.

Alert dziennika aktywności można skonfigurować na podstawie dowolnej właściwości najwyższego poziomu w obiekcie JSON dla zdarzenia dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Kategorie w dzienniku aktywności](./activity-log.md#view-the-activity-log). Aby dowiedzieć się więcej na temat zdarzeń usługi Service Health, zobacz [otrzymywanie alertów dziennika aktywności dla powiadomień dotyczących usług](../../service-health/alerts-activity-log-service-notifications-portal.md). 

Alerty dziennika aktywności mają kilka typowych opcji:

- **Kategoria**: administracyjne, Service Health, automatyczne skalowanie, zabezpieczenia, zasady i rekomendacje. 
- **Zakres**: pojedynczy zasób lub zestaw zasobów, dla których zdefiniowano alert w dzienniku aktywności. Zakres alertu dziennika aktywności można zdefiniować na różnych poziomach:
    - Poziom zasobu: na przykład dla określonej maszyny wirtualnej
    - Poziom grupy zasobów: na przykład wszystkie maszyny wirtualne w określonej grupie zasobów
    - Poziom subskrypcji: na przykład wszystkie maszyny wirtualne w subskrypcji (lub) wszystkie zasoby w ramach subskrypcji
- **Grupa zasobów**: Domyślnie reguła alertu jest zapisywana w tej samej grupie zasobów co element docelowy zdefiniowany w zakresie. Użytkownik może również zdefiniować grupę zasobów, w której ma być przechowywana reguła alertu.
- **Typ zasobu**: Menedżer zasobów zdefiniowanej przestrzeni nazw dla elementu docelowego alertu.
- **Nazwa operacji**: nazwa [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md) używana na potrzeby kontroli dostępu opartej na rolach na platformie Azure. Operacji nie zarejestrowano w Azure Resource Manager nie można użyć w regule alertu dziennika aktywności.
- **Poziom**: poziom ważności zdarzenia (informacyjny, ostrzegawczy, błąd lub krytyczny).
- **Stan**: stan zdarzenia, zazwyczaj rozpoczęte, zakończone niepowodzeniem lub zakończone powodzeniem.
- **Zdarzenie zainicjowane przez**: nazywane również "obiektem wywołującym". Adres e-mail lub identyfikator Azure Active Directory użytkownika, który wykonał operację.

> [!NOTE]
> W subskrypcji do 100 reguły alertów można utworzyć dla działania zakresu w jednym z nich: pojedynczy zasób, wszystkie zasoby w grupie zasobów (lub) cały poziom subskrypcji.

Po aktywowaniu alertu dziennika aktywności program używa grupy akcji do generowania akcji lub powiadomień. Grupa akcji to zbiór odbiorników powiadomień wielokrotnego użytku, takich jak adresy e-mail, adresy URL elementów webhook lub numery telefonów SMS. Odbiorcy mogą odwoływać się z wielu alertów w celu scentralizowania i grupowania kanałów powiadomień. Podczas definiowania alertu dziennika aktywności dostępne są dwie opcje. Oto co możesz zrobić:

* Użyj istniejącej grupy akcji w alercie dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](action-groups.md).


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem alertów](alerts-overview.md).
- Dowiedz się więcej o [tworzeniu i modyfikowaniu alertów dziennika aktywności](alerts-activity-log.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](activity-log-alerts-webhook.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../../service-health/service-notifications.md).