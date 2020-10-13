---
title: Przewodnik konfigurowania dla Azure Lab Services
description: Ten przewodnik ułatwia twórcom laboratorium szybkie konfigurowanie konta laboratorium do użycia w ramach szkoły.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 510aa97a0a47e62f627203495c601bb2538e19ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652214"
---
# <a name="classroom-lab-setup-guide"></a>Przewodnik konfigurowania laboratorium zajęć

Proces publikowania laboratorium dla uczniów może trwać do kilku godzin w zależności od liczby maszyn wirtualnych, które zostaną utworzone w laboratorium. Poczekaj co najmniej dzień, aby skonfigurować laboratorium, aby upewnić się, że działa prawidłowo i aby umożliwić wystarczającą ilość czasu na publikowanie maszyn wirtualnych uczniów.

## <a name="understand-the-lab-requirements-of-your-class"></a>Zapoznaj się z wymaganiami laboratorium klasy

Przed skonfigurowaniem nowego laboratorium należy wziąć pod uwagę następujące kwestie.

### <a name="what-software-requirements-does-the-class-have"></a>Jakie wymagania programowe ma Klasa?

Na podstawie celów szkoleniowych klasy należy zdecydować, który system operacyjny, aplikacje i narzędzia muszą być zainstalowane na maszynach wirtualnych laboratorium. Aby skonfigurować maszyny wirtualne laboratorium, dostępne są trzy opcje:

- **Korzystanie z obrazu portalu Azure Marketplace**: Witryna Azure Marketplace udostępnia setki obrazów, których można użyć podczas tworzenia laboratorium. W przypadku niektórych klas jeden z tych obrazów może już zawierać wszystko, co jest potrzebne dla klasy.

- **Tworzenie nowego obrazu niestandardowego**: możesz utworzyć własny obraz niestandardowy przy użyciu obrazu portalu Azure Marketplace jako punktu wyjścia i dostosować go, instalując dodatkowe oprogramowanie i wprowadzając zmiany w konfiguracji.

- **Użyj istniejącego obrazu niestandardowego**: możesz ponownie użyć istniejących wcześniej utworzonych obrazów niestandardowych lub utworzonych przez innych administratorów lub wykładowców w szkole. Wymaga to od administratorów skonfigurowania udostępnionej galerii obrazów, która jest repozytorium do zapisywania obrazów niestandardowych.

> [!NOTE]
> Administratorzy są odpowiedzialni za Włączanie obrazów witryny Azure Marketplace i obrazów niestandardowych, dzięki czemu można z nich korzystać. Koordynuj z działem IT, aby upewnić się, że obrazy, które są potrzebne, są włączone. Utworzone obrazy niestandardowe są automatycznie włączane do użycia w ramach własnych laboratoriów.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jakie wymagania sprzętowe ma Klasa?

Istnieją różne rozmiary obliczeń, spośród których można wybrać:

- Zagnieżdżone rozmiary wirtualizacji, dzięki czemu możesz zapewnić dostęp do uczniów do maszyny wirtualnej, która może obsługiwać wiele zagnieżdżonych maszyn wirtualnych. Na przykład można użyć tego rozmiaru obliczeń dla kursów sieciowych.

- Rozmiary procesora GPU, dzięki czemu uczniowie mogą korzystać z typów aplikacji intensywnie korzystających z komputerów. Na przykład ten wybór może być odpowiedni dla sztucznej analizy i uczenia maszynowego.

Zapoznaj się z przewodnikiem dotyczącym [ustalania rozmiaru maszyny wirtualnej](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) , aby wyświetlić pełną listę dostępnych rozmiarów obliczeniowych.

> [!NOTE]
> W zależności od regionu laboratorium można zobaczyć mniej dostępne rozmiary obliczeń, ponieważ zależy to od regionu. Ogólnie rzecz biorąc, należy wybrać najmniejszy rozmiar obliczeń zbliżony do Twoich potrzeb. Za pomocą Azure Lab Services można skonfigurować nowe laboratorium z inną pojemnością obliczeniową później, w razie konieczności.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jakie zależności są zależne od platformy Azure lub zasobów sieciowych?

Jeśli maszyny wirtualne laboratorium muszą używać zasobów zewnętrznych, takich jak baza danych, udział plików lub serwer licencjonowania, koordynuj się z administratorami, aby upewnić się, że laboratorium ma dostęp do tych zasobów.

Aby uzyskać dostęp do zasobów platformy Azure, które *nie* są zabezpieczone przez sieć wirtualną, nie musisz szukać dodatkowych konfiguracji przez administratorów. Dostęp do tych zasobów można uzyskać za pomocą publicznego Internetu.

> [!NOTE]
> Należy rozważyć, czy można zmniejszyć zależności laboratorium do zasobów zewnętrznych, dostarczając zasób bezpośrednio na maszynie wirtualnej. Na przykład aby wyeliminować konieczność odczytywania danych z zewnętrznej bazy danych, można zainstalować bazę danych bezpośrednio na maszynie wirtualnej.  

### <a name="how-will-costs-be-controlled"></a>Jak będzie można kontrolować koszty?

Usługi Lab Services korzystają z modelu cen z płatność zgodnie z rzeczywistym użyciem, co oznacza, że płacisz tylko za czas działania maszyny wirtualnej laboratorium. Aby kontrolować koszty, dostępne są trzy opcje, które zwykle są używane w połączeniu ze sobą:

- **Harmonogram**: harmonogram pozwala na automatyczne sterowanie, gdy maszyny wirtualne w laboratoriach są uruchamiane i zamykane.
- **Limit przydziału**: określa liczbę godzin, przez które uczniowie będą mieli dostęp do maszyny wirtualnej poza zaplanowanymi godzinami. Jeśli przydział zostanie osiągnięty w czasie korzystania z niego, maszyna wirtualna zostanie automatycznie wyłączona. Student nie może ponownie uruchomić maszyny wirtualnej, o ile nie zwiększono limitu przydziału.
- **Automatyczne zamykanie**: po włączeniu ustawienia automatycznego zamykania powoduje automatyczne wyłączenie maszyn wirtualnych z systemem Windows po upływie określonego czasu, gdy student odłączy się od sesji Remote Desktop Protocol (RDP). To ustawienie jest domyślnie wyłączone.  

### <a name="how-will-students-save-their-work"></a>Jak uczniowie mogą zapisywać swoją służbę?

Studenci mają przypisane do nich własną maszynę wirtualną, która jest przypisana do nich przez okres istnienia laboratorium. Mogą oni:

- Zapisz bezpośrednio na maszynie wirtualnej.
- Zapisz w lokalizacji zewnętrznej, np. w usłudze OneDrive lub GitHub.

Możliwe jest automatyczne skonfigurowanie usługi OneDrive dla studentów na maszynach wirtualnych laboratorium.

> [!NOTE]
> Aby mieć pewność, że uczniowie mają ciągły dostęp do swoich zapisanych zadań poza laboratorium, a po zakończeniu klasy zalecamy, aby studenci zapisywali pracę w repozytorium zewnętrznym.

### <a name="how-will-students-connect-to-their-vm"></a>Jak uczniowie będą łączyć się z maszyną wirtualną?

W przypadku protokołu RDP z maszynami wirtualnymi z systemem Windows zaleca się, aby studenci używali [klienta pulpit zdalny Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient Pulpit zdalny obsługuje komputery Mac, Chromebooks i Windows.

W przypadku maszyn wirtualnych z systemem Linux studenci mogą korzystać z protokołów SSH lub RDP. Aby studenci mogli nawiązywać połączenia przy użyciu protokołu RDP, należy zainstalować i skonfigurować wymagane pakiety protokołu RDP i interfejsu GUI.

## <a name="set-up-your-lab"></a>Konfigurowanie laboratorium

Po zrozumieniu wymagań dla laboratorium klasy można rozpocząć konfigurację. Postępuj zgodnie z linkami w tej sekcji, aby dowiedzieć się, jak skonfigurować laboratorium.

1. **Utwórz laboratorium.** Aby uzyskać instrukcje, zapoznaj się z samouczkiem dotyczącym [tworzenia laboratorium klas](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) .

    > [!NOTE]
    > Jeśli Klasa wymaga wirtualizacji zagnieżdżonej, zapoznaj się z instrukcjami w temacie [Włączanie wirtualizacji zagnieżdżonej](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Dostosowywanie obrazów i publikowanie maszyn wirtualnych laboratorium.** Nawiąż połączenie z specjalną maszyną wirtualną o nazwie maszyna wirtualna. Zobacz kroki opisane w następujących przewodnikach:
    - [Tworzenie i zarządzanie maszyną wirtualną szablonów](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Używanie galerii obrazów udostępnionych](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > W przypadku korzystania z systemu Windows należy również zapoznać się z instrukcjami zawartymi w sekcji [przygotowywanie maszyny wirtualnej z szablonem systemu Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Te instrukcje obejmują kroki konfigurowania usługi OneDrive i pakietu Office do użycia przez uczniów.

1. **Zarządzanie pulą i pojemnością maszyn wirtualnych.** Możesz łatwo skalować w górę lub w dół pojemność maszyny wirtualnej, zgodnie z potrzebami klasy. Należy pamiętać, że zwiększenie pojemności maszyny wirtualnej może potrwać kilka godzin, ponieważ obejmuje to skonfigurowanie nowych maszyn wirtualnych. Zobacz procedurę [konfigurowania puli maszyn wirtualnych i zarządzania nią](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Dodawanie użytkowników laboratorium i zarządzanie nimi.** Aby dodać użytkowników do laboratorium, zapoznaj się z instrukcjami w następujących samouczkach:
   - [Dodawanie użytkowników do laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Wyślij zaproszenia do użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Aby uzyskać informacje na temat typów kont, których mogą używać studenci, zobacz [konta uczniów](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Ustaw kontrolki kosztu.** Aby kontrolować koszty laboratorium, ustawiać harmonogramy, przydziały i automatyczne zamykanie. Zobacz następujące samouczki:

   - [Ustawianie harmonogramu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > W zależności od typu zainstalowanego systemu operacyjnego uruchomienie maszyny wirtualnej może potrwać kilka minut. Aby upewnić się, że maszyna wirtualna laboratorium jest gotowa do użycia w zaplanowanych godzinach, zalecamy ponowne uruchomienie maszyn wirtualnych w ciągu 30 minut.

   - [Ustawianie przydziałów dla użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) i [Ustawianie dodatkowego przydziału dla określonego użytkownika](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Włączanie automatycznego zamykania po rozłączeniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Harmonogramy i przydziały nie mają zastosowania do szablonu maszyny wirtualnej, ale obowiązują ustawienia automatycznego zamykania. 
        > 
        > Podczas tworzenia laboratorium jest tworzona maszyna wirtualna z szablonem, ale nie została ona uruchomiona. Można go uruchomić, nawiązać z nim połączenie i zainstalować wszystkie wstępnie wymagane oprogramowanie dla laboratorium, a następnie opublikować je. Po opublikowaniu szablonu maszyna wirtualna jest automatycznie zamykana, jeśli nie zostało to zrobione. 
        > 
        > Na maszynach wirtualnych szablonowych naliczane są **koszty** , dlatego należy się upewnić, że maszyna wirtualna jest ZAMKNIĘTA, gdy nie jest potrzebna do uruchomienia. 


1. **Użyj pulpitu nawigacyjnego.** Aby uzyskać instrukcje, zobacz [Korzystanie z pulpitu nawigacyjnego laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Szacowany koszt pokazany na pulpicie nawigacyjnym to maksymalny koszt, który można oczekiwać na użycie dla studentów w laboratorium. Na przykład *nie* będzie naliczana opłata za niewykorzystane godziny według uczniów. Szacowane koszty *nie* uwzględniają żadnych opłat za korzystanie z szablonu maszyny wirtualnej, galerii obrazów udostępnionych ani gdy twórca laboratorium uruchamia komputer użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Śledzenie użycia laboratorium na potrzeby zajęć](tutorial-track-usage.md)
  
- [Dostęp do laboratorium na potrzeby zajęć](tutorial-connect-virtual-machine-classroom-lab.md)
