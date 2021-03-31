---
title: Zarządzanie zasadami laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak definiować zasady laboratorium, takie jak rozmiary maszyn wirtualnych, Maksymalna liczba maszyn wirtualnych na użytkownika i Automatyzacja zamknięcia.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530356"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzaj wszystkimi zasadami dla laboratorium w Azure DevTest Labs

Azure DevTest Labs pozwala kontrolować koszt i zminimalizować liczbę odpadów w laboratoriach przez Zarządzanie zasadami (ustawieniami) dla każdego laboratorium. W tym artykule opisano szczegółowo krok po kroku, jak ustawić poszczególne zasady.  

## <a name="set-allowed-virtual-machine-sizes"></a>Ustaw dozwolone rozmiary maszyn wirtualnych
Zasady umożliwiające ustawienie dozwolonych rozmiarów maszyn wirtualnych ułatwiają minimalizowanie odpadów laboratoryjnych przez umożliwienie określania, które rozmiary maszyn wirtualnych są dozwolone w środowisku laboratoryjnym. Jeśli te zasady są aktywowane, do tworzenia maszyn wirtualnych mogą być używane tylko rozmiary maszyn wirtualnych z tej listy.

1. W [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium, a następnie wybierz pozycję **Konfiguracja i zasady**.

    ![Uzyskiwanie dostępu do konfiguracji i zasad laboratorium](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. W okienku **Konfiguracja i zasady** laboratorium wybierz **dozwolone rozmiary maszyn wirtualnych**.
   
    ![Dozwolone rozmiary maszyn wirtualnych](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Wybierz **opcję Włącz,** aby włączyć tę zasadę i **wyłączyć** ją.

1. Jeśli włączysz te zasady, wybierz co najmniej jeden rozmiar maszyny wirtualnej, który można utworzyć w środowisku laboratoryjnym.

1. Wybierz pozycję **Zapisz**.

## <a name="set-virtual-machines-per-user"></a>Ustaw maszyny wirtualne na użytkownika
Zasady dla **maszyn wirtualnych na użytkownika** umożliwiają określenie liczby maszyn wirtualnych, które mogą zostać utworzone przez pojedynczego użytkownika. Jeśli użytkownik próbuje utworzyć lub przejąć maszynę wirtualną, gdy zostanie osiągnięty limit użytkowników, komunikat o błędzie wskazuje, że maszyna wirtualna nie może zostać utworzona/przejęta. 

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **maszyny wirtualne na użytkownika**.
   
    ![Maszyny wirtualne na użytkownika](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wybierz pozycję **tak** , aby ograniczyć liczbę maszyn wirtualnych na użytkownika. Jeśli nie chcesz ograniczyć liczby maszyn wirtualnych na użytkownika, wybierz pozycję **nie**. W przypadku wybrania opcji **tak** wprowadź wartość liczbową wskazującą liczbę maszyn wirtualnych, które mogą zostać utworzone lub przejęte przez użytkownika. 

1. Wybierz opcję **tak** , aby ograniczyć liczbę maszyn wirtualnych, które mogą korzystać z dysku SSD. Jeśli nie chcesz ograniczyć liczby maszyn wirtualnych, które mogą korzystać z dysków SSD, wybierz pozycję **nie**. W przypadku wybrania opcji **tak** wprowadź wartość wskazującą liczbę maszyn wirtualnych, które można utworzyć przy użyciu dysku SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-virtual-machines-per-lab"></a>Ustaw maszyny wirtualne na laboratorium
Zasady dla **maszyn wirtualnych na laboratorium** umożliwiają określenie liczby maszyn wirtualnych, które można utworzyć dla bieżącego laboratorium. Jeśli użytkownik próbuje utworzyć maszynę wirtualną po spełnieniu limitu laboratorium, komunikat o błędzie wskazuje, że nie można utworzyć maszyny wirtualnej. 

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **maszyny wirtualne na laboratorium**.
   
    ![Maszyny wirtualne na laboratorium](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Wybierz pozycję **tak** , aby ograniczyć liczbę maszyn wirtualnych na laboratorium. Jeśli nie chcesz ograniczyć liczby maszyn wirtualnych na laboratorium, wybierz pozycję **nie**. W przypadku wybrania opcji **tak** wprowadź wartość liczbową wskazującą liczbę maszyn wirtualnych, które mogą zostać utworzone lub przejęte przez użytkownika. 

1. Wybierz opcję **tak** , aby ograniczyć liczbę maszyn wirtualnych, które mogą korzystać z dysku SSD. Jeśli nie chcesz ograniczyć liczby maszyn wirtualnych, które mogą korzystać z dysków SSD, wybierz pozycję **nie**. W przypadku wybrania opcji **tak** wprowadź wartość wskazującą liczbę maszyn wirtualnych, które można utworzyć przy użyciu dysku SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-auto-shutdown"></a>Ustawianie automatycznego zamykania
Zasady automatycznego zamykania ułatwiają minimalizowanie odpadów laboratoryjnych przez określenie czasu, w którym maszyny wirtualne tego laboratorium zostały zamknięte.

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **automatyczne zamykanie**.
   
    ![Automatyczne zamykanie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wybierz **opcję Włącz,** aby włączyć tę zasadę i **wyłączyć** ją.

1. Jeśli włączysz tę zasadę, określ czas (i strefę czasową), aby zamknąć wszystkie maszyny wirtualne w bieżącym laboratorium.

1. Wybierz opcję **tak** lub **nie** , aby można było wysłać powiadomienie 15 minut przed upływem określonego czasu automatycznego zamknięcia. Jeśli wybierzesz opcję **tak**, wprowadź punkt końcowy URL elementu webhook lub adres e-mail określający, gdzie ma być ogłaszane lub wysyłane. Użytkownik otrzymuje powiadomienie i ma możliwość odłożenia zamknięcia.

   Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API platformy Azure](../azure-functions/functions-bindings-http-webhook.md). 

1. Wybierz pozycję **Zapisz**.

Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć to ustawienie z określonej maszyny wirtualnej, Otwórz okienko zarządzania maszyną wirtualną i zmień jego ustawienie **automatycznego zamykania** .

## <a name="set-auto-shutdown-policy"></a>Ustawianie zasad automatycznego zamykania
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w laboratorium. Dzięki temu można zaoszczędzić koszty z uruchomionych maszyn, które nie są używane (bezczynny). Zasady zamykania można wymusić na wszystkich maszynach wirtualnych laboratorium centralnie, ale także zaoszczędzić użytkownikom laboratorium nakłady pracy z konfigurowania harmonogramu dla poszczególnych maszyn. Ta funkcja umożliwia ustawienie zasad w harmonogramie laboratorium, rozpoczynając od oferty bez kontroli w celu uzyskania pełnej kontroli dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować te zasady, wykonując następujące czynności:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz pozycję **zasady automatycznego zamykania** w sekcji **harmonogramy** menu po lewej stronie.
3. Wybierz jedną z opcji. Poniższe sekcje zawierają więcej informacji na temat tych opcji: zasady zestawu są stosowane tylko do nowych maszyn wirtualnych utworzonych w laboratorium, a nie do istniejących maszyn wirtualnych. 

    ![Opcje zasad automatycznego zamykania](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustawia harmonogram i może zrezygnować
Jeśli ustawisz laboratorium na te zasady, użytkownicy laboratorium mogą przesłonić lub zrezygnować z harmonogramu laboratorium. Ta opcja przyznaje użytkownikom laboratorium pełną kontrolę nad harmonogramem automatycznego zamykania maszyn wirtualnych. Użytkownicy laboratorium nie zmieniają żadnej zmiany na stronie harmonogramu automatycznego zamykania maszyny wirtualnej.

![Opcja zasad automatycznego zamykania — 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie może zrezygnować
Jeśli ustawisz laboratorium do tych zasad, użytkownicy laboratorium mogą przesłonić harmonogram laboratorium. Nie mogą jednak zrezygnować z zasad automatycznego zamykania. Ta opcja zapewnia, że każdy komputer w laboratorium jest objęty harmonogramem automatycznego zamykania. Użytkownicy laboratorium mogą zaktualizować harmonogram automatycznego zamykania dla swoich maszyn wirtualnych i skonfigurować powiadomienia o zamknięciu.

![Opcja zasad automatycznego zamykania — 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramem ustawionym przez administratora laboratorium
Jeśli ustawisz laboratorium na te zasady, użytkownicy laboratorium nie mogą przesłonić ani zrezygnować z harmonogramu laboratorium. Ta opcja zapewnia administratorowi laboratorium pełną kontrolę nad harmonogramem dla każdej maszyny w laboratorium. Użytkownicy laboratorium mogą konfigurować tylko powiadomienia o automatycznym zamknięciu dla swoich maszyn wirtualnych.

![Opcja zasad automatycznego zamykania — 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Ustawianie autostartu
Zasady autostartu umożliwiają określenie czasu uruchamiania maszyn wirtualnych w bieżącym laboratorium.  

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Autostart**.
   
    ![Funkcja przechwytywania ekranu pokazuje opcje automatycznego uruchamiania dla maszyny wirtualnej.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wybierz **opcję Włącz,** aby włączyć tę zasadę i **wyłączyć** ją.

3. Po włączeniu tych zasad Określ zaplanowany czas rozpoczęcia, strefę czasową i dni tygodnia, dla których ma zastosowanie ten czas. 

4. Wybierz pozycję **Zapisz**.

Po włączeniu tych zasad nie są automatycznie stosowane do żadnych maszyn wirtualnych w bieżącym laboratorium. Aby zastosować to ustawienie do określonej maszyny wirtualnej, Otwórz okienko zarządzania maszyną wirtualną i zmień jego ustawienie **Autostart** .

## <a name="set-expiration-date"></a>Ustaw datę wygaśnięcia
Podczas [tworzenia maszyny wirtualnej](devtest-lab-add-vm.md)można ustawić datę wygaśnięcia. W obszarze **Ustawienia zaawansowane** wybierz ikonę kalendarza, aby określić datę automatycznego usunięcia maszyny wirtualnej. Domyślnie maszyna wirtualna nigdy nie wygasa.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po zdefiniowaniu i zastosowaniu różnych ustawień zasad maszyny wirtualnej dla laboratorium należy wykonać następujące czynności:

* Informacje o [udostępnionych adresach IP](devtest-lab-shared-ip.md) — wyjaśnia, jak współużytkowane adresy IP są używane w DevTest Labs, aby zminimalizować liczbę publicznych adresów IP wymaganych do nawiązania połączenia z maszynami wirtualnymi laboratorium.
* [Konfigurowanie zarządzania kosztami](devtest-lab-configure-cost-management.md) — pokazuje, jak używać wykresu **trendu miesięcznego szacowanych kosztów**  
  Aby wyświetlić szacunkowy koszt bieżącego miesiąca oraz Przewidywany koszt końca miesiąca.
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny wirtualnej należy określić podstawową, która może być obrazem niestandardowym lub obrazem portalu Marketplace. W tym artykule pokazano, jak utworzyć obraz niestandardowy na podstawie pliku VHD.
* [Konfigurowanie obrazów z witryny Marketplace](devtest-lab-configure-marketplace-images.md) — Azure DevTest Labs obsługuje tworzenie maszyn wirtualnych w oparciu o obrazy w portalu Azure Marketplace. W tym artykule pokazano, jak określić, w jaki sposób można używać obrazów portalu Azure Marketplace podczas tworzenia maszyn wirtualnych w środowisku laboratoryjnym.
* [Tworzenie maszyny wirtualnej w laboratorium](devtest-lab-add-vm.md) — pokazuje, jak utworzyć maszynę wirtualną na podstawie obrazu podstawowego (niestandardowego lub Marketplace) oraz jak korzystać z artefaktów na maszynie wirtualnej.
