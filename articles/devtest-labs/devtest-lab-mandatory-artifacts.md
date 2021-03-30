---
title: Określ obowiązkowe artefakty dla Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak określić obowiązkowe artefakty, które muszą zostać zainstalowane przed zainstalowaniem artefaktów wybranych przez użytkownika na maszynach wirtualnych w środowisku laboratoryjnym.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480307"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Określ obowiązkowe artefakty dla laboratorium w Azure DevTest Labs
Jako właściciel laboratorium można określić obowiązkowe artefakty, które są stosowane do wszystkich maszyn utworzonych w laboratorium. Załóżmy, że każdy komputer w laboratorium ma być połączony z siecią firmową. W takim przypadku każdy użytkownik laboratorium będzie musiał dodać artefakt przyłączania do domeny podczas tworzenia maszyny wirtualnej, aby upewnić się, że ich maszyna jest podłączona do domeny firmowej. Innymi słowy, użytkownicy laboratorium muszą ponownie utworzyć maszynę w przypadku zapomnienia zastosowania obowiązkowych artefaktów na komputerze. Jako właściciel laboratorium utworzysz artefakt przyłączania do domeny jako obowiązkowy artefakt w laboratorium. Ten krok zapewnia, że każdy komputer jest połączony z siecią firmową i oszczędność czasu i nakładu pracy dla użytkowników laboratorium.
 
Inne obowiązkowe artefakty mogą obejmować powszechnie używane narzędzie, z których korzysta Twój zespół, lub pakiet zabezpieczeń związany z platformą, który domyślnie musi mieć każdy komputer. W krótkim przypadku każde typowe oprogramowanie, które musi być w laboratorium, stać się obligatoryjnym artefaktem. W przypadku utworzenia obrazu niestandardowego z komputera, który ma zastosowane obowiązkowe artefakty, a następnie utworzysz maszynę z tego obrazu, obowiązkowe artefakty są ponownie stosowane na komputerze podczas jego tworzenia. To zachowanie oznacza również, że mimo że obraz niestandardowy jest stary, za każdym razem, gdy tworzysz maszynę z niej najbardziej zaktualizowaną wersję obowiązkowych artefaktów stosuje się do niej podczas tworzenia przepływu. 
 
Tylko artefakty, które nie mają parametrów są obsługiwane jako obowiązkowe. Użytkownik laboratorium nie musi wprowadzać dodatkowych parametrów podczas tworzenia laboratorium, co sprawia, że proces tworzenia maszyn wirtualnych jest prosty. 

## <a name="specify-mandatory-artifacts"></a>Określanie obowiązkowych artefaktów
Obowiązkowe artefakty dla maszyn z systemem Windows i Linux można wybrać osobno. Można również zmienić kolejność tych artefaktów w zależności od kolejności, w jakiej mają być stosowane. 

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady** w obszarze **Ustawienia**. 
3. Wybierz **obowiązkowe artefakty** w obszarze **Zasoby zewnętrzne**. 
4. Wybierz pozycję **Edytuj** w sekcji **system Windows** lub **Linux** . W tym przykładzie została użyta opcja **systemu Windows** . 

    ![Strona obowiązkowe artefakty — przycisk Edytuj](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Wybierz artefakt. W tym przykładzie zostanie użyta opcja **7-zip** . 
5. Na stronie **Dodawanie artefaktu** wybierz pozycję **Dodaj**. 

    ![Wymagana Strona artefaktów — Dodaj 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Aby dodać kolejny artefakt, wybierz artykuł i wybierz pozycję **Dodaj**. Ten przykład dodaje **Chrome** jako drugi obowiązkowy artefakt.

    ![Strona obowiązkowych artefaktów — Dodawanie przeglądarki Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na stronie **obowiązkowe artefakty** zobaczysz komunikat informujący o liczbie wybranych artefaktów. Jeśli klikniesz komunikat, zobaczysz wybrane artefakty. Wybierz pozycję **Zapisz** , aby zapisać. 

    ![Wymagana Strona artefaktów — Zapisz artefakty](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Powtórz kroki, aby określić obowiązkowe artefakty dla maszyn wirtualnych z systemem Linux. 
    
    ![Strona obowiązkowych artefaktów — artefakty systemów Windows i Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Aby **usunąć** artefakt z listy, wybierz pozycję **... (wielokropek)** na końcu wiersza, a następnie wybierz pozycję **Usuń**. 
10. Aby **zmienić kolejność** artefaktów na liście, umieść kursor myszy nad artefaktem, wybierz pozycję **... (wielokropek)** , który jest wyświetlany na początku wiersza, i przeciągnij element do nowej pozycji. 
11. Aby zapisać obowiązkowe artefakty w laboratorium, wybierz pozycję **Zapisz**. 

    ![Wymagana Strona artefaktów — Zapisz artefakty w laboratorium](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zamknij stronę **Konfiguracja i zasady** (wybierz pozycję **X** w prawym górnym rogu), aby wrócić do strony głównej dla laboratorium.  

## <a name="delete-a-mandatory-artifact"></a>Usuwanie obowiązkowego artefaktu
Aby usunąć obowiązkowy artefakt z laboratorium, wykonaj następujące czynności: 

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja i zasady** . 
2. Wybierz **obowiązkowe artefakty** w obszarze **Zasoby zewnętrzne**. 
3. Wybierz pozycję **Edytuj** w sekcji **system Windows** lub **Linux** . W tym przykładzie została użyta opcja **systemu Windows** . 
4. Wybierz komunikat z liczbą obowiązkowych artefaktów u góry. 

    ![Strona obowiązkowe artefakty — wybierz komunikat](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na stronie **wybrane artefakty** wybierz pozycję **... (wielokropek)** dla artefaktu, który ma zostać usunięty, a następnie wybierz pozycję **Usuń**. 
    
    ![Strona obowiązkowych artefaktów — usuwanie artefaktu](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Wybierz **przycisk OK** , aby zamknąć **wybraną stronę artefaktów** . 
7. Wybierz pozycję **Zapisz** na stronie **obowiązkowe artefakty** .
8. W razie potrzeby powtórz kroki dla obrazów systemu **Linux** . 
9. Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w laboratorium. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Wyświetl obowiązkowe artefakty podczas tworzenia maszyny wirtualnej
Teraz jako użytkownik laboratorium można wyświetlić listę obowiązkowych artefaktów podczas tworzenia maszyny wirtualnej w laboratorium. Nie można edytować ani usunąć obowiązkowych artefaktów ustawionych w laboratorium przez właściciela laboratorium.

1. Na stronie głównej laboratorium wybierz pozycję **Przegląd** z menu.
2. Aby dodać maszynę wirtualną do laboratorium, wybierz pozycję **+ Dodaj**. 
3. Wybierz **obraz podstawowy**. W tym przykładzie zastosowano **system Windows Server w wersji 1709**.
4. Zauważ, że zobaczysz komunikat dotyczący **artefaktów** z wybraną liczbą wymaganych artefaktów. 
5. Wybierz pozycję **artefakty**. 
6. Upewnij się, że są widoczne **obowiązkowe artefakty** określone w konfiguracji i zasadach laboratorium. 

    ![Tworzenie maszyny wirtualnej — obligatoryjne artefakty](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium artefaktów git do laboratorium](devtest-lab-add-artifact-repo.md).

