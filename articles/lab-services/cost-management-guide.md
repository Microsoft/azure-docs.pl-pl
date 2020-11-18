---
title: Przewodnik po zarządzaniu kosztami dla Azure Lab Services
description: Poznaj różne sposoby wyświetlania kosztów usług Lab Services.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659730"
---
# <a name="cost-management-for-azure-lab-services"></a>Zarządzanie kosztami dla Azure Lab Services

W przypadku Azure Lab Services zarządzanie kosztami można podzielić na dwa różne obszary: oszacowanie kosztów i analizę kosztów. Szacowanie kosztów odbywa się podczas konfigurowania laboratorium, aby upewnić się, że początkowa struktura laboratorium mieści się w oczekiwanym budżecie. Analiza kosztów zwykle odbywa się na koniec miesiąca, aby określić niezbędne działania w następnym miesiącu.

## <a name="estimate-the-lab-costs"></a>Oszacuj koszty laboratorium

Każdy pulpit nawigacyjny laboratorium ma **koszt & sekcji rozliczeń** , która zawiera przybliżone oszacowanie dotyczące tego, co jest kosztem danego miesiąca. Szacowany koszt podsumowuje użycie godzinowe przez maksymalną liczbę użytkowników według szacowanego kosztu na godzinę. Aby uzyskać najdokładniejsze oszacowanie, skonfiguruj laboratorium, w tym [harmonogram](how-to-create-schedules.md). Pulpit nawigacyjny będzie odzwierciedlać szacowany koszt. 

To oszacowanie może nie zawierać wszystkich możliwych kosztów. Niektóre zasoby nie są uwzględniane:

- Koszt przygotowania szablonu. Może się znacznie różnić w czasie koniecznym do utworzenia szablonu. Koszt uruchomienia szablonu jest taki sam jak ogólny koszt laboratorium na godzinę. 
- Dowolnych kosztów [udostępnionej galerii obrazów](how-to-use-shared-image-gallery.md) , ponieważ Galeria może być współużytkowana przez wiele laboratoriów. 
- Godziny naliczane, gdy twórca laboratorium uruchomi maszynę wirtualną (VM).

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający oszacowanie kosztów pulpitu nawigacyjnego.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analizuj użycie poprzedniego miesiąca

Analiza kosztów służy do przeglądania użycia poprzedniego miesiąca, aby pomóc w ustaleniu dowolnych dostosowań dla laboratorium. Podział kosztów ponoszonych w ramach [analizy kosztów subskrypcji](../cost-management-billing/costs/quick-acm-cost-analysis.md)można znaleźć w temacie. W Azure Portal możesz wprowadzić **subskrypcje** w polu wyszukiwania, a następnie wybrać opcję **subskrypcje** . 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający pole wyszukiwania i opcję subskrypcje.](./media/cost-management-guide/subscription-search.png)

Wybierz konkretną subskrypcję, którą chcesz przejrzeć.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący wybór subskrypcji.](./media/cost-management-guide/subscription-select.png)

Wybierz pozycję **Analiza kosztów** w lewym okienku w obszarze **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający analizę kosztów subskrypcji na wykresie.](./media/cost-management-guide/subscription-cost-analysis.png)

Ten pulpit nawigacyjny umożliwia dokładne analizowanie kosztów, w tym możliwość eksportowania do różnych typów plików zgodnie z harmonogramem. Aby uzyskać więcej informacji, zobacz [Cost Management + Omówienie rozliczeń](../cost-management-billing/cost-management-billing-overview.md).

Można filtrować według typu zasobu. Użycie `microsoft.labservices/labaccounts` spowoduje wyświetlenie tylko kosztu związanego z usługami Lab Services.

## <a name="understand-the-usage"></a>Informacje o użyciu

Poniższy zrzut ekranu przedstawia przykład analizy kosztów.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący przykładową analizę kosztów dla subskrypcji.](./media/cost-management-guide/cost-analysis.png)

Domyślnie istnieje sześć kolumn: **zasób**, **Typ zasobu**, **Lokalizacja**, **Nazwa grupy zasobów**, **Tagi** i **koszt**. Kolumna **Resource** zawiera informacje o koncie laboratorium, nazwie laboratorium i maszynie wirtualnej. Wiersze pokazujące konto laboratorium, nazwę laboratorium i domyślny (drugi i trzeci wiersz) są kosztami laboratorium. Używane maszyny wirtualne mają koszt, który można wyświetlić w przypadku wierszy, które wyświetlają konto laboratorium, nazwę laboratorium, domyślną i nazwę maszyny wirtualnej. 

W tym przykładzie dodanie pierwszego i drugiego wiersza (Rozpocznij od **aaalab/dockerlab**) spowoduje udostępnienie łącznego kosztu laboratorium "dockerlab" w ramach konta laboratorium "aaalab".

Aby uzyskać ogólny koszt galerii obrazów, Zmień typ zasobu na `Microsoft.Compute/Galleries` . Galeria obrazów udostępnionych może nie być wyświetlana w kosztach, w zależności od tego, gdzie jest przechowywana Galeria.

> [!NOTE]
> Udostępniona Galeria obrazów jest połączona z kontem laboratorium. Oznacza to, że wiele laboratoriów może korzystać z tego samego obrazu.

## <a name="separate-the-costs"></a>Oddziel koszty

Niektóre szkoły wyższe używały konta laboratorium i grupy zasobów jako sposobów rozdzielania klas. Każda klasa ma własne konto laboratorium i grupę zasobów. 

W okienku analiza kosztów Dodaj filtr oparty na nazwie grupy zasobów z odpowiednią nazwą grupy zasobów dla klasy. Następnie będą widoczne tylko koszty tej klasy. Umożliwia to wyraźniejsze rozróżnianie klas podczas wyświetlania kosztów. Możesz użyć funkcji [zaplanowana eksport](../cost-management-billing/costs/tutorial-export-acm-data.md) w analizie kosztów, aby pobrać koszty każdej klasy w oddzielnych plikach.

## <a name="manage-costs"></a>Zarządzanie kosztami

W zależności od typu klasy istnieją sposoby zarządzania kosztami, aby zmniejszyć liczbę wystąpień maszyn wirtualnych, które są uruchomione bez studenta.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Ustawienia automatycznego zamykania dla kontroli kosztów

Funkcje automatycznego zamykania umożliwiają zapobieganie utracie czasu użycia maszyn wirtualnych w laboratoriach. Poniższe ustawienia przechwytują większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący trzy ustawienia automatycznego zamykania.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Te ustawienia można skonfigurować zarówno na poziomie konta laboratorium, jak i na poziomie laboratorium. Jeśli włączysz je na poziomie konta laboratorium, zostaną one zastosowane do wszystkich laboratoriów w ramach konta laboratorium. W przypadku wszystkich nowych kont laboratorium te ustawienia są domyślnie włączone. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Automatyczne rozłączanie użytkowników z maszyn wirtualnych uznawanych za bezczynne przez system operacyjny

> [!NOTE]
> To ustawienie jest dostępne tylko dla maszyn wirtualnych z systemem Windows.

Gdy ustawienie **bezczynne użytkownicy rozłączenia nie są** włączone, użytkownik zostanie odłączony od wszystkich maszyn w laboratorium, gdy system operacyjny Windows uzna, że sesja jest bezczynna (w tym szablonów maszyn wirtualnych). [Definicja systemu operacyjnego Windows do bezczynności](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) używa dwóch kryteriów: 

* Nieobecność użytkownika: brak klawiatury lub myszy.
* Brak użycia zasobów: wszystkie procesory i wszystkie dyski były bezczynne przez określony procent czasu.

Użytkownicy będą widzieć następujący komunikat na maszynie wirtualnej przed rozłączeniem: 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający komunikat ostrzegawczy informujący o tym, że sesja przekroczyła limit czasu i zostanie odłączona.](./media/cost-management-guide/idle-timer-expired.png)
 
Maszyna wirtualna jest nadal uruchomiona, gdy użytkownik zostanie odłączony. Jeśli użytkownik ponownie nawiązuje połączenie z maszyną wirtualną, logując się, system Windows lub pliki, które zostały otwarte lub pracują w niezapisanym miejscu przed rozłączeniem, nadal będą dostępne. W tym stanie, ponieważ maszyna wirtualna jest uruchomiona, nadal liczy się jako aktywny i naliczany koszt. 
 
Aby automatycznie zamknąć bezczynne maszyny wirtualne z systemem Windows, należy użyć kombinacji **rozłączania użytkowników, gdy maszyny wirtualne są w stanie bezczynności** i wyłączać **maszyny wirtualne, gdy użytkownicy łączą** ustawienia.

Na przykład, jeśli skonfigurujesz ustawienia w następujący sposób:
 
* **Rozłącz użytkowników, gdy maszyny wirtualne są w stanie bezczynności**: 15 minut po wykryciu stanu bezczynności.
* **Zamknij maszyny wirtualne, gdy użytkownicy odłączają** się: 5 minut po rozłączeniu użytkownika.
 
Maszyny wirtualne z systemem Windows zostaną automatycznie zamknięte przez 20 minut od momentu ich zatrzymania przez użytkownika. 
 
> [!div class="mx-imgBorder"]
> ![Diagram przedstawiający kombinację ustawień powodujących automatyczne zamykanie maszyny wirtualnej.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników
 
Ustawienie **odłączania maszyn wirtualnych, gdy użytkownicy** obsługują zarówno maszyny wirtualne z systemem Windows, jak i Linux. Gdy to ustawienie jest włączone, automatyczne zamykanie nastąpi po:
 
* W przypadku systemu Windows połączenie Pulpit zdalny (RDP) zostało rozłączone.
* W systemie Linux połączenie SSH zostało rozłączone.
 
> [!NOTE]
> Obsługiwane są tylko [określone dystrybucje i wersje systemu Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) .
 
Możesz określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się
 
W środowisku laboratoryjnym użytkownik może uruchomić maszynę wirtualną, ale nigdy nie nawiązać z nią połączenia. Na przykład:
 
* Harmonogram w laboratorium uruchamia wszystkie maszyny wirtualne dla sesji klasy, ale niektórzy uczniowie nie są wyświetlani i nie łączą się z ich komputerami. 
* Użytkownik uruchamia maszynę wirtualną, ale zapomni o nawiązaniu połączenia. 
 
Ustawienie **Zamknij maszyny wirtualne, gdy użytkownicy nie nawiązują połączenia** , będzie przechwytywać te przypadki i automatycznie wyłączać maszyny wirtualne. 
 
Aby uzyskać informacje na temat konfigurowania i włączania automatycznego zamykania maszyn wirtualnych przy rozłączaniu, zobacz następujące artykuły:

* [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium](how-to-configure-lab-accounts.md)
* [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla laboratorium](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Zaplanowany czas a czas przydziału

Informacje o [zaplanowanym czasie](classroom-labs-concepts.md#schedules) i [czasie przydziału](classroom-labs-concepts.md#quota) ułatwią skonfigurowanie laboratorium do lepszego dopasowania do potrzeb profesor i studentów. 

Zaplanowana godzina to zestaw czasu, w którym wszystkie maszyny wirtualne uczniów zostały uruchomione i są dostępne do połączenia. Zaplanowany czas jest często używany, gdy wszyscy studenci mają własne maszyny wirtualne i są zgodnie z instrukcjami profesor w określonym czasie w dniu (na przykład w godzinach klas). Minusem polega na tym, że wszystkie maszyny wirtualne uczniów są uruchamiane i naliczają koszty, nawet jeśli student nie zaloguje się do maszyny wirtualnej. 

Czas przydziału jest przypisywany do każdego ucznia do użycia według własnego uznania i jest często używany do niezależnego badania. Maszyny wirtualne nie są uruchamiane, dopóki student nie uruchomi maszyny wirtualnej. 

Laboratorium może korzystać z czasu przydziału lub czasu zaplanowanych lub kombinacji obu tych metod. Jeśli Klasa nie wymaga zaplanowanego czasu, użyj tylko czasu przydziału dla najbardziej efektywnego wykorzystania maszyn wirtualnych.

### <a name="scheduled-event-stop-only"></a>Zaplanowane zdarzenie: tylko Zatrzymaj

W harmonogramie można dodać typ zdarzenia tylko do zatrzymania, który spowoduje zatrzymanie wszystkich maszyn w określonym czasie. Niektórzy właściciele laboratorium ustawili zdarzenie tylko do zatrzymania dla każdego dnia o północy, aby zmniejszyć użycie kosztów i przydziału, gdy student zapomni, aby zamknąć maszynę wirtualną, której używa. Minusem do tego typu zdarzenia, że wszystkie maszyny wirtualne zostaną zamknięte, nawet jeśli student korzysta z maszyny wirtualnej.

### <a name="other-costs-related-to-labs"></a>Inne koszty związane z laboratoriami 

Niektóre koszty nie są uwzględniane w usługach Lab Services, ale mogą być powiązane z usługą laboratoryjną. Możesz połączyć galerię obrazów udostępnionych z laboratorium, ale nie będzie ona widoczna w ramach kosztów usług laboratoryjnych i ma koszty. Aby zmniejszyć koszty ogólne, należy usunąć z galerii wszystkie nieużywane obrazy, ponieważ obrazy mają własny koszt magazynu. 

Laboratoria mogą łączyć się z innymi zasobami platformy Azure za pomocą sieci wirtualnej. Po usunięciu laboratorium należy usunąć sieć wirtualną i inne zasoby.

## <a name="conclusion"></a>Wniosek

Miejmy nadzieję informacje przedstawione w tym artykule zawierają lepsze zrozumienie narzędzi, które mogą pomóc w zmniejszeniu kosztów użytkowania.