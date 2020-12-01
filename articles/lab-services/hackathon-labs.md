---
title: Użyj Azure Lab Services dla hackathon
description: W tym artykule opisano, jak używać Azure Lab Services do tworzenia laboratoriów, których można użyć do uruchamiania imprezy rozpoczynają.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 561351636a37c2cd5c3070dbfef2a3122e5c29b0
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434264"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Użyj Azure Lab Services dla następnej hackathon
Azure Lab Services zaprojektowano jako uproszczone i łatwe w użyciu, dzięki czemu możesz szybko uruchomić nowe laboratorium maszyn wirtualnych dla hackathon.  Użyj poniższej listy kontrolnej, aby upewnić się, że hackathon jest tak płynnie, jak to możliwe. Ta lista kontrolna powinna zostać wykonana przez dział IT lub wykładowców odpowiedzialnych za tworzenie i zarządzanie laboratorium hackathon. 

Aby korzystać z usług Lab Services dla hackathon, upewnij się, że oba konta laboratorium i laboratorium są tworzone co najmniej kilka dni przed rozpoczęciem hackathon. Ponadto postępuj zgodnie z poniższymi wskazówkami:

## <a name="guidance"></a>Wskazówki

- **Utwórz laboratorium w regionie lub miejscu, które jest najbliższe uczestnikom**. 

    Aby zmniejszyć opóźnienie, Utwórz laboratorium w regionie, który znajduje się najbliżej Twoich uczestników hackathon.  Jeśli uczestnicy znajdują się na całym świecie, musisz użyć najlepszego osądu, aby utworzyć laboratorium, które znajduje się centralnie.  Lub Podziel hackathon na używanie wielu laboratoriów w oparciu o lokalizacje, w których znajdują się uczestnicy.
- **Wybierz rozmiar obliczeń, który najlepiej odpowiada wymaganiom dotyczącym użycia**.

    Ogólnie rzecz biorąc, im większy rozmiar obliczeń, tym szybciej zostanie wykonana maszyna wirtualna. Aby jednak ograniczyć koszty, należy wybrać odpowiedni rozmiar obliczeń w zależności od potrzeb uczestników. Szczegóły dotyczące dostępnych rozmiarów obliczeń można znaleźć [w temacie Informacje o zmianie rozmiaru maszyny wirtualnej w podręczniku administratora](administrator-guide.md#vm-sizing) .
- **Skonfiguruj RDP\SSH dla połączenia pulpitu zdalnego z maszynami wirtualnymi z systemem Linux**.

    Jeśli hackathon korzysta z maszyn wirtualnych z systemem Linux, upewnij się, że pulpit zdalny jest włączony, aby uczestnicy mogli łączyć się z maszynami wirtualnymi za pomocą protokołu RDP (Remote Desktop Protocol) lub SSH (Secure Shell). Ten krok jest wymagany tylko w przypadku maszyn wirtualnych z systemem Linux i musi być włączony podczas tworzenia laboratorium. Ponadto w przypadku protokołu RDP przed opublikowaniem może być konieczne zainstalowanie i skonfigurowanie serwera RDP oraz pakietów graficznego interfejsu użytkownika na maszynie wirtualnej z szablonem.  Aby uzyskać więcej informacji, zobacz [Przewodnik po włączeniu pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).

- **Zainstaluj i Zatrzymaj aktualizacje systemu Windows**. 

    Jeśli używasz obrazu systemu Windows, zalecamy zainstalowanie najnowszych aktualizacji systemu Windows na [maszynie wirtualnej szablonu](how-to-create-manage-template.md) laboratorium przed opublikowaniem jej w celu utworzenia maszyn wirtualnych laboratoriów. Jest to przeznaczone do celów związanych z bezpieczeństwem i uniemożliwiają nieprzerwane działanie uczestników w hackathon w celu zainstalowania aktualizacji, co może spowodować ponowne uruchomienie maszyn wirtualnych. Możesz również rozważyć wyłączenie aktualizacji systemu Windows, aby zapobiec jakimkolwiek przyszłym przerwom. Zobacz przewodnik dotyczący [instalowania i konfigurowania aktualizacji systemu Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Zdecyduj, jak uczniowie będą tworzyć kopie zapasowe swoich zadań**. 

    Studenci otrzymują każdą przypisaną maszynę wirtualną na okres istnienia hackathon. Mogą oni zapisywać swoją służbę bezpośrednio na komputerze, ale zaleca się, aby uczniowie tworzyli kopie zapasowe swojej pracy, tak aby mieli do nich dostęp po zakończeniu hackathon. Na przykład powinny one zapisywać w lokalizacji zewnętrznej, np. w usłudze OneDrive, GitHub itd. Aby korzystać z usługi OneDrive, możesz skonfigurować ją automatycznie dla studentów na ich maszynach wirtualnych laboratorium. Zobacz [Przewodnik krok po kroku, aby zainstalować i skonfigurować usługę OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Ustawianie pojemności maszyny wirtualnej zgodnie z liczbą uczestników**. 

    Upewnij się, że pojemność maszyny wirtualnej laboratorium jest ustawiona na podstawie liczby uczestników, której oczekujesz w Twoim hackathon. Po opublikowaniu maszyny wirtualnej szablonu może upłynąć kilka godzin, aby utworzyć wszystkie maszyny w laboratorium. Dlatego zalecamy, aby po rozpoczęciu hackathon z wyprzedzeniem. Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący aktualizowania pojemności laboratorium](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Zdecyduj, czy ograniczyć dostęp do laboratorium**. 

    W przypadku dodawania użytkowników do laboratorium istnieje opcja Ogranicz dostęp, która jest domyślnie włączona. Ta funkcja wymaga dodania do listy wszystkich wiadomości e-mail uczestników hackathon, zanim będą oni mogli zarejestrować się i uzyskać dostęp do laboratorium przy użyciu linku rejestracji. Jeśli masz hackathon, w którym nie wiesz, kim są uczestnicy, możesz wyłączyć opcję Ogranicz dostęp, która umożliwia każdemu zarejestrowanie się w laboratorium przy użyciu linku rejestracji. Aby uzyskać więcej informacji, zobacz [Przewodnik po dodawaniu użytkowników](how-to-configure-student-usage.md#add-users-to-a-lab).

- **Sprawdź ustawienia harmonogramu, limitu przydziału i automatycznego zamykania**. 

    Usługi Lab Services udostępniają kilka kontrolek kosztów, które ograniczają użycie maszyn wirtualnych. Jednak jeśli te ustawienia są nieprawidłowo skonfigurowane, mogą spowodować nieoczekiwane zamknięcie maszyn wirtualnych w laboratorium. Aby upewnić się, że te ustawienia zostały odpowiednio skonfigurowane dla hackathon, sprawdź następujące ustawienia:

    **Harmonogram**: [harmonogram](how-to-create-schedules.md) pozwala automatycznie kontrolować, kiedy maszyny w laboratoriach są uruchamiane i zamykane. Domyślnie podczas tworzenia nowego laboratorium nie jest konfigurowany żaden harmonogram. Należy jednak upewnić się, że harmonogram laboratorium jest ustawiony zgodnie z tym, co jest sensowne dla hackathon.  Jeśli na przykład hackathon rozpoczyna się w sobotę o godzinie 8:00 AM i kończy się w niedzielę o godzinie 5:00 PM — można utworzyć harmonogram, który automatycznie uruchamia maszynę w dniu 7:30 w sobotę (około 30 minut przed rozpoczęciem hackathon) i zamyka ją na 5:00 PM w niedzielę. Zamiast tego możesz też zrezygnować z używania harmonogramu.

    **Limit przydziału**: [określa liczbę](how-to-configure-student-usage.md#set-quotas-for-users) godzin, przez które uczestnicy będą mieli dostęp do maszyny wirtualnej poza zaplanowanymi godzinami. Jeśli przydział zostanie osiągnięty w czasie, gdy jest używany przez uczestnika, komputer zostanie automatycznie zamknięty i uczestnik nie będzie mógł go uruchomić ponownie, chyba że zostanie zwiększony przydział. Domyślnie podczas tworzenia laboratorium obowiązuje limit przydziału równy 10 godzin. Należy pamiętać o ustawieniu limitu przydziału, aby zapewnić wystarczającą ilość czasu dla hackathon, co jest szczególnie ważne, jeśli nie utworzono harmonogramu.

    Automatyczne **zamykanie**: po włączeniu ustawienia automatycznego [zamykania](how-to-enable-shutdown-disconnect.md) powoduje, że maszyny wirtualne z systemem Windows są automatycznie zamykane po upływie pewnego czasu od momentu odłączenia ucznia od sesji RDP. To ustawienie jest domyślnie wyłączone.

- **Skonfiguruj ustawienia zapory, aby umożliwić nawiązywanie połączeń z maszynami wirtualnymi laboratorium**. 

    Upewnij się, że ustawienia zapory dla szkoły lub organizacji umożliwiają nawiązywanie połączenia z maszynami wirtualnymi laboratorium przy użyciu RDP\SSH. Aby uzyskać więcej informacji, zobacz [Przewodnik po konfigurowaniu ustawień zapory w sieci](how-to-configure-firewall-settings.md).

- **Zainstaluj klienta programu RDP\SSH na tabletach, komputerach Mac, komputerach i tak dalej**.

    Uczestnicy hackathon muszą mieć zainstalowanego klienta RDP i/lub SSH na swoich tabletach lub laptopach, które będą używane do nawiązywania połączeń z maszynami wirtualnymi laboratorium. Możesz wybrać różnych klientów RDP lub SSH, takich jak:

    - Aplikacja **Podłączanie pulpitu zdalnego** firmy Microsoft dla połączeń RDP. Aplikacja Podłączanie pulpitu zdalnego jest obsługiwana na różnych typach platform, w tym Chromebooks i [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - W [przypadku używania protokołu SSH do](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) nawiązywania połączenia z maszyną wirtualną z systemem Linux.
- **Weryfikowanie maszyn wirtualnych laboratorium**. 

    Po opublikowaniu maszyn wirtualnych laboratorium należy sprawdzić, czy są one poprawnie skonfigurowane. Tę weryfikację należy wykonać tylko dla jednej z maszyn wirtualnych w laboratorium uczestnika:

    1. Połącz przy użyciu protokołu RDP and\or SSH.
    2. Otwórz każdą dodatkową aplikację i narzędzie, które zostały zainstalowane, aby dostosować podstawowy obraz maszyny wirtualnej.
    3. Zapoznaj się z kilkoma podstawowymi scenariuszami, które są reprezentatywne dla działań podejmowanych przez uczestników w celu zapewnienia wydajności maszyny wirtualnej, w zależności od wybranego rozmiaru obliczeń.

## <a name="on-the-day-of-hackathon"></a>W dniu hackathon
W tej sekcji opisano kroki, które należy wykonać, aby zakończyć dzień Twojego Hackathonu.

1. **Uruchom maszyny wirtualne laboratorium**.

    W zależności od systemu operacyjnego uruchomienie maszyny laboratoryjnej może potrwać do 30 minut. W związku z tym ważne jest, aby uruchomić maszyny przed rozpoczęciem hackathon, aby uczestnicy nie musieli czekać. Jeśli używasz harmonogramu, upewnij się, że maszyny wirtualne są uruchamiane automatycznie co najmniej 30 minut wcześniej.
2. **Zapraszaj uczniów do rejestracji i uzyskiwania dostępu do maszyny wirtualnej laboratorium**. 

    Zapewnij uczestnikom następujące informacje, aby uczestnicy mogli uzyskiwać dostęp do maszyn wirtualnych laboratorium. 

    - Link do rejestracji laboratorium. 
    - Poświadczenia, które powinny być używane do nawiązywania połączenia z maszyną. Ten krok ma zastosowanie tylko wtedy, gdy laboratorium używa obrazu z systemem Windows i skonfigurowano wszystkie maszyny wirtualne do używania tego samego hasła.
    - Instrukcje dotyczące sposobu, w jaki uczestnicy SSH and\or RDP z maszynami.

        Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [wysyłania zaproszeń do użytkowników](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) i [łączenia się z maszynami wirtualnymi z systemem Linux](how-to-use-remote-desktop-linux-student.md?branch=master). 

## <a name="next-steps"></a>Następne kroki
Rozpocznij od utworzenia konta laboratorium w laboratoriach, wykonując instrukcje zawarte w artykule: [Samouczek: Konfigurowanie konta laboratorium przy użyciu Azure Lab Services](tutorial-setup-lab-account.md).