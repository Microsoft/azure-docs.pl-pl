---
title: Automatyczne skalowanie zestawów skalowania maszyn wirtualnych w Azure Portal
description: Jak utworzyć reguły automatycznego skalowania dla zestawów skalowania maszyn wirtualnych w Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: ac42fe3265163a5a967524fe11063803c9ca91d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080594"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatyczne skalowanie zestawu skalowania maszyn wirtualnych w Azure Portal
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia.

W tym artykule opisano sposób tworzenia reguł automatycznego skalowania w Azure Portal, które monitorują wydajność wystąpień maszyn wirtualnych w zestawie skalowania. Te reguły automatycznego skalowania zwiększają lub zmniejszają liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Te kroki można również wykonać za pomocą [Azure PowerShell](tutorial-autoscale-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby można było tworzyć reguły automatycznego skalowania, potrzebny jest istniejący zestaw skalowania maszyn wirtualnych. Zestaw skalowania można utworzyć za pomocą [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Utwórz regułę automatycznego skalowania w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

1. Otwórz Azure Portal i wybierz pozycję **grupy zasobów** z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów zawierającą zestaw skalowania, a następnie wybierz zestaw skalowania z listy zasobów.
3. Wybierz pozycję **skalowanie** z menu po lewej stronie okna zestawu skalowania. Wybierz przycisk, aby **włączyć funkcję automatycznego skalowania**:

    ![Włącz automatyczne skalowanie w Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę ustawień, na przykład *Automatyczne skalowanie*, a następnie wybierz opcję, aby **dodać regułę**.

5. Utwórzmy regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w zestawie skalowania, gdy średnie obciążenie procesora CPU jest większe niż 70% w okresie 10 minut. Gdy reguła jest wyzwalana, liczba wystąpień maszyn wirtualnych jest zwiększana o 20%. W przypadku zestawów skalowania z małą liczbą wystąpień maszyn wirtualnych można ustawić **operację** , aby *zwiększyć liczbę,* a następnie określić wartość *1* lub *2* dla *liczby wystąpień*. W przypadku zestawów skalowania z dużą liczbą wystąpień maszyn wirtualnych zwiększenie liczby wystąpień maszyn wirtualnych 10% lub 20% może być bardziej odpowiednie.

    Określ następujące ustawienia reguły:
    
    | Parametr              | Objaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarna czasu* | Definiuje sposób agregowania zebranych metryk w poszczególnych ziarnach w celu analizy.                             | Średnia        |
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż   |
    | *Próg*            | Wartość procentowa powodująca wyzwalanie akcji przez regułę skalowania automatycznego.                                                 | 70             |
    | *Czas trwania*             | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową. Nie obejmuje okresu chłodzenia.                                   | 10 minut     |
    | *Operacja*            | Określa, czy zestaw skalowania ma być skalowany w górę, czy w dół, gdy reguła zostanie zastosowana, i według tego przyrostu.                        | Zwiększ wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 20             |
    | *Czas schładzania (minuty)*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 min      |

    W poniższych przykładach przedstawiono regułę utworzoną w Azure Portal, która pasuje do następujących ustawień:

    ![Tworzenie reguły automatycznego skalowania w celu zwiększenia liczby wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz pozycję **Dodaj** .


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły automatycznego skalowania w poziomie
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

1. Wybierz, aby ponownie **dodać regułę** .
2. Utwórz regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w zestawie skalowania, gdy średnie obciążenie procesora CPU spadnie poniżej 30% w okresie 10 minut. Gdy reguła jest wyzwalana, liczba wystąpień maszyn wirtualnych zostanie zmniejszona o 20%.

    Użyj tego samego podejścia jak w przypadku poprzedniej reguły. Dostosuj następujące ustawienia reguły:
    
    | Parametr              | Objaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                      | Mniejsze niż   |
    | *Próg*            | Wartość procentowa powodująca wyzwalanie akcji przez regułę skalowania automatycznego.                                                 | 30             |
    | *Operacja*            | Określa, czy zestaw skalowania ma być skalowany w górę, czy w dół, gdy reguła zostanie zastosowana, a następnie przez zwiększenie                         | Zmniejsz procent według |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                             | 20             |

3. Aby utworzyć regułę, wybierz pozycję **Dodaj** .


## <a name="define-autoscale-instance-limits"></a>Definiuj limity wystąpień automatycznego skalowania
Profil skalowania automatycznego musi definiować minimalną, maksymalną i domyślną liczbę wystąpień maszyn wirtualnych. Po zastosowaniu reguł skalowania automatycznego te limity nie są skalowane w poziomie poza maksymalną liczbą wystąpień lub skalowaniem poza minimalnymi wystąpieniami.

1. Ustaw następujące limity wystąpień:

    | Minimum | Maksimum | Domyślne|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Aby zastosować reguły automatycznego skalowania i limity wystąpień, wybierz pozycję **Zapisz**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitoruj liczbę wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wybierz pozycję **wystąpienia** z menu po lewej stronie okna zestawu skalowania. Stan wskazuje, czy wystąpienie maszyny wirtualnej jest *tworzone* , ponieważ zestaw skalowania jest automatycznie skalowany, lub jest *usuwany* , ponieważ skala jest automatycznie skalowana w poziomie.

![Wyświetlanie listy wystąpień maszyn wirtualnych zestawu skalowania](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatyczne skalowanie na podstawie harmonogramu
Poprzednie przykłady automatycznie przeskalowane zestaw skalowania do lub z podstawowymi metrykami hosta, takimi jak użycie procesora CPU. Można również tworzyć reguły automatycznego skalowania na podstawie harmonogramów. Te reguły oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyn wirtualnych przed przewidywanym wzrostem zapotrzebowania na aplikacje, takich jak podstawowe godziny pracy, a następnie automatyczne skalowanie liczby wystąpień w czasie, w którym przewidywane jest mniejsze zapotrzebowanie, takie jak weekend.

1. Wybierz pozycję **skalowanie** z menu po lewej stronie okna zestawu skalowania. Aby usunąć istniejące reguły automatycznego skalowania utworzone w poprzednich przykładach, wybierz ikonę kosza.

    ![Usuń istniejące reguły automatycznego skalowania](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Wybierz, aby **dodać warunek skali**. Wybierz ikonę ołówka obok pozycji Nazwa reguły i podaj nazwę, taką jak *skalowanie w poziomie poszczególnych dni roboczych*.

    ![Zmień nazwę domyślnej reguły automatycznego skalowania](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wybierz przycisk radiowy do **skalowania do określonej liczby wystąpień**.
4. Aby skalować w górę liczbę wystąpień, wprowadź *10* jako liczbę wystąpień.
5. Wybierz opcję **Powtarzaj określone dni** dla typu **harmonogramu** .
6. Wybierz wszystkie dni robocze, od poniedziałku do piątku.
7. Wybierz odpowiednią strefę czasową, a następnie określ **godzinę rozpoczęcia** *09:00*.
8. Wybierz, aby ponownie **dodać warunek skalowania** . Powtórz ten proces, aby utworzyć harmonogram o nazwie *skalowanie w czasie wieczorem* , który skaluje się do *3* wystąpień, powtarza każdy dzień tygodnia i rozpocznie się o *18:00*.
9. Aby zastosować reguły automatycznego skalowania oparte na harmonogramie, wybierz pozycję **Zapisz**.

    ![Tworzenie reguł skalowania automatycznego skalowania według harmonogramu](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Aby zobaczyć, jak są stosowane reguły automatycznego skalowania, wybierz pozycję **Uruchom historię** w górnej części okna **skalowanie** . Lista wykresów i zdarzeń pokazuje, kiedy wyzwalacze automatycznego skalowania i liczba wystąpień maszyn wirtualnych w zestawie skalowania rośnie lub zmniejsza.


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób używania reguł skalowania automatycznego w celu skalowania w poziomie i zwiększania lub zmniejszania *liczby* wystąpień maszyn wirtualnych w zestawie skalowania. Możesz również skalować w pionie, aby zwiększyć lub zmniejszyć *rozmiar*wystąpienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [pionowy Skalowanie automatyczne przy użyciu zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpieniami maszyn wirtualnych, zobacz [Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Aby dowiedzieć się, jak generować alerty podczas wyzwalacza reguł skalowania automatycznego, zobacz [Używanie akcji automatycznego skalowania do wysyłania powiadomień o alertach poczty e-mail i elementów webhook w Azure monitor](../azure-monitor/platform/autoscale-webhook-email.md). [Dzienników inspekcji można także używać do wysyłania powiadomień o alertach poczty e-mail i elementów webhook w programie Azure monitor](../azure-monitor/platform/alerts-log-webhook.md).
