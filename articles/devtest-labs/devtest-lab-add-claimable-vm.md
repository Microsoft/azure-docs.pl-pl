---
title: Twórz i Zarządzaj maszynami wirtualnymi z możliwością domagania w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal dodać maszynę wirtualną z możliwością domaganą w Azure DevTest Labs i wyświetlić procesy, które należy wykonać, aby zgłosić/odrościć maszynę wirtualną.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270983"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Twórz i Zarządzaj maszynami wirtualnymi z możliwością domagania w Azure DevTest Labs
Możesz dodać maszynę wirtualną z możliwością przechodzenia do laboratorium w podobny sposób, aby [dodać standardową maszynę wirtualną](devtest-lab-add-vm.md) — od *podstawowej* , która jest [obrazem niestandardowym](devtest-lab-create-template.md), [formułą](devtest-lab-manage-formulas.md)lub [obrazem witryny Marketplace](devtest-lab-configure-marketplace-images.md). Ten samouczek przeprowadzi Cię przez proces używania Azure Portal, aby dodać maszynę wirtualną do laboratorium w środowisku DevTest Labs i wyświetlić procesy, do których użytkownik może zatwierdzić i odrościć maszynę wirtualną.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki umożliwiające dodanie maszyny wirtualnej z zastrzeżeniem do laboratorium w Azure DevTest Labs
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
3. Wybierz pozycję **Utwórz** , aby dodać określoną maszynę wirtualną do laboratorium.

   Na stronie laboratorium zostanie wyświetlony stan tworzenia maszyny wirtualnej — najpierw jako **Tworzenie**, a następnie **uruchomiona** po uruchomieniu maszyny wirtualnej.

> [!NOTE]
> W przypadku wdrażania maszyn wirtualnych laboratoryjnych za pomocą [szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md)można utworzyć możliwe do zaewidencjonowania maszyny wirtualne, ustawiając właściwość **allowClaim** na wartość true w sekcji Właściwości.


## <a name="using-a-claimable-vm"></a>Używanie maszyny wirtualnej do zajmowania

Użytkownik może przejąć dowolną MASZYNę wirtualną z listy "maszyn wirtualnych" objętych zakresem, wykonując jedną z następujących czynności:

* Z listy "maszyny wirtualne do zaewidencjonowania" w dolnej części okienka "przegląd" laboratorium, kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych na liście i wybierz polecenie **Przejmij komputer**.

  ![Poproś o konkretną maszynę wirtualną, której dotyczy żądanie.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* W górnej części okienka "przegląd" Wybierz pozycję **Przejmij dowolne**. Losowa maszyna wirtualna jest przypisana z listy maszyn wirtualnych z zakresem.

  ![Zażądaj dowolnych maszyn wirtualnych.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Po potwierdzeniu przez użytkownika maszyny wirtualnej DevTest Labs uruchomi maszynę i przeniesie ją do listy "moje maszyny wirtualne" użytkownika laboratorium. Oznacza to, że użytkownik laboratorium będzie miał teraz uprawnienia właściciela na tym komputerze. Czas wymagany na ten krok może się różnić w zależności od czasu uruchomienia, a także innych akcji niestandardowych wykonywanych podczas zdarzenia roszczeń. Po zarejestrowaniu komputer nie jest już dostępny w puli możliwej do zastrzeżenia.  

## <a name="unclaim-a-vm"></a>Odbierz maszynę wirtualną

Gdy użytkownik zakończył korzystanie z przejętej maszyny wirtualnej i chce udostępnić ją innym osobom, może zwrócić zażądaną MASZYNę wirtualną na listę maszyn wirtualnych z możliwością docelową, wykonując jedną z następujących czynności:

- Z listy "moje maszyny wirtualne" kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych na liście — lub wybierz jej wielokropek (...), a następnie wybierz pozycję **Anuluj.**

  ![Odbierz maszynę wirtualną na liście maszyn wirtualnych.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na liście "moje maszyny wirtualne" Wybierz MASZYNę wirtualną, aby otworzyć jej okienko zarządzania, a następnie wybierz **pozycję Usuń z** górnego paska menu.

  ![Odbierz maszynę wirtualną w okienku zarządzania maszyną wirtualną.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Gdy użytkownik odrzuca maszynę wirtualną, nie ma już uprawnień właściciela dla danej maszyny wirtualnej laboratorium i jest dostępna do żądania przez dowolnego innego użytkownika laboratorium w stanie, w którym został zwrócony do puli. 

### <a name="transferring-the-data-disk"></a>Transferowanie dysku z danymi
Jeśli do możliwej do przydzielenia maszyny wirtualnej jest dołączony dysk z danymi, a użytkownik nie wybrał oświadczenia, dysk danych pozostaje w maszynie wirtualnej. Gdy inny użytkownik będzie następnie zgłaszać tę maszynę wirtualną, ten nowy użytkownik przejmuje dysk danych oraz maszynę wirtualną.

Jest to nazywane "transferem dysku danych". Dysk danych jest następnie dostępny na liście nowych użytkowników na **dyskach danych** , które mają być zarządzane przez program.

![Odbierz dyski danych.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Następne kroki
* Po utworzeniu można nawiązać połączenie z maszyną wirtualną, wybierając pozycję **Połącz** w jej okienku zarządzania.
* Zapoznaj się z [galerią szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
