---
title: Dodawanie maszyny wirtualnej do laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal dodać maszynę wirtualną do laboratorium w Azure DevTest Labs. Można wybrać podstawę, która jest obrazem niestandardowym lub formułą.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297627"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Dodawanie maszyny wirtualnej do laboratorium w Azure DevTest Labs
Jeśli masz już [utworzoną pierwszą maszynę wirtualną](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), możesz to zrobić z wstępnie załadowanego [obrazu z portalu Marketplace](devtest-lab-configure-marketplace-images.md). Teraz, jeśli chcesz dodać kolejne maszyny wirtualne do laboratorium, możesz również wybrać *podstawę* , która jest [obrazem niestandardowym](devtest-lab-create-template.md) lub [formułą](devtest-lab-manage-formulas.md). W tym samouczku pokazano, jak za pomocą Azure Portal dodać maszynę wirtualną do laboratorium w DevTest Labs.

W tym artykule pokazano również, jak zarządzać artefaktami dla maszyny wirtualnej w laboratorium.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Procedura dodawania maszyny wirtualnej do laboratorium w Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **DevTest Labs** w sekcji **DEVOPS** . Jeśli wybierzesz pozycję * (gwiazdka) obok pozycji **DevTest Labs** w sekcji **DEVOPS** . Ta akcja dodaje **DevTest Labs** do menu nawigacji po lewej stronie, aby można było łatwo uzyskać do niego dostęp. Następnie możesz wybrać pozycję **DevTest Labs** w menu nawigacji po lewej stronie.

    ![Wszystkie usługi — wybierz pozycję DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz utworzyć maszynę wirtualną.
2. Na stronie **Przegląd** laboratorium wybierz pozycję **+ Dodaj**.

    ![Przycisk dodawania maszyny wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na stronie **Wybierz stronę podstawową** wybierz obraz witryny Marketplace dla maszyny wirtualnej.
1. Na karcie **Ustawienia podstawowe** na stronie **maszyna wirtualna** wykonaj następujące czynności:
    1. Wprowadź nazwę maszyny wirtualnej w polu tekstowym **Nazwa maszyny wirtualnej** . Pole tekstowe jest wstępnie wypełniane za pomocą unikatowej automatycznie generowanej nazwy. Nazwa odpowiada nazwie użytkownika w adresie e-mail, po którym następuje unikatowy numer 3-cyfrowy. Ta funkcja umożliwia zaoszczędzenie czasu na pomyślną nazwę komputera i wpisanie go przy każdym utworzeniu maszyny. Jeśli chcesz, możesz przesłonić to pole automatycznie wypełnione wybranym przez Ciebie nazwą. Aby zastąpić automatycznie wypełnioną nazwę maszyny wirtualnej, wprowadź nazwę w polu tekstowym **Nazwa maszyny wirtualnej** .
    2. Wprowadź **nazwę użytkownika** , który ma przyznane uprawnienia administratora na maszynie wirtualnej. **Nazwa użytkownika** dla maszyny jest wstępnie wypełniona unikatową automatycznie wygenerowaną nazwą. Nazwa odpowiada nazwie użytkownika w adresie e-mail. Ta funkcja umożliwia zaoszczędzenie czasu na podjęcie decyzji dotyczącej nazwy użytkownika za każdym razem, gdy tworzysz nową maszynę. Jeśli chcesz, możesz przesłonić to pole automatycznie wypełnione przez wybraną nazwę użytkownika. Aby zastąpić automatycznie wypełnioną wartość dla nazwy użytkownika, wprowadź wartość w polu tekstowym **Nazwa użytkownika** . Ten użytkownik ma uprawnienia **administratora** na maszynie wirtualnej.
    3. Jeśli tworzysz pierwszą maszynę wirtualną w laboratorium, wprowadź **hasło** dla użytkownika. Aby zapisać to hasło jako domyślne hasło w magazynie kluczy Azure skojarzonym z laboratorium, wybierz opcję **Zapisz jako hasło domyślne**. Domyślne hasło jest zapisywane w magazynie kluczy o nazwie: **VmPassword**. Gdy próbujesz utworzyć kolejne maszyny wirtualne w laboratorium, **VmPassword** jest automatycznie wybierane dla **hasła**. Aby zastąpić wartość, wyczyść pole wyboru **Użyj zapisanego wpisu tajnego** , a następnie wprowadź hasło.

        ![Zrzut ekranu przedstawiający stronę "Tworzenie zasobu laboratorium" z wybraną opcją "Ustawienia podstawowe" i wprowadzone wartości "Ustawienia użytkownika".](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Możesz również najpierw zapisać wpisy tajne w magazynie kluczy, a następnie użyć go podczas tworzenia maszyny wirtualnej w laboratorium. Aby uzyskać więcej informacji, zobacz [zapisywanie wpisów tajnych w magazynie kluczy](devtest-lab-store-secrets-in-key-vault.md). Aby użyć hasła przechowywanego w magazynie kluczy, wybierz opcję **Użyj zapisanego klucza tajnego**i określ kluczową wartość odpowiadającą hasłu (hasło).
    4. W sekcji **więcej opcji** wybierz pozycję **Zmień rozmiar**. Wybierz jeden ze wstępnie zdefiniowanych elementów, które określają rdzenie procesora, rozmiar pamięci RAM i rozmiar dysku twardego maszyny wirtualnej do utworzenia.
    5. Wybierz pozycję **Dodaj lub Usuń artefakty**. Wybierz i skonfiguruj artefakty, które chcesz dodać do obrazu podstawowego.
    **Uwaga:** Jeśli dopiero zaczynasz DevTest Labs lub konfigurujesz artefakty, zapoznaj się z sekcją [Dodaj istniejący artefakt do maszyny wirtualnej](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) , a następnie wróć tutaj po zakończeniu.
2. Przejdź do karty **Ustawienia zaawansowane** u góry i wykonaj następujące czynności:
    1. Aby zmienić sieć wirtualną, w której znajduje się maszyna wirtualna, wybierz pozycję **Zmień**Sieć wirtualna.
    2. Aby zmienić podsieć, wybierz pozycję **Zmień podsieć**.
    3. Określ, czy adres IP maszyny wirtualnej jest **publiczny, prywatny czy udostępniony**.
    4. Aby automatycznie usunąć maszynę wirtualną, określ **datę i godzinę wygaśnięcia**.
    5. Aby można było przejąć maszynę wirtualną przez użytkownika laboratorium, wybierz opcję **tak** dla opcji **uczyń tę maszynę** .
    6. Określ liczbę **wystąpień maszyny wirtualnej** , które mają być dostępne dla użytkowników laboratorium.

        ![Wybieranie bazy](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Wybierz pozycję **Utwórz** , aby dodać określoną maszynę wirtualną do laboratorium.

   Na stronie laboratorium zostanie wyświetlony stan tworzenia maszyny wirtualnej — najpierw jako **Tworzenie**, a następnie **uruchomiona** po uruchomieniu maszyny wirtualnej.

    ![Stan tworzenia maszyny wirtualnej](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Dodawanie istniejącego artefaktu do maszyny wirtualnej
Podczas tworzenia maszyny wirtualnej można dodać istniejące artefakty. Każde laboratorium obejmuje artefakty z repozytorium publicznych artefaktów DevTest Labs, a także artefakty, które zostały utworzone i dodane do własnego repozytorium artefaktów.

* Azure DevTest Labs *artefakty* umożliwiają określanie *akcji* wykonywanych podczas aprowizacji maszyny wirtualnej, takich jak uruchamianie skryptów programu Windows PowerShell, uruchamianie poleceń bash i instalowanie oprogramowania.
* *Parametry* artefaktów umożliwiają dostosowanie artefaktu dla konkretnego scenariusza

Aby dowiedzieć się, jak tworzyć artefakty, zapoznaj się z artykułem [dotyczącym tworzenia własnych artefaktów do użycia z DevTest Labs](devtest-lab-artifact-author.md).

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz laboratorium zawierające maszynę wirtualną, której chcesz użyć.
1. Wybierz pozycję **moje maszyny wirtualne**.
1. Wybierz odpowiednią maszynę wirtualną.
1. Wybierz pozycję **Zarządzaj artefaktami**.
1. Wybierz pozycję **Zastosuj artefakty**.
1. W okienku **Zastosuj artefakty** wybierz artefakt, który chcesz dodać do maszyny wirtualnej.
1. W okienku **Dodawanie artefaktu** wprowadź wymagane wartości parametrów i wszelkie parametry opcjonalne, które są potrzebne.
1. Wybierz pozycję **Dodaj** , aby dodać artefakt i wrócić do okienka **stosowanie artefaktów** .
1. Kontynuuj dodawanie artefaktów zgodnie z potrzebami dla maszyny wirtualnej.
1. Po dodaniu artefaktów możesz [zmienić kolejność, w której są uruchamiane artefakty](#change-the-order-in-which-artifacts-are-run). Możesz również wrócić do [wyświetlania lub modyfikowania artefaktu](#view-or-modify-an-artifact).
1. Po zakończeniu dodawania artefaktów wybierz pozycję **Zastosuj** .

## <a name="change-the-order-in-which-artifacts-are-run"></a>Zmień kolejność, w której są uruchamiane artefakty
Domyślnie działania artefaktów są wykonywane w kolejności, w jakiej są dodawane do maszyny wirtualnej.
Poniższe kroki ilustrują sposób zmiany kolejności, w której są uruchamiane artefakty.

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny wirtualnej.

    ![Liczba artefaktów dodanych do maszyny wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **wybrane artefakty** przeciągnij i upuść artefakty do żądanej kolejności. **Uwaga:** Jeśli masz problem z przeciąganiem artefaktu, upewnij się, że przeciągniesz od lewej strony artefaktu.
1. Po zakończeniu wybierz przycisk **OK**.

## <a name="view-or-modify-an-artifact"></a>Wyświetlanie lub modyfikowanie artefaktu
Poniższe kroki ilustrują sposób wyświetlania lub modyfikowania parametrów artefaktu:

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny wirtualnej.

    ![Liczba artefaktów dodanych do maszyny wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **wybrane artefakty** wybierz artefakt, który chcesz wyświetlić lub edytować.
1. W okienku **Dodaj artefakt** Wprowadź wszelkie potrzebne zmiany, a następnie wybierz **przycisk OK** , aby zamknąć okienko **Dodawanie artefaktu** .
1. Wybierz **przycisk OK** , aby zamknąć **wybrane okienko artefaktów** .

## <a name="save-azure-resource-manager-template"></a>Zapisz szablon Azure Resource Manager
Szablon Azure Resource Manager zapewnia deklaratywny sposób definiowania powtarzalnego wdrożenia.
Poniższe kroki wyjaśniają, jak zapisać szablon Azure Resource Manager dla tworzonej maszyny wirtualnej.
Po zapisaniu można użyć szablonu Azure Resource Manager, aby [wdrożyć nowe maszyny wirtualne z Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. W okienku **maszyna wirtualna** wybierz pozycję **Wyświetl Azure Resource Manager szablon**.
2. W okienku **widok Azure Resource Manager szablonu** zaznacz tekst szablonu.
3. Skopiuj zaznaczony tekst do Schowka.
4. Wybierz **przycisk OK** , aby zamknąć **okienko widok Azure Resource Manager szablon**.
5. Otwórz edytor tekstów.
6. Wklej tekst szablonu ze schowka.
7. Zapisz plik do późniejszego użycia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Po utworzeniu maszyny wirtualnej można nawiązać połączenie z maszyną wirtualną, wybierając pozycję **Połącz** w okienku maszyny wirtualnej.
* Dowiedz się, jak [tworzyć niestandardowe artefakty dla maszyny wirtualnej z DevTest Labs](devtest-lab-artifact-author.md).
* Zapoznaj się z [galerią szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
