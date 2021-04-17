---
title: Automatyczne skalowanie zestawów skalowania maszyn wirtualnych w Azure Portal
description: Jak utworzyć reguły skalowania automatycznego dla zestawów skalowania maszyn wirtualnych w Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537526"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatyczne skalowanie zestawu skalowania maszyn wirtualnych w Azure Portal
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia.

W tym artykule pokazano, jak utworzyć reguły skalowania automatycznego w Azure Portal, które monitorują wydajność wystąpień maszyn wirtualnych w zestawie skalowania. Te reguły skalowania automatycznego zwiększają lub zmniejszają liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Te kroki można również wykonać za pomocą polecenia [Azure PowerShell](tutorial-autoscale-powershell.md) interfejsu wiersza [polecenia platformy Azure.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Wymagania wstępne
Do utworzenia reguł skalowania automatycznego potrzebny jest istniejący zestaw skalowania maszyn wirtualnych. Zestaw skalowania można utworzyć przy użyciu interfejsu [wiersza Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub interfejsu wiersza polecenia [platformy Azure.](quick-create-cli.md)


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły w celu automatycznego skalowania w zewnątrz
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

1. Otwórz Azure Portal i wybierz **pozycję Grupy** zasobów z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów zawierającą zestaw skalowania, a następnie wybierz zestaw skalowania z listy zasobów.
3. Wybierz **pozycję Skalowanie** z menu po lewej stronie okna zestawu skalowania. Wybierz przycisk Włącz **autoskalowanie:**

    ![Włączanie autoskalowania w Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę ustawień, na przykład *autoskalowanie,* a następnie wybierz opcję **Dodaj regułę**.

5. Utwórzmy regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w zestawie skalowania, gdy średnie obciążenie procesora CPU jest większe niż 70% w okresie 10 minut. Po wyzwoleniu reguły liczba wystąpień maszyn wirtualnych zwiększa się o 20%. W zestawach skalowania z małą liczbą wystąpień  maszyn wirtualnych można ustawić dla operacji wartość *Zwiększ liczbę o,* a następnie określić *wartość 1* lub *2* dla *wartości Liczba wystąpień*. W zestawach skalowania z dużą liczbą wystąpień maszyn wirtualnych bardziej odpowiednie może być zwiększenie liczby wystąpień maszyn wirtualnych o 10% lub 20%.

    Określ następujące ustawienia reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarna czasu* | Definiuje sposób agregowania zebranych metryk w każdym ziarnie czasu do analizy.                             | Średnia        |
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż   |
    | *Próg*            | Wartość procentowa, która powoduje wyzwolenie akcji przez regułę autoskalowania.                                                 | 70             |
    | *Czas trwania*             | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową. Nie obejmuje okresu ochłodzenia.                                   | 10 minut     |
    | *Operacja*            | Określa, czy zestaw skalowania ma być skalowany w górę, czy w dół, gdy reguła ma być stosowana, i o jaki przyrost.                        | Zwiększ procent o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 20             |
    | *Czas schładzania (minuty)*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 min      |

    W poniższych przykładach przedstawiono regułę utworzoną w Azure Portal, która jest taka jak te ustawienia:

    ![Tworzenie reguły autoskalowania w celu zwiększenia liczby wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz pozycję **Dodaj**


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły do automatycznego skalowania w skali
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

1. Wybierz ponownie **pozycję Dodaj regułę.**
2. Utwórz regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w zestawie skalowania, gdy średnie obciążenie procesora CPU spadnie poniżej 30% w okresie 10 minut. Po wyzwoleniu reguły liczba wystąpień maszyn wirtualnych zmniejsza się o 20%.

    Użyj tego samego podejścia, co w przypadku poprzedniej reguły. Dostosuj następujące ustawienia reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                      | Mniejsze niż   |
    | *Próg*            | Wartość procentowa, która powoduje wyzwolenie akcji przez regułę autoskalowania.                                                 | 30             |
    | *Operacja*            | Określa, czy zestaw skalowania ma być skalowany w górę, czy w dół, gdy reguła jest stosowana, i o jaki przyrost                         | Zmniejsz wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                             | 20             |

3. Aby utworzyć regułę, wybierz pozycję **Dodaj**


## <a name="define-autoscale-instance-limits"></a>Definiowanie limitów wystąpień autoskalowania
Profil skalowania automatycznego musi definiować minimalną, maksymalną i domyślną liczbę wystąpień maszyn wirtualnych. Zastosowanie reguł autoskalowania zapewnia, że te limity wystąpień nie przekraczają maksymalnej liczby wystąpień ani nie przekraczają minimalnej liczby wystąpień.

1. Ustaw następujące limity wystąpień:

    | Minimum | Maksimum | Domyślne|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Aby zastosować reguły skalowania automatycznego i limity wystąpień, wybierz pozycję **Zapisz**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorowanie liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wybierz pozycję **Wystąpienia** z menu po lewej stronie okna zestawu skalowania. Stan wskazuje, czy wystąpienie  maszyny wirtualnej ma wartość Tworzenie,  ponieważ zestaw skalowania jest automatycznie skalowany w zewnątrz, czy usuwanie w przypadku automatycznego skalowania w skali.

![Wyświetlanie listy wystąpień maszyn wirtualnych zestawu skalowania](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoskalowanie na podstawie harmonogramu
W poprzednich przykładach zestaw skalowania był automatycznie skalowany w poziomie lub w poziomie przy użyciu podstawowych metryk hosta, takich jak użycie procesora CPU. Można również tworzyć reguły autoskalowania na podstawie harmonogramów. Te reguły oparte na harmonogramie umożliwiają automatyczne skalowanie w poziomie liczby wystąpień maszyn wirtualnych przed przewidywanym wzrostem zapotrzebowania na aplikacje, takiego jak podstawowe godziny pracy, a następnie automatyczne skalowanie w poziomie liczby wystąpień w czasie, które przewiduje się mniejsze zapotrzebowanie, na przykład w weekendy.

1. Wybierz **pozycję Skalowanie** z menu po lewej stronie okna zestawu skalowania. Aby usunąć istniejące reguły skalowania automatycznego utworzone w poprzednich przykładach, wybierz ikonę kosza.

    ![Usuwanie istniejących reguł autoskalowania](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Wybierz pozycję **Dodaj warunek skalowania.** Wybierz ikonę ołówka obok nazwy reguły i podaj nazwę, taką jak *Skalowanie w zewnątrz w każdym dniu pracy.*

    ![Zmienianie nazwy domyślnej reguły autoskalowania](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wybierz przycisk radiowy, aby **skalować do określonej liczby wystąpień.**
4. Aby skalować w górę liczbę wystąpień, wprowadź *10* jako liczbę wystąpień.
5. Wybierz **pozycję Powtórz określone dni** dla typu **Harmonogram.**
6. Wybierz wszystkie dni robocze od poniedziałku do piątku.
7. Wybierz odpowiednią strefę czasową, a następnie określ czas **rozpoczęcia** *09:00.*
8. Wybierz ponownie **pozycję Dodaj warunek skalowania.** Powtórz ten proces, aby  utworzyć harmonogram o nazwie Skaluj w poziomie wieczorem, który będzie skalowany do *3* wystąpień, powtarzany każdego dnia roboczego i rozpoczyna się o *godzinie 18:00.*
9. Aby zastosować reguły automatycznego skalowania opartego na harmonogramie, wybierz pozycję **Zapisz.**

    ![Tworzenie reguł autoskalowania, które są skalowane zgodnie z harmonogramem](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Aby zobaczyć, jak są stosowane reguły skalowania automatycznego, wybierz pozycję **Historia uruchamiania** w górnej części **okna Skalowanie.** Wykres i lista zdarzeń pokazuje, kiedy reguły autoskalowania są wyzwalane, a liczba wystąpień maszyn wirtualnych w zestawie skalowania zwiększa się lub zmniejsza.


## <a name="next-steps"></a>Następne kroki
W tym artykule opisano sposób używania reguł skalowania automatycznego  do skalowania w poziomie oraz zwiększania lub zmniejszenia liczby wystąpień maszyn wirtualnych w zestawie skalowania. Można również skalować w pionie, aby zwiększyć lub zmniejszyć rozmiar wystąpienia maszyny *wirtualnej.* Aby uzyskać więcej informacji, zobacz [Vertical autoscale with Virtual Machine Scale sets (Skalowanie automatyczne w pionie za pomocą zestawów skalowania maszyn wirtualnych).](virtual-machine-scale-sets-vertical-scale-reprovision.md)

Aby uzyskać informacje na temat zarządzania wystąpieniami maszyn wirtualnych, zobacz [Zarządzanie zestawami skalowania](./virtual-machine-scale-sets-manage-powershell.md)maszyn wirtualnych za pomocą Azure PowerShell .

Aby dowiedzieć się, jak generować alerty po wyzwoleniu reguł autoskalowania, zobacz Wysyłanie powiadomień e-mail i powiadomień [o alertach](../azure-monitor/autoscale/autoscale-webhook-email.md)dotyczących alertów dotyczących Azure Monitor . Możesz również użyć [dzienników inspekcji do wysyłania](../azure-monitor/alerts/alerts-log-webhook.md)powiadomień o alertach dotyczących poczty e-mail i alertów dotyczących Azure Monitor .
