---
title: Śledzenie użycia laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku omówiono śledzenie użycia laboratorium przez twórcę/właściciela laboratorium.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: a9a9b49b568decc621be1969a8578d61ac7e4861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445036"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Samouczek: śledzenie użycia laboratorium w usłudze Azure Lab Service
W tym samouczku zostanie pokazane, w jaki sposób twórca/właściciel laboratorium może śledzić użycie laboratorium.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Wyświetlanie użytkowników zarejestrowanych w Twoim laboratorium
> * Wyświetlanie użycia maszyn wirtualnych w laboratorium
> * Zarządzanie maszynami wirtualnymi uczniów 


## <a name="view-registered-users"></a>Wyświetl zarejestrowanych użytkowników

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft.
3. Na stronie **My labs** (Moje laboratoria) wybierz laboratorium, którego użycie chcesz śledzić. 
4. Wybierz kafelek **Users** (Użytkownicy) lub pozycję **Users** (Użytkownicy) w menu po lewej stronie. Zostanie wyświetlona lista uczniów, którzy zarejestrowali się w Twoim laboratorium.  

    ![Zarejestrowani użytkownicy](./media/tutorial-track-usage/registered-users.png)

    Aby uzyskać więcej informacji na temat dodawania użytkowników do laboratorium i zarządzania nimi, zobacz [Dodawanie użytkowników laboratorium i zarządzanie nimi](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Wyświetlanie użycia maszyn wirtualnych

1. Wybierz pozycję **Virtual Machines** (Maszyny wirtualne) w menu po lewej stronie. 
2. Upewnij się, że jest wyświetlany stan maszyn wirtualnych i liczba godzin, podczas których maszyny wirtualne były uruchomione. Czas, który właściciel laboratorium spędza na maszynie wirtualnej ucznia, nie jest wliczany do czasu użycia widocznego w ostatniej kolumnie. 

    ![Użycie maszyny wirtualnej](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Zarządzanie maszynami wirtualnymi uczniów 
Na tej stronie można uruchamiać, zatrzymywać lub resetować maszyny wirtualne uczniów przy użyciu kontrolek w kolumnie **stan** lub na pasku narzędzi.

![Akcje maszyny wirtualnej](./media/tutorial-track-usage/vm-controls.png)

Aby uzyskać więcej informacji o zarządzaniu pulą maszyn wirtualnych dla laboratorium, zobacz [Konfigurowanie puli maszyn wirtualnych i zarządzanie nią](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Gdy nauczycieli włącza maszynę wirtualną ucznia, nie ma to żadnego limitu przydziału dla ucznia. Przydział dla użytkownika określa liczbę godzin laboratorium dostępnych dla użytkownika poza zaplanowanym czasem klasy. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat laboratoriów na potrzeby zajęć, zobacz artykuły w sekcji [Przewodniki z instrukcjami](how-to-manage-lab-accounts.md).
