---
title: Dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku maszyny wirtualne są dostępne w laboratorium skonfigurowanym przez nauczyciela.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77134035"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Samouczek: dostęp do laboratorium w usłudze Azure Lab Services
W tym samouczku Ty, jako osoba ucząca się, nawiążesz połączenie z maszyną wirtualną w laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Zarejestruj się w laboratorium
> * Uruchamianie maszyny wirtualnej
> * Łączenie z maszyną wirtualną

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 

    > [!NOTE]
    > Do korzystania z Azure Lab Services jest wymagany konto Microsoft. Jeśli próbujesz zalogować się do portalu przy użyciu innych konto Microsoft, takich jak Yahoo lub konta Google, postępuj zgodnie z instrukcjami, aby utworzyć konto Microsoft, który zostanie połączony z niekonto Microsoft. Następnie postępuj zgodnie z instrukcjami, aby ukończyć proces rejestracji. 
1. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
1. Zaczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny wirtualnej Zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zostanie wyświetlona ikona reprezentująca **system operacyjny (OS)** maszyny wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Pasek postępu na kafelku pokazuje liczbę godzin użycia w odniesieniu do liczby godzin [przypisanych](how-to-configure-student-usage.md#set-quotas-for-users) do użytkownika. Ten czas jest dodatkowym czasem przydzielonym do czasu zaplanowanym dla laboratorium. 
    1. Zobaczysz ikonę/przyciski w dolnej części kafelka, aby uruchomić/zatrzymać maszynę wirtualną, a następnie nawiązać połączenie z maszyną wirtualną. 
    1. Na prawo od przycisków zobaczysz stan maszyny wirtualnej. Upewnij się, że stan maszyny wirtualnej jest **zatrzymany**. 

        ![Maszyna wirtualna w stanie zatrzymania](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej
1. **Uruchom** maszynę wirtualną, wybierając pierwszy przycisk, jak pokazano na poniższej ilustracji. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Upewnij się, że stan maszyny wirtualnej to **uruchomiono**. 

    ![Maszyna wirtualna w stanie uruchomienia](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Zauważ, że ikona pierwszego przycisku została zmieniona, aby reprezentować operację **zatrzymania** . Możesz wybrać ten przycisk, aby zatrzymać maszynę wirtualną. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz drugi przycisk, jak pokazano na poniższej ilustracji, aby **nawiązać połączenie** z maszyną wirtualną laboratorium. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. W przypadku maszyn wirtualnych z **systemem Windows** Zapisz plik **RDP** na dysku twardym. Otwórz plik RDP, aby nawiązać połączenie z maszyną wirtualną. Zaloguj się na komputerze przy użyciu **nazwy użytkownika** i **hasła** uzyskanych z nauczycieli/profesor. 
    3. W przypadku maszyn wirtualnych z **systemem Linux** można użyć protokołu **SSH** lub **RDP** (jeśli jest włączony), aby połączyć się z nimi. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto linku rejestracji otrzymanego od nauczyciela w celu uzyskania dostępu do laboratorium.

Jako właściciel laboratorium możesz sprawdzać, kto zarejestrował się w Twoim laboratorium, i śledzić użycie maszyn wirtualnych. Przejdź do następnego samouczka, aby dowiedzieć się, jak śledzić użycie laboratorium:

> [!div class="nextstepaction"]
> [Śledzenie użycia laboratorium](tutorial-track-usage.md) 
