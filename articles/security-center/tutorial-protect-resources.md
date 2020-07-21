---
title: Samouczek dotyczący kontroli dostępu & aplikacji — Azure Security Center
description: W tym samouczku przedstawiono sposób konfigurowania zasad dostępu just in Time do maszyny wirtualnej oraz zasad kontroli aplikacji.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 3e4404589e180be730579b8cbbfadd132502585a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529322"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Samouczek: Ochrona zasobów przy użyciu usługi Azure Security Center
Usługa Security Center ogranicza narażenia na zagrożenia poprzez zastosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań. Dostęp do maszyn wirtualnych just-in-Time (JIT) zmniejsza narażenie na ataki przez umożliwienie trwałego dostępu do maszyn wirtualnych. Zamiast tego możesz zapewnić kontrolowany, monitorowany dostęp do maszyn wirtualnych tylko w razie potrzeby. Adaptacyjne kontrole aplikacji pomagają zabezpieczać maszyny wirtualne przed złośliwym oprogramowaniem poprzez kontrolowanie aplikacji, które mogą być uruchamiane na maszynach wirtualnych. Usługa Security Center analizuje procesy uruchomione na maszynie wirtualnej przy użyciu uczenia maszynowego i za pomocą tej analizy ułatwia zastosowanie listy reguł elementów dozwolonych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej
> * Konfigurowanie zasad kontroli aplikacji

## <a name="prerequisites"></a>Wymagania wstępne
Aby przekroczyć funkcje omówione w tym samouczku, musisz mieć Security Center warstwy cenowej standardowa. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](security-center-get-started.md) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

## <a name="manage-vm-access"></a>Zarządzanie dostępem do maszyny wirtualnej
Dostęp typu „ just in time” do maszyny wirtualnej może być używany do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure w celu zmniejszenia narażenia na ataki przy zapewnieniu łatwego dostępu do maszyn wirtualnych na potrzeby łączenia się z nimi w razie potrzeby.

Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, np. aby wykonać zadania związane z zarządzaniem lub konserwacją. Gdy jest włączona funkcja just-in-Time, Security Center używa reguł grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń), które ograniczają dostęp do portów zarządzania, przez co nie mogą być objęte przez osoby atakujące.

1. W menu głównym Security Center wybierz **dostęp just in Time do maszyny wirtualnej** w obszarze **Zaawansowana ochrona w chmurze**.

   ![Dostęp just in time do maszyny wirtualnej][1]

   **Dostęp just in Time do maszyny wirtualnej** zapewnia informacje o stanie maszyn wirtualnych:

   - **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu just in Time do maszyny wirtualnej.
   - **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyny wirtualnej just-in-Time, ale nie zostały skonfigurowane do programu.
   - **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:

     - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in Time wymaga, aby sieciowej grupy zabezpieczeń.
     - Klasyczna maszyna wirtualna — Security Center dostęp do maszyny wirtualnej just in Time aktualnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager.
     - Inne — maszyna wirtualna jest w tej kategorii, jeśli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub że maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciowej grupy zabezpieczeń.

2. Wybierz zalecaną maszynę wirtualną, a następnie kliknij pozycję **Włącz JIT na 1 maszynie wirtualnej** , aby skonfigurować zasady just in Time dla tej maszyny wirtualnej:

   Można zapisać porty domyślne, które Security Center zalecane, lub dodać i skonfigurować nowy port, na którym chcesz włączyć rozwiązanie just in Time. W tym samouczku dodamy port, używając opcji **Dodaj**.

   ![Dodawanie konfiguracji portu][2]

3. W obszarze **Dodawanie konfiguracji portu** należy określić:

   - Numer portu
   - Typ protokołu
   - Dozwolone źródłowe adresy IP — zakresy adresów IP, które mogą uzyskać dostęp po zatwierdzonym żądaniu
   - Maksymalny czas żądania — maksymalny przedział czasu, w którym dany port może być otwarty

4. Wybierz opcję **OK**, aby zapisać.

## <a name="harden-vms-against-malware"></a>Ochrona maszyn wirtualnych przed złośliwym oprogramowaniem
Funkcje adaptacyjnego sterowania aplikacjami ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach zasobów, co poza innymi korzyściami zabezpiecza maszyny wirtualne przed złośliwym oprogramowaniem. Usługa Security Center analizuje procesy uruchomione na maszynie wirtualnej przy użyciu uczenia maszynowego i za pomocą tej analizy ułatwia zastosowanie listy reguł elementów dozwolonych.

1. Wróć do menu głównego usługi Security Center. W obszarze **ZAAWANSOWANA OCHRONA CHMURY** wybierz opcję **Adaptacyjne kontrole aplikacji**.

   ![Funkcje adaptacyjnego sterowania aplikacjami][3]

   Sekcja **Grupy zasobów** zawiera trzy karty:

   - **Skonfigurowane**: lista grup zasobów zawierających maszyny wirtualne, dla których skonfigurowano sterowanie aplikacjami.
   - **Zalecane**: lista grup zasobów, dla których zaleca się sterowanie aplikacjami.
   - **Brak zaleceń**: lista grup zasobów zawierających maszyny wirtualne bez żadnych zaleceń dotyczących sterowania aplikacjami. Na przykład maszyny wirtualne, na których aplikacje stale się zmieniają i nie osiągają stanu stabilnego.

2. Wybierz kartę **Zalecane**, aby uzyskać listę grup zasobów z zaleceniami dotyczącymi sterowania aplikacjami.

   ![Zalecenia dotyczące kontroli aplikacji][4]

3. Wybierz grupę zasobów, aby otworzyć opcję **Utwórz reguły sterowania aplikacjami**. W obszarze **Wybierz maszyny wirtualne** zapoznaj się z listą zalecanych maszyn wirtualnych i wyczyść zaznaczenie pól wyboru obok tych, do których nie ma być stosowane sterowanie aplikacjami. W obszarze **Wybierz procesy dla reguł umieszczania na białej liście** zapoznaj się z listą zalecanych aplikacji i wyczyść zaznaczenie pól wyboru obok tych, do których nie ma być stosowana ta funkcja. Lista zawiera następujące pozycje:

   - **NAZWA**: pełna ścieżka aplikacji
   - **PROCESY**: ile aplikacji znajduje się w każdej ścieżce
   - **Typowy**: wartość "tak" wskazuje, że te procesy zostały wykonane na większości maszyn wirtualnych w tej grupie zasobów
   - Możliwe do **wykorzystania**: ikona ostrzeżenia wskazuje, czy można użyć aplikacji przez osobę atakującą do obejścia listy dozwolonych aplikacji. Zaleca się dokonanie przeglądu tych aplikacji przed ich zatwierdzeniem.

4. Po wybraniu opcji wybierz przycisk **Utwórz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Kontynuuj uruchamianie warstwy Standardowa i Włącz automatyczną obsługę administracyjną. Jeśli nie zamierzasz kontynuować lub chcesz wrócić do warstwy bezpłatnej:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję lub zasady, którym chcesz przywrócić warstwę bezpłatną. Zostanie otwarte okno **Zasady zabezpieczeń**.
3. W obszarze **SKŁADNIKI ZASAD** wybierz pozycję **Warstwa cenowa**.
4. Wybierz opcję **bezpłatna** , aby zmienić subskrypcję z warstwy Standardowa na warstwę bezpłatna.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego Security Center i wybierz pozycję **zasady zabezpieczeń**.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. W sekcji **Zasady zabezpieczeń — zbieranie danych** wybierz pozycję **Wyłącz** w obszarze **Dołączanie**, aby wyłączyć automatyczną aprowizację.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono sposób ograniczania narażenia na zagrożenia poprzez:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej w celu zapewnienia kontroli dostępu do maszyn wirtualnych i przeprowadzania na nich inspekcji tylko w razie potrzeby
> * konfigurowanie zasad adaptacyjnych kontroli aplikacji w celu kontrolowania aplikacji, które mogą być uruchamiane na maszynach wirtualnych.

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat reagowania na przypadki naruszenia zabezpieczeń.

> [!div class="nextstepaction"]
> [Samouczek: Reagowanie na zdarzenia związane z zabezpieczeniami](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
