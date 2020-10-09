---
title: Tworzenie laboratorium przy użyciu usługi Azure DevTest Labs | Microsoft Docs
description: W tym samouczku utworzysz laboratorium w Azure DevTest Labs przy użyciu Azure Portal. Administrator laboratorium konfiguruje laboratorium, tworzy maszyny wirtualne w laboratorium i konfiguruje zasady.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 4b59edd4ab1204d6683cf95a02070d10b1abe061
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324284"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Samouczek: konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs
W tym samouczku utworzysz laboratorium za pomocą witryny Azure Portal. Administrator laboratorium konfiguruje laboratorium w organizacji, tworzy maszyny wirtualne w laboratorium i konfiguruje zasady. Użytkownicy laboratorium (na przykład deweloper i testerzy) przejmują maszyny wirtualne w laboratorium, nawiązują połączenie z nimi i korzystają z nich. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium
> * Dodawanie maszyn wirtualnych do laboratorium
> * Dodawanie użytkownika do roli użytkownika laboratorium

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-lab"></a>Tworzenie laboratorium
Następujące kroki ilustrują tworzenie laboratorium w usłudze Azure DevTest Labs przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu głównego po lewej stronie wybierz pozycję **Utwórz zasób** (na początku listy), wskaż pozycję **Narzędzia deweloperskie** i kliknij pozycję **DevTest Labs**. 

    ![Menu nowego laboratorium DevTest Labs](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. W oknie **Tworzenie laboratorium DevTest Lab** wykonaj następujące czynności: 
    1. W polu **Nazwa laboratorium** wprowadź nazwę laboratorium. 
    2. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć laboratorium. 
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów. 
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których ma zostać utworzone laboratorium. 
    5. Wybierz przycisk **Utwórz**. 
    6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. Utworzone laboratorium zostanie wyświetlone na pulpicie nawigacyjnym. 

        ![Tworzenie sekcji laboratorium usługi DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. Upewnij się, że laboratorium zostało utworzone pomyślnie, sprawdzając powiadomienia. Wybierz pozycję **Przejdź do zasobu**.  

    ![Powiadomienie](./media/tutorial-create-custom-lab/creation-notification.png)
3. Upewnij się, że strona **laboratorium DevTest Lab** dla Twojego laboratorium jest widoczna. 

    ![Strona główna laboratorium](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Dodawanie maszyny wirtualnej do laboratorium

1. Na stronie **Laboratorium DevTest Lab** wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    ![Przycisk dodawania](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na stronie **Wybieranie bazy** wyszukaj słowo kluczowe (na przykład: Windows, Ubuntu) i wybierz jeden z obrazów podstawowych z listy. 
1. Na stronie **Maszyna wirtualna** wykonaj następujące czynności: 
    1. W polu **Nazwa maszyny wirtualnej** wprowadź nazwę maszyny wirtualnej. 
    2. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika, który ma dostęp do maszyny wirtualnej. 
    3. W polu **Hasło** wprowadź hasło użytkownika. 

        ![Zrzut ekranu przedstawiający podstawowe ustawienia strony "Tworzenie zasobu laboratorium".](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Wybierz kartę **Ustawienia zaawansowane**.
    1. W obszarze **Oznacz tę maszynę jako możliwą do przejęcia** wybierz pozycję **Tak**.
    2. Upewnij się, że w polu **Liczba wystąpień** ustawiono wartość **1**. Ustawienie wartości **2** spowoduje utworzenie 2 maszyn wirtualnych o następujących nazwach: `<base image name>00' and <base image name>01`. Przykład: `win10vm00` i `win10vm01`.     
    3. Wybierz pozycję **Prześlij**. 

        ![Wybieranie bazy](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. Stan maszyny wirtualnej jest wyświetlany na liście **Maszyny wirtualne możliwe do przejęcia**. Tworzenie maszyny wirtualnej może potrwać około 25 minut. Maszyna wirtualna jest tworzona w oddzielnej grupie zasobów platformy Azure, której nazwa rozpoczyna się od nazwy bieżącej grupy zasobów zawierającej laboratorium. Jeśli na przykład laboratorium znajduje się w grupie `labrg`, maszyna wirtualna może zostać utworzona w grupie zasobów `labrg3988722144002`. 

        ![Stan tworzenia maszyny wirtualnej](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Utworzona maszyna wirtualna jest wyświetlana na liście **Maszyny wirtualne możliwe do przejęcia**. 

    > [!NOTE] 
    > Na stronie **Ustawienia zaawansowane** można skonfigurować publiczny, prywatny lub współużytkowany adres IP dla maszyny wirtualnej. Gdy opcja **współużytkowanego adresu IP** jest włączona, usługa Azure DevTest Labs automatycznie włącza protokół RDP dla maszyn wirtualnych z systemem Windows oraz protokół SSH dla maszyn wirtualnych z systemem Linux. Jeśli tworzysz maszyny wirtualne z **publicznym adresem IP**, protokoły RDP i SSH są włączone bez wprowadzania żadnych zmian w usłudze DevTest Labs.  

## <a name="add-a-user-to-the-lab-user-role"></a>Dodawanie użytkownika do roli użytkownika laboratorium

1. Z menu po lewej stronie wybierz pozycję **Konfiguracja i zasady**. 

    ![Konfiguracja i zasady](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Z menu wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)**, a następnie wybierz pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Dodaj przypisanie roli — przycisk](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Na stronie **Dodawanie uprawnień** wykonaj następujące czynności:
    1. W obszarze **Rola** wybierz pozycję **Użytkownik usługi DevTest Labs**. 
    2. Wybierz **użytkownika**, którego chcesz dodać. 
    3. Wybierz pozycję **Zapisz**.

        ![Dodaj użytkownika](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W następnym samouczku pokazano, jak użytkownik laboratorium może przejąć maszynę wirtualną w laboratorium i nawiązać z nią połączenie. Jeśli nie chcesz korzystać z tamtego samouczka i chcesz oczyścić zasoby utworzone w ramach tego samouczka, wykonaj następujące czynności: 

1. W witrynie Azure Portal wybierz z menu pozycję **Grupy zasobów**. 

    ![Grupy zasobów](./media/tutorial-create-custom-lab/resource-groups.png)
1. Wybierz grupę zasobów, w której utworzono laboratorium. 
1. Wybierz pozycję **Usuń grupę zasobów** z paska narzędzi. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie, łącznie z laboratorium. 

    ![Grupa zasobów laboratorium](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Powtórz te czynności, aby usunąć dodatkową utworzoną grupę zasobów o nazwie `<your resource group name><random numbers>`. Przykład: `splab3988722144001`. Maszyny wirtualne są tworzone w tej grupie zasobów, a nie w grupie zasobów, w której istnieje laboratorium. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono laboratorium z maszyną wirtualną i udzielono użytkownikowi dostępu do laboratorium. Aby dowiedzieć się, jak uzyskać dostęp laboratorium jako użytkownik laboratorium, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: uzyskiwanie dostępu do laboratorium](tutorial-use-custom-lab.md)

