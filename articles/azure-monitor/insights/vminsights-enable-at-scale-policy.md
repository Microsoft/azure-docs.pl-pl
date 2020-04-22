---
title: Włącz usługę Azure Monitor dla maszyn wirtualnych przy użyciu zasad platformy Azure
description: W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych dla wielu maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu zasad platformy Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 73c18d45136eea90ad29dc1bd40c4539dddc0ee6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767251"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Włącz usługę Azure Monitor dla maszyn wirtualnych przy użyciu zasad platformy Azure

W tym artykule wyjaśniono, jak włączyć usługę Azure Monitor dla maszyn wirtualnych dla maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu zasad platformy Azure. Na końcu tego procesu zostanie pomyślnie skonfigurowany włączenie usługi Log Analytics i agentów zależności i zidentyfikowane maszyny wirtualne, które nie są zgodne.

Aby odnajdować, zarządzać i włączyć usługę Azure Monitor dla maszyn wirtualnych dla wszystkich maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych, można użyć usługi Azure Policy lub programu Azure PowerShell. Zasady platformy Azure to metoda, którą zalecamy, ponieważ można zarządzać definicjami zasad, aby skutecznie zarządzać subskrypcjami, aby zapewnić spójną zgodność i automatyczne włączanie nowo aprowizacji maszyn wirtualnych. Następujące definicje zasad:

* Wdrażanie agenta usługi Log Analytics i agenta zależności.
* Sprawozdanie na temat wyników zgodności.
* Korygowanie niezgodnych maszyn wirtualnych.

Jeśli chcesz wykonać te zadania za pomocą usługi Azure PowerShell lub szablonu usługi Azure Resource Manager, zobacz [Włączanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu szablonów usługi Azure PowerShell lub Usługi Azure Resource Manager.](vminsights-enable-at-scale-powershell.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem zasad do dołączania maszyn wirtualnych platformy Azure i zestawów skalowania maszyny wirtualnej do monitorowania platformy Azure dla maszyn wirtualnych należy włączyć rozwiązanie VMInsights w obszarze roboczym, który będzie używany do przechowywania danych monitorowania. To zadanie można wykonać na stronie **Wprowadzenie** w usłudze Azure Monitor na karcie **Inne opcje dołączania.**  Wybierz **pozycję Konfiguruj obszar roboczy,** który wyświetli monit o wybranie obszaru roboczego, który ma zostać skonfigurowany.

![Konfigurowanie obszaru roboczego](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Obszar roboczy można również skonfigurować, wybierając pozycję **Włącz przy użyciu zasad,** a następnie przycisk **Konfiguruj obszar roboczy.**  Spowoduje to zainstalowanie rozwiązania VMInsights w wybranym obszarze roboczym, który umożliwi obszarowi roboczemu przechowywanie danych monitorowania wysyłanych przez maszyny wirtualne i zestawy skalowania maszyny wirtualnej, które zostaną włączone przy użyciu zasad. 

![Włączanie korzystania z zasad](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Omówienie funkcji Zarządzanie zakresem zasad

Zakres zasad usługi Azure Monitor for VMs upraszcza odnajdowanie, zarządzanie i włączanie na dużą skalę inicjatywy **Włącz usługę Azure Monitor dla maszyn wirtualnych,** która obejmuje definicje zasad wymienione wcześniej. Aby uzyskać dostęp do tej funkcji, wybierz **inne opcje dołączania** z karty **Wprowadzenie** w usłudze Azure Monitor dla maszyn wirtualnych. Wybierz **pozycję Zarządzaj zakresem zasad,** aby otworzyć stronę **Zasady usługi Azure Monitor dla maszyn wirtualnych.**

![Karta Wprowadzenie do usługi Azure Monitor z maszyn wirtualnych](./media/vminsights-enable-at-scale-policy/get-started-page.png)

W tym miejscu możesz sprawdzić zasięg inicjatywy i zarządzać nim w grupach zarządzania i subskrypcjach. Można zrozumieć, ile maszyn wirtualnych istnieje w każdej z grup zarządzania i subskrypcji i ich stanu zgodności.

![Strona Zasady zarządzania usługami Azure Monitor dla maszyn wirtualnych](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Te informacje są przydatne, aby ułatwić planowanie i wykonywanie scenariusza nadzoru dla usługi Azure Monitor dla maszyn wirtualnych z jednej centralnej lokalizacji. Usługa Azure Policy udostępnia widok zgodności, gdy do zakresu przypisano zasadę lub inicjatywę, na tej nowej stronie można znaleźć miejsce, w którym zasady lub inicjatywa nie są przypisane, i przypisać ją na swoim miejscu. Wszystkie akcje, takie jak przypisywanie, wyświetlanie i edytowanie przekierowania bezpośrednio do usługi Azure Policy. Strona **Zasady usługi Azure Monitor dla maszyn wirtualnych** jest rozszerzonym i zintegrowanym środowiskom tylko dla inicjatywy Włącz usługę Azure Monitor dla maszyn **wirtualnych.**

Na tej stronie można również skonfigurować obszar roboczy usługi Log Analytics dla usługi Azure Monitor dla maszyn wirtualnych, które:

- Instaluje rozwiązanie mapy usług.
- Włącza liczniki wydajności systemu operacyjnego używane przez wykresy wydajności, skoroszyty oraz niestandardowe kwerendy i alerty dziennika.

![Usługa Azure Monitor dla maszyn wirtualnych konfiguruje obszar roboczy](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ta opcja nie jest powiązana z żadnymi akcjami zasad. Jest dostępna, aby zapewnić łatwy sposób spełnienia [wymagań wstępnych wymaganych](vminsights-enable-overview.md) do włączania usługi Azure Monitor dla maszyn wirtualnych.  

### <a name="what-information-is-available-on-this-page"></a>Jakie informacje są dostępne na tej stronie?

Poniższa tabela zawiera podział informacji, które są prezentowane na stronie zakresu zasad i jak je interpretować.

| Funkcja | Opis | 
|----------|-------------| 
| **Zakres** | Grupa zarządzania i subskrypcje, do których masz lub dziedziczono dostęp z możliwością przechodzenia do szczegółów hierarchii grupy zarządzania.|
| **Rola** | Twoja rola w zakresie, który może być czytelnik, właściciel lub współautor. W niektórych przypadkach może wydawać się puste, aby wskazać, że możesz mieć dostęp do subskrypcji, ale nie do grupy zarządzania, do której należy. Informacje w innych kolumnach różnią się w zależności od roli. Rola jest kluczowa w określaniu, jakie dane można wyświetlać i akcje, które można wykonać w zakresie przypisywania zasad lub inicjatyw (właściciela), edytowania ich lub wyświetlania zgodności. |
| **Łączna liczba maszyn wirtualnych** | Liczba maszyn wirtualnych w tym zakresie. Dla grupy zarządzania jest to suma maszyn wirtualnych zagnieżdżonych w ramach subskrypcji lub podrzędnej grupy zarządzania. |
| **Pokrycie przydziału** | Procent maszyn wirtualnych, które są objęte zasad lub inicjatywy. |
| **Stan przydziału** | Informacje o stanie zasad lub cesji inicjatywy. |
| **Zgodne maszyny wirtualne** | Liczba maszyn wirtualnych, które są zgodne w ramach zasad lub inicjatywy. W przypadku inicjatywy **Włącz usługę Azure Monitor dla maszyn wirtualnych**jest to liczba maszyn wirtualnych, które mają agenta usługi Log Analytics i agenta zależności. W niektórych przypadkach może wydawać się puste z powodu braku przypisania, żadnych maszyn wirtualnych lub za mało uprawnień. Informacje są dostarczane w stanie **zgodności**. |
| **Zgodność** | Ogólna liczba zgodności jest sumą różnych zasobów, które są zgodne podzielone przez sumę wszystkich różnych zasobów. |
| **Stan zgodności** | Informacje o stanie zgodności dla przypisania zasad lub inicjatywy.|

Po przypisaniu zasady lub inicjatywy zakres wybrany w przypisaniu może być zakresem na liście lub jego podzbiorem. Na przykład można było utworzyć przypisanie dla subskrypcji (zakres zasad), a nie grupy zarządzania (zakres zasięgu). W takim przypadku wartość **pokrycia przypisania** wskazuje maszyny wirtualne w zakresie zasad lub inicjatywy podzielone przez maszyny wirtualne w zakresie zasięgu. W innym przypadku można wykluczyć niektóre maszyny wirtualne, grupy zasobów lub subskrypcję z zakresu zasad. Jeśli wartość jest pusta, oznacza to, że zasady lub inicjatywy nie istnieje lub nie masz uprawnień. Informacje są dostarczane w obszarze **Status przydziału**.

## <a name="enable-by-using-azure-policy"></a>Włączanie przy użyciu zasad platformy Azure

Aby włączyć usługę Azure Monitor dla maszyn wirtualnych przy użyciu zasad platformy Azure w dzierżawie:

- Przypisz inicjatywę do zakresu: grupy zarządzania, subskrypcji lub grupy zasobów.
- Przejrzyj i koryguj wyniki zgodności.

Aby uzyskać więcej informacji na temat przypisywania zasad platformy Azure, zobacz [omówienie zasad platformy Azure](../../governance/policy/overview.md#assignments) i zapoznaj [się z omówieniem grup zarządzania](../../governance/management-groups/overview.md) przed kontynuowaniem.

### <a name="policies-for-azure-vms"></a>Zasady dotyczące maszyn wirtualnych platformy Azure

Definicje zasad dla maszyny Wirtualnej platformy Azure są wymienione w poniższej tabeli.

|Nazwa |Opis |Typ |
|-----|------------|-----|
|Włączanie usługi Azure Monitor dla maszyn wirtualnych |Włącz usługę Azure Monitor dla maszyn wirtualnych w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. |Inicjatywy |
|Wdrożenie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny |Raporty maszyn wirtualnych jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowany na liście i agent nie jest zainstalowany. |Zasady |
|Wdrożenie agenta analizy dzienników inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny |Raporty maszyn wirtualnych jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowany na liście i agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta zależności dla maszyn wirtualnych z systemem Linux |Wdrażanie agenta zależności dla maszyn wirtualnych z systemem Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta zależności dla maszyn wirtualnych systemu Windows |Wdrażanie agenta zależności dla maszyn wirtualnych systemu Windows, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux |Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows |Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zasady dotyczące zestawów skalowania maszyn wirtualnych platformy Azure

Definicje zasad dla zestawu skalowania maszyny wirtualnej platformy Azure są wymienione w poniższej tabeli.

|Nazwa |Opis |Typ |
|-----|------------|-----|
|Włączanie usługi Azure Monitor dla zestawów skalowania maszyn wirtualnych |Włącz usługę Azure Monitor dla zestawów skalowania maszyny wirtualnej w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. Uwaga: Jeśli zasady uaktualniania zestawu skalowania są ustawione na Ręczne, zastosuj rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując uaktualnienie na nich. W cli, jest `az vmss update-instances`to . |Inicjatywy |
|Wdrożenie agenta zależności inspekcji w zestawach skalowania maszyny wirtualnej — obraz maszyny wirtualnej (OS) niepubliczny |Raporty skalowania maszyny wirtualnej zestaw jako niezgodne, jeśli obraz maszyny wirtualnej (OS) nie jest zdefiniowany na liście i agent nie jest zainstalowany. |Zasady |
|Wdrożenie agenta analizy dzienników inspekcji w zestawach skalowania maszyny wirtualnej — obraz maszyny wirtualnej (OS) niepubliczny |Raporty skalowania maszyny wirtualnej zestaw jako niezgodne, jeśli obraz maszyny wirtualnej (OS) nie jest zdefiniowany na liście i agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta zależności dla zestawów skalowania maszyny wirtualnej systemu Linux |Wdrażanie agenta zależności dla zestawów skalowania maszyny wirtualnej systemu Linux, jeśli obraz maszyny wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta zależności dla zestawów skalowania maszyny wirtualnej systemu Windows |Wdrażanie agenta zależności dla zestawów skalowania maszyny wirtualnej systemu Windows, jeśli obraz maszyny wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyny wirtualnej systemu Linux |Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyny wirtualnej systemu Linux, jeśli obraz maszyny wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows |Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyny wirtualnej systemu Windows, jeśli obraz maszyny wirtualnej (OS) jest zdefiniowany na liście, a agent nie jest zainstalowany. |Zasady |

Zasady autonomiczne (nie uwzględnione w inicjatywie) opisano tutaj:

|Nazwa |Opis |Typ |
|-----|------------|-----|
|Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu |Zgłoś maszyny wirtualne jako niezgodne, jeśli nie logują się do obszaru roboczego usługi Log Analytics określonego w przypisaniu zasad lub inicjatywy. |Zasady |

### <a name="assign-the-azure-monitor-initiative"></a>Przypisywanie inicjatywy Usługi Azure Monitor

Aby utworzyć przypisanie zasad na stronie **Pokrycie zasad usługi Azure Monitor dla maszyn wirtualnych,** wykonaj następujące kroki. Aby dowiedzieć się, jak wykonać te kroki, zobacz [Tworzenie przypisania zasad z witryny Azure Portal](../../governance/policy/assign-policy-portal.md).

Po przypisaniu zasady lub inicjatywy zakres wybrany w przypisaniu może być zakresem wymienionym w tym miejscu lub jego podzbiorem. Na przykład można było utworzyć przypisanie dla subskrypcji (zakres zasad), a nie grupy zarządzania (zakres zasięgu). W takim przypadku procent pokrycia wskazuje maszyn wirtualnych w zakresie zasad lub inicjatywy podzielone przez maszyny wirtualne w zakresie zasięgu. W innym przypadku można wykluczyć niektóre maszyny wirtualne lub grupy zasobów lub subskrypcję z zakresu zasad. Jeśli jest pusta, oznacza to, że zasady lub inicjatywy nie istnieje lub nie masz uprawnień. Informacje są dostarczane w obszarze **Status przydziału**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W witrynie Azure portal wybierz pozycję **Monitor**. 

3. Wybierz pozycję **Maszyny wirtualne** w sekcji **Insights.**
 
4. Wybierz kartę **Wprowadzenie.** Na stronie wybierz pozycję **Zarządzaj pokryciem zasad**.

5. Wybierz grupę zarządzania lub subskrypcję z tabeli. Wybierz **zakres,** wybierając wielokropek (...). W przykładzie zakres ogranicza przypisanie zasad do grupowania maszyn wirtualnych w celu wymuszania.

6. Na stronie **przypisywania zasad platformy Azure** jest wstępnie wypełniona inicjatywą **Włącz usługę Azure Monitor dla maszyn wirtualnych.** 
    Pole **Nazwa przypisania** jest automatycznie wypełniane nazwą inicjatywy, ale można ją zmienić. Można również dodać opis opcjonalny. Pole **Przypisane przez** jest wypełniane automatycznie na podstawie tego, kto jest zalogowany. Ta wartość jest opcjonalna.

7. (Opcjonalnie) Aby usunąć jeden lub więcej zasobów z zakresu, wybierz pozycję **Wykluczenia**.

8. Z listy **rozwijanej Obszaru roboczego usługi Log Analytics** dla obsługiwanego regionu wybierz obszar roboczy.

   > [!NOTE]
   > Jeśli obszar roboczy wykracza poza zakres przydziału, przyznaj uprawnienia *współautora usługi Log Analytics* do identyfikatora głównego przypisania zasad. Jeśli tego nie zrobisz, może pojawić się `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` błąd wdrożenia, taki jak Udzielenie dostępu, przejrzyj sposób [ręcznego konfigurowania tożsamości zarządzanej](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Pole wyboru **Tożsamość zarządzana** jest zaznaczone, ponieważ przypisywana inicjatywa zawiera zasady z efektem *deployIfNotExists.*
    
9. Z listy rozwijanej **Zarządzanie lokalizacją tożsamości** wybierz odpowiedni region.

10. Wybierz opcję **Przypisz**.

Po utworzeniu przypisania strona **Usługa Azure Monitor dla zasad maszyn wirtualnych aktualizuje pokrycie przydziału,** stan **przydziału,** **zgodne maszyny wirtualne**i **stan zgodności** w celu odzwierciedlenia zmian. **Assignment Coverage** 

Poniższa macierz mapuje każdy możliwy stan zgodności dla inicjatywy.  

| Stan zgodności | Opis | 
|------------------|-------------|
| **Zgodne** | Wszystkie maszyny wirtualne w zakresie mają agentów analizy dzienników i zależności wdrożonych do nich.|
| **Niezgodne** | Nie wszystkie maszyny wirtualne w zakresie mają usługi Log Analytics i agentów zależności wdrożone do nich i może wymagać korygowania.|
| **Nie rozpoczęto** | Dodano nowe przypisanie. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1.</sup> | 
| **Puste** | Nie są przypisane żadne zasady. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela o udzielenie dostępu. Możesz też wyświetlać zgodność i zarządzać przydziałami za pośrednictwem podrzędnych grup zarządzania lub subskrypcji. 

W poniższej tabeli przedstawiono każdy możliwy stan przydziału dla inicjatywy.

| Stan przydziału | Opis | 
|------------------|-------------|
| **Powodzenie** | Wszystkie maszyny wirtualne w zakresie mają agentów analizy dzienników i zależności wdrożonych do nich.|
| **Ostrzeżenie** | Subskrypcja nie jest w grupie zarządzania.|
| **Nie rozpoczęto** | Dodano nowe przypisanie. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1.</sup> | 
| **Puste** | Nie istnieją maszyny wirtualne lub zasady nie są przypisane. | 
| **Akcja** | Przypisywanie zasad lub edytowanie przypisania. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela o udzielenie dostępu. Możesz też wyświetlać zgodność i zarządzać przydziałami za pośrednictwem podrzędnych grup zarządzania lub subskrypcji.

## <a name="review-and-remediate-the-compliance-results"></a>Przegląd i korygowanie wyników zgodności

Poniższy przykład dotyczy maszyny wirtualnej platformy Azure, ale dotyczy również zestawów skalowania maszyny wirtualnej. Aby dowiedzieć się, jak przeglądać wyniki zgodności, zobacz [Identyfikowanie wyników niezgodności](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na stronie **Pokrycie zasad usługi Azure Monitor dla maszyn wirtualnych** wybierz grupę zarządzania lub subskrypcję z tabeli. Wybierz **opcję Wyświetl zgodność,** wybierając wielokropek (...).   

![Zgodność zasad dla maszyn wirtualnych platformy Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Na podstawie wyników zasad zawartych w inicjatywie maszyny wirtualne są zgłaszane jako niezgodne w następujących scenariuszach:

* Agent usługi Log Analytics lub agent zależności nie jest wdrażany.  
    Ten scenariusz jest typowy dla zakresu z istniejącymi maszynami wirtualnymi. Aby go złagodzić, należy wdrożyć wymagane agentów, [tworząc zadania korygujące](../../governance/policy/how-to/remediate-resources.md) na niezgodnych zasadach.  
    - Wdrażanie agenta zależności dla maszyn wirtualnych z systemem Linux
    - Wdrażanie agenta zależności dla maszyn wirtualnych systemu Windows
    - Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
    - Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows

* Obraz maszyny Wirtualnej (OS) nie jest identyfikowany w definicji zasad.  
    Kryteria zasad wdrażania obejmują tylko maszyny wirtualne, które są wdrażane na podstawie dobrze znanych obrazów maszyn wirtualnych platformy Azure. Sprawdź dokumentację, aby zobaczyć, czy system operacyjny maszyny Wirtualnej jest obsługiwany. Jeśli nie jest obsługiwany, zduplikuj zasady wdrażania i zaktualizuj lub zmodyfikuj ją, aby obraz był zgodny.  
    - Wdrożenie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
    - Wdrożenie agenta analizy dzienników inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny

* Maszyny wirtualne nie logują się do określonego obszaru roboczego usługi Log Analytics.  
    Jest możliwe, że niektóre maszyny wirtualne w zakresie inicjatywy są logowania do obszaru roboczego usługi Log Analytics innych niż ten, który jest określony w przypisaniu zasad. Ta zasada jest narzędziem do identyfikowania maszyn wirtualnych, które zgłaszają do niezgodnego obszaru roboczego.  
    - Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu

## <a name="edit-an-initiative-assignment"></a>Edytowanie przypisania inicjatywy

W dowolnym momencie po przypisaniu inicjatywy do grupy zarządzania lub subskrypcji można ją edytować, aby zmodyfikować następujące właściwości:

- Nazwa przydziału
- Opis
- Przypisany przez
- Obszar roboczy usługi Log Analytics
- Wyjątki

## <a name="next-steps"></a>Następne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne do analizy za pomocą usługi Azure Monitor dla maszyn wirtualnych. 

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md) 

- Aby zidentyfikować wąskie gardła i ogólne wykorzystanie wydajności maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny Wirtualnej platformy Azure.](vminsights-performance.md) 
