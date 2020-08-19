---
title: Przewodnik po zarządzaniu kosztami dla Azure Lab Services
description: Poznaj różne sposoby wyświetlania kosztów usług Lab Services.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 0aaa454df05cd8981b314abe238163caced7864c
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604603"
---
# <a name="cost-management-for-azure-lab-services"></a>Zarządzanie kosztami dla Azure Lab Services

Zarządzanie kosztami można podzielić na dwa różne obszary: oszacowanie kosztów i analizę kosztów.  Szacowanie kosztów odbywa się podczas konfigurowania laboratorium, aby upewnić się, że początkowa struktura laboratorium mieści się w oczekiwanym budżecie.  Analiza kosztów zwykle odbywa się na koniec miesiąca w celu przeanalizowania kosztów i ustalenia niezbędnych działań w następnym miesiącu.

## <a name="estimate-the-lab-costs"></a>Oszacuj koszty laboratorium

Każdy pulpit nawigacyjny laboratorium ma **koszt & sekcji rozliczeń** , która zawiera przybliżone oszacowanie dotyczące tego, co jest kosztem danego miesiąca.  Szacowany koszt podsumowuje użycie godzinowe przez maksymalną liczbę użytkowników według szacowanego kosztu za godziny.  Aby uzyskać najdokładniejsze oszacowanie, skonfiguruj laboratorium, łącznie z [harmonogramem](how-to-create-schedules.md), a pulpit nawigacyjny będzie odzwierciedlać szacowany koszt.  

To oszacowanie może nie mieć wszystkich możliwych kosztów, ale istnieje kilka zasobów, które nie zostały uwzględnione.  Koszt przygotowania szablonu nie jest uwzględniany w szacowaniu kosztów.  Może się to różnić znacznie w czasie koniecznym do utworzenia szablonu. Koszt uruchomienia szablonu jest taki sam jak ogólny koszt laboratorium na godzinę. Wszystkie [udostępnione z galerii obrazów](how-to-use-shared-image-gallery.md) nie są uwzględniane w pulpicie nawigacyjnym laboratorium, ponieważ Galeria może być współdzielona przez wiele laboratoriów.  Na koniec, godz., gdy twórca laboratorium uruchomi maszynę, jest wykluczony z tego oszacowania.

> [!div class="mx-imgBorder"]
> ![Oszacowanie kosztów pulpitu nawigacyjnego](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analizowanie użycia poprzedniego miesiąca

Analiza kosztów służy do przeglądania poprzedniego miesięcznego użycia, aby pomóc w ustaleniu wszelkich dostosowań dla laboratorium.  Podział kosztów w przeszłości można znaleźć w [analizie kosztów subskrypcji](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  W Azure Portal można wpisać "subskrypcje" w górnym polu wyszukiwania, a następnie wybrać opcję subskrypcje.  

> [!div class="mx-imgBorder"]
> ![Wyszukiwanie subskrypcji](./media/cost-management-guide/subscription-search.png)

Wybierz konkretną subskrypcję, którą chcesz przejrzeć.

> [!div class="mx-imgBorder"]
> ![Wybór subskrypcji](./media/cost-management-guide/subscription-select.png)

 Wybierz pozycję "analiza kosztów" w okienku po lewej stronie w obszarze **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Analiza kosztów subskrypcji](./media/cost-management-guide/subscription-cost-analysis.png)

Ten pulpit nawigacyjny umożliwi dokładne analizowanie kosztów, w tym możliwość eksportowania do różnych typów plików zgodnie z harmonogramem.  Aby uzyskać więcej informacji, Cost Management ma wiele możliwości, zobacz [Omówienie Rozliczeń Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtrowanie według typu zasobu: `microsoft.labservices/labaccounts` spowoduje wyświetlenie tylko kosztu związanego z usługami Lab Services.

## <a name="understand-the-usage"></a>Informacje o użyciu

Poniżej znajduje się przykład analizy kosztów.

> [!div class="mx-imgBorder"]
> ![Analiza kosztów subskrypcji](./media/cost-management-guide/cost-analysis.png)

Domyślnie istnieje sześć kolumn: zasób, typ zasobu, lokalizacja, nazwa grupy zasobów, Tagi, koszt.  Kolumna Resource zawiera informacje o koncie laboratorium, nazwie laboratorium i maszynie wirtualnej.  Wiersze z kontem Lab/Lab name/default są kosztami laboratorium, które mogą być widoczne w drugim i trzecim wierszu.  Używane maszyny wirtualne będą miały koszt w ramach konta laboratorium/nazwy laboratorium/nazwy domyślnej/maszyny wirtualnej.  W tym przykładzie dodanie pierwszego wiersza z drugim wierszem, rozpoczynając od "aaalab/dockerlab", spowoduje łączny koszt dla laboratorium "dockerlab" w ramach konta laboratorium "aaalab".

Aby uzyskać informacje o udostępnionej galerii obrazów, Zmień typ zasobu na `Microsoft.Compute/Galleries` , co spowoduje uzyskanie całkowitego kosztu galerii obrazów.  Galerie obrazów udziałów mogą nie być wyświetlane w kosztach w zależności od tego, gdzie jest przechowywana Galeria.

> [!NOTE]
> Udostępniona Galeria obrazów jest połączona z kontem laboratorium.  Oznacza to, że wiele laboratoriów może korzystać z tego samego obrazu.

## <a name="separating-costs"></a>Rozdzielenie kosztów

Niektóre uniwersytety używały konta laboratorium i grupy zasobów jako metody oddzielenia różnych klas.  Każda klasa będzie miała własne konto laboratorium i grupę zasobów. W okienku analiza kosztów Dodaj filtr oparty na nazwie grupy zasobów z odpowiednią nazwą grupy zasobów dla klasy, a tylko koszty tej klasy będą widoczne.  Umożliwia to wyraźniejsze rozróżnienie między różnymi klasami podczas wyświetlania kosztów.  Funkcja [zaplanowana eksport](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) w analizie kosztów umożliwia pobranie kosztów każdej klasy w oddzielnych plikach.

## <a name="managing-costs"></a>Zarządzanie kosztami

W zależności od typu klasy istnieją sposoby zarządzania kosztami, aby zmniejszyć liczbę uruchomionych maszyn wirtualnych bez uczniów przy użyciu maszyny.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Maksymalizuj kontrolę kosztów za pomocą ustawień automatycznego zamykania

Automatyczne zamykanie funkcji kontroli kosztów pozwala uniknąć marnowania godzin użycia maszyn wirtualnych w laboratoriach. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:

> [!div class="mx-imgBorder"]
> ![Analiza kosztów subskrypcji](./media/cost-management-guide/auto-shutdown-disconnect.png)

Te ustawienia można skonfigurować zarówno na poziomie konta laboratorium, jak i na poziomie laboratorium. Jeśli ustawienia są włączone na poziomie konta laboratorium, są one stosowane do wszystkich laboratoriów w ramach konta laboratorium. W przypadku wszystkich nowych kont laboratorium te ustawienia są domyślnie włączone. 

#### <a name="details-about-auto-shutdown-settings"></a>Szczegóły dotyczące ustawień automatycznego zamykania

* Automatyczne rozłączanie użytkowników z maszyn wirtualnych, które system operacyjny uzna za bezczynne (tylko system Windows).

    > [!NOTE]
    > To ustawienie jest dostępne tylko dla maszyn wirtualnych z systemem Windows.

    Gdy to ustawienie jest włączone, użytkownik zostanie odłączony od wszystkich maszyn w laboratorium, gdy system operacyjny Windows uzna, że sesja jest bezczynna (w tym szablonów maszyn wirtualnych). [Definicja systemu operacyjnego Windows dla bezczynności](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) używa dwóch kryteriów: 

    * Nieobecność użytkownika — brak klawiatury ani danych wejściowych myszy.
    * Brak użycia zasobów — wszystkie procesory i wszystkie dyski były bezczynne przez określony% czasu

    Użytkownicy będą widzieli komunikat podobny do tego wewnątrz maszyny wirtualnej przed rozłączeniem: 

    > [!div class="mx-imgBorder"]
    > ![Analiza kosztów subskrypcji](./media/cost-management-guide/idle-timer-expired.png)
    
    Maszyna wirtualna jest nadal uruchomiona, gdy użytkownik zostanie odłączony. Jeśli użytkownik ponownie nawiązuje połączenie z maszyną wirtualną przez zalogowanie się, system Windows lub pliki, które zostały otwarte lub niezapisane wcześniej, nadal będą dostępne. W tym stanie, ponieważ maszyna wirtualna jest uruchomiona, nadal liczy się jako aktywny i naliczany koszt. 
    
    Aby automatycznie zamknąć bezczynne maszyny wirtualne z systemem Windows, należy użyć kombinacji **rozłączania użytkowników, gdy maszyny wirtualne są w stanie bezczynności** i **zamknąć maszyny wirtualne, gdy użytkownicy łączą** ustawienia.

    Na przykład, jeśli skonfigurujesz ustawienia w następujący sposób:
    
    * Rozłącz użytkowników, gdy wykryje się, że maszyny wirtualne są w stanie bezczynności — 15 minut.
    * Zamknij maszyny wirtualne, gdy użytkownicy rozłączą się – 5 minut po rozłączeniu użytkownika.
    
    Maszyny wirtualne z systemem Windows zostaną automatycznie zamknięte przez 20 minut od momentu ich zatrzymania przez użytkownika. 
    
    > [!div class="mx-imgBorder"]
    > ![Analiza kosztów subskrypcji](./media/cost-management-guide/vm-idle-diagram.png)
* Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników (Windows & Linux).
    
    To ustawienie obsługuje maszyny wirtualne z systemami Windows i Linux. Gdy to ustawienie jest włączone, automatyczne zamykanie nastąpi po:
    
    * W przypadku systemu Windows połączenie Pulpit zdalny (RDP) zostało rozłączone.
    * W przypadku systemu Linux połączenie SSH zostało rozłączone.
    
    > [!NOTE]
    > Obsługiwane są tylko [określone dystrybucje i wersje systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) .
    
    Możesz określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem. 
* Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.
     
    W środowisku laboratoryjnym użytkownik może uruchomić maszynę wirtualną, ale nigdy nie nawiązać z nią połączenia. Na przykład:
    
    * Harmonogram w laboratorium uruchamia wszystkie maszyny wirtualne dla sesji klasy, ale niektórzy uczniowie nie są wyświetlani i nie łączą się z ich komputerami.  
    * Użytkownik uruchamia maszynę wirtualną, ale zapomni o nawiązaniu połączenia. 
    
    Ustawienie "Zamknij maszyny wirtualne, gdy użytkownicy nie nawiązują połączenia" będzie przechwytywać te przypadki i automatycznie wyłączać maszyny wirtualne.  
    
Aby uzyskać informacje na temat konfigurowania i włączania automatycznego zamykania maszyn wirtualnych przy rozłączaniu, zobacz następujące artykuły:

* [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium](how-to-configure-lab-accounts.md)
* [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla laboratorium](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Przydział a zaplanowany czas

Zrozumienie [czasu przydziału](classroom-labs-concepts.md#quota) w porównaniu z [zaplanowanym czasem](classroom-labs-concepts.md#schedules) umożliwi właścicielowi laboratorium skonfigurowanie laboratorium do lepszego dopasowania do potrzeb profesor i studentów.  Zaplanowana godzina to zestaw czasu, w którym wszystkie maszyny wirtualne uczniów zostały uruchomione i są dostępne do nawiązania połączenia.  Często planowane jest użycie w sytuacji, gdy wszyscy studenci będą mieć własną maszynę wirtualną i są zgodne z instrukcjami profesor w określonym czasie w ciągu dnia, takiego jak godziny klasy.  Minusem polega na tym, że wszystkie maszyny wirtualne uczniów są uruchamiane i naliczają koszty, nawet jeśli student nie zaloguje się do maszyny wirtualnej.  Czas przydziału jest przypisywany do każdego ucznia, którego mogą używać według własnego uznania i jest często używany do niezależnego badania. Maszyny wirtualne nie są uruchamiane do momentu uruchomienia przez studenta maszyny wirtualnej.  

Laboratorium może korzystać z czasu przydziału, zaplanowanego czasu lub kombinacji obu tych elementów. Jeśli Klasa nie jest potrzebna w zaplanowanym czasie, użyj tylko czasu przydziału dla najbardziej efektywnego użycia maszyn wirtualnych.

### <a name="scheduled-event---stop-only"></a>Zaplanowane zdarzenie — tylko zatrzymywanie

W harmonogramie można dodać typ zdarzenia tylko Zatrzymaj, które spowoduje zatrzymanie wszystkich maszyn w określonym czasie.  Niektórzy właściciele laboratorium ustawili zdarzenie zatrzymania tylko dla każdego dnia o północy, aby zmniejszyć użycie kosztów i przydziału, gdy student zapomni, aby zamknąć maszynę wirtualną, której używa.  Minusem do tego typu zdarzenia, że wszystkie maszyny wirtualne zostaną zamknięte, nawet jeśli student korzysta z maszyny wirtualnej.

### <a name="other-costs-related-to-labs"></a>Inne koszty związane z laboratoriami 

Istnieją koszty, które nie są uwzględniane w usługach laboratoryjnych, ale mogą być powiązane z usługą laboratoryjną.  Udostępniona Galeria obrazów może być połączona z laboratoriami, ale nie jest wyświetlana pod kosztem usług laboratoryjnych i ma koszty.  Aby pomóc w utrzymaniu ogólnych kosztów, należy usunąć wszystkie nieużywane obrazy z galerii, ponieważ obrazy mają koszt magazynu dziedziczenia.  Laboratoria mogą mieć połączenia z innym zasobem platformy Azure przez sieć wirtualną (VNet) po usunięciu laboratorium należy usunąć sieć wirtualna i inne zasoby.

## <a name="conclusion"></a>Podsumowanie

Miejmy nadzieję powyższe informacje zapewniają lepszy wgląd w koszty użycia oraz sposoby korzystania z tych narzędzi w celu ograniczenia nadmiernych kosztów.
