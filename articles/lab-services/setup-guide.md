---
title: Przewodnik konfigurowania programu Lab dla Azure Lab Services
description: Jeśli jesteś twórcą laboratorium, ten przewodnik może pomóc w szybkim skonfigurowaniu konta laboratorium w szkole.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021735"
---
# <a name="lab-setup-guide"></a>Przewodnik po konfiguracji laboratorium

W tym przewodniku dowiesz się, jak utworzyć laboratorium dla studentów w szkole.

Proces publikowania laboratorium dla uczniów może trwać do kilku godzin. Czas instalacji zależy od liczby maszyn wirtualnych, które mają zostać utworzone w laboratorium. Zezwól na co najmniej dzień, aby upewnić się, że laboratorium działa prawidłowo i aby zapewnić wystarczającą ilość czasu na opublikowanie maszyn wirtualnych uczniów.

## <a name="understand-the-lab-requirements-of-your-class"></a>Zapoznaj się z wymaganiami laboratorium klasy

Przed skonfigurowaniem nowego laboratorium należy wziąć pod uwagę następujące kwestie.

### <a name="what-software-requirements-does-the-class-have"></a>Jakie wymagania programowe ma Klasa?

Zapoznaj się z celami uczenia klasy podczas decydowania, który system operacyjny, aplikacje i narzędzia potrzebne do zainstalowania na maszynach wirtualnych laboratorium. Aby skonfigurować maszyny wirtualne laboratorium, dostępne są trzy opcje:

- **Korzystanie z obrazu portalu Azure Marketplace**: Witryna Azure Marketplace udostępnia setki obrazów, których można użyć podczas tworzenia laboratorium. W przypadku niektórych klas jeden z tych obrazów może już zawierać wszystko, co jest potrzebne dla klasy.

- **Tworzenie nowego obrazu niestandardowego**: możesz utworzyć własny obraz niestandardowy przy użyciu obrazu portalu Azure Marketplace jako punktu początkowego. Następnie można dostosować ją, instalując dodatkowe oprogramowanie i wprowadzając zmiany w konfiguracji.

- **Użyj istniejącego obrazu niestandardowego**: możesz ponownie użyć wcześniej utworzonych obrazów niestandardowych lub obrazów utworzonych przez innych administratorów lub wykładowców w szkole. Aby korzystać z obrazów niestandardowych, Administratorzy muszą skonfigurować udostępnioną galerię obrazów.  Udostępniona Galeria obrazów jest repozytorium, które jest używane do zapisywania obrazów niestandardowych.

> [!NOTE]
> Administratorzy są odpowiedzialni za Włączanie obrazów witryny Azure Marketplace i obrazów niestandardowych, dzięki czemu można z nich korzystać. Koordynuj z działem IT, aby upewnić się, że obrazy, które są potrzebne, są włączone. Utworzone obrazy niestandardowe są automatycznie włączane do użycia w ramach własnych laboratoriów.

### <a name="what-hardware-requirements-does-the-class-have"></a>Jakie wymagania sprzętowe ma Klasa?

Można wybierać spośród różnych rozmiarów obliczeń:

- **Zagnieżdżone rozmiary wirtualizacji**: umożliwia przyznanie uczniom dostępu do maszyny wirtualnej, która może obsługiwać wiele zagnieżdżonych maszyn wirtualnych. Na przykład można użyć tego rozmiaru obliczeń dla sieci lub klas działanie hakerskie etycznych.

- **Rozmiary procesora GPU**: umożliwia studentom korzystanie z typów aplikacji intensywnie korzystających z komputerów. Na przykład ten wybór jest często używany w przypadku sztucznej analizy i uczenia maszynowego.

Aby uzyskać wskazówki dotyczące wybierania odpowiedniego rozmiaru maszyny wirtualnej, zobacz:
- [Rozmiar maszyny wirtualnej](./administrator-guide.md#vm-sizing)
- [Przenieś z laboratorium fizycznego do Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Ponieważ dostępność rozmiaru obliczeń różni się w zależności od regionu, w laboratorium mogą być dostępne mniejsze rozmiary. Ogólnie rzecz biorąc, należy wybrać najmniejszy rozmiar obliczeniowy, który odpowiada Twoim potrzebom. Za pomocą Azure Lab Services można skonfigurować nowe laboratorium o większej pojemności obliczeniowej później, jeśli zachodzi taka potrzeba.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Jakie zależności są zależne od platformy Azure lub zasobów sieciowych?
Maszyny wirtualne laboratorium mogą wymagać dostępu do zasobów zewnętrznych, takich jak baza danych, udział plików lub serwer licencjonowania.  Aby umożliwić maszynom wirtualnym laboratorium korzystanie z zasobów zewnętrznych, koordynuj je z administratorami IT.

> [!NOTE]
> Należy rozważyć, czy można zmniejszyć zależność laboratorium od zasobów zewnętrznych, dostarczając zasoby sieciowe bezpośrednio na maszynie wirtualnej. Na przykład aby wyeliminować konieczność odczytywania danych z zewnętrznej bazy danych, można zainstalować bazę danych bezpośrednio na maszynie wirtualnej.  

### <a name="how-will-you-control-costs"></a>Jak będą kontrolowane koszty?
Usługi Lab Services korzystają z modelu cen z płatność zgodnie z rzeczywistym użyciem, co oznacza, że płacisz tylko za czas działania maszyny wirtualnej laboratorium. Aby kontrolować koszty, użyj dowolnej lub wszystkich następujących opcji:

- **Harmonogram**: Użyj harmonogramów, aby automatycznie kontrolować czas uruchamiania i zamykania maszyn wirtualnych laboratorium.
- **Przydział**: przydziały służą do kontrolowania liczby godzin, do których uczniowie mają dostęp do maszyny wirtualnej poza zaplanowanymi godzinami.  Gdy student korzysta z maszyny wirtualnej i osiągnie limit przydziału, maszyna wirtualna zostanie automatycznie wyłączona.  Student nie może ponownie uruchomić maszyny wirtualnej, o ile nie zostanie zwiększony przydział.
- **Automatyczne zamykanie**: po włączeniu ustawienia automatycznego zamykania maszyny wirtualne z systemem Windows są automatycznie zamykane po odłączeniu ucznia od sesji Remote Desktop Protocol (RDP). To ustawienie jest domyślnie wyłączone.

Aby uzyskać więcej informacji na temat kontrolowania kosztów, zobacz:
- [Szacowanie kosztów](./cost-management-guide.md#estimate-the-lab-costs)
- [Zarządzanie kosztami](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Jak uczniowie mogą zapisywać swoją służbę?
Każdy student ma przypisaną maszynę wirtualną na okres istnienia laboratorium. Studenci mogą zaoszczędzić swoją służbę:

- Na maszynę wirtualną.
- Do lokalizacji zewnętrznej, takiej jak usługa OneDrive lub GitHub. Możliwe jest automatyczne skonfigurowanie usługi OneDrive dla studentów na maszynach wirtualnych laboratorium.

> [!NOTE]
> Aby mieć pewność, że uczniowie mają ciągły dostęp do swoich zapisanych zadań poza laboratorium i po zakończeniu klasy, zalecamy zapisanie pracy w repozytorium zewnętrznym.

### <a name="how-will-students-connect-to-their-vms"></a>Jak uczniowie będą łączyć się z maszynami wirtualnymi?
W przypadku połączeń RDP z maszynami wirtualnymi z systemem Windows zaleca się, aby studenci używali [klienta pulpit zdalny Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klient Pulpit zdalny obsługuje urządzenia Mac, Chromebook i Windows.

W przypadku maszyn wirtualnych z systemem Linux studenci mogą korzystać z protokołu Secure Shell (SSH) lub RDP. Aby nawiązać połączenie uczniów przy użyciu protokołu RDP, należy zainstalować i skonfigurować niezbędne pakiety protokołu RDP i graficznego interfejsu użytkownika (GUI).

### <a name="will-students-also-use-microsoft-teams"></a>Czy uczniowie będą również korzystać z Microsoft Teams?
Azure Lab Services integruje się z usługą Microsoft Teams, aby członkowie wykładowców mogli tworzyć i zarządzać swoimi laboratoriami w zespołach.  Podobnie uczniowie mogą uzyskać dostęp do swoich laboratoriów w zespołach.

Aby uzyskać więcej informacji, zobacz [Azure Lab Services w programie Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Konfigurowanie laboratorium

Po zrozumieniu wymagań dla laboratorium klasy można rozpocząć konfigurację. Aby dowiedzieć się, jak to zrobić, Skorzystaj z linków w tej sekcji. Dostępne są również instrukcje dotyczące konfigurowania laboratoriów w zespołach.

1. **Utwórz laboratorium**. Zobacz następujące samouczki:
    - [Tworzenie laboratorium na potrzeby zajęć](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Tworzenie laboratorium w zespołach](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Jeśli Klasa wymaga wirtualizacji zagnieżdżonej, zobacz [Włączanie wirtualizacji zagnieżdżonej](./how-to-enable-nested-virtualization-template-vm.md).

1. **Dostosowywanie obrazów i publikowanie maszyn wirtualnych laboratorium**. Aby nawiązać połączenie z specjalną maszyną wirtualną o nazwie maszyna wirtualna z szablonem, zobacz:
    - [Tworzenie i zarządzanie maszyną wirtualną szablonów](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Używanie galerii obrazów udostępnionych](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Jeśli używasz systemu Windows, zobacz również [Konfigurowanie maszyny wirtualnej z szablonem systemu Windows](./how-to-prepare-windows-template.md). Te instrukcje obejmują procedurę konfigurowania usługi OneDrive i Microsoft Office dla uczniów.

1. **Zarządzanie pulą i pojemnością maszyn wirtualnych**. Możesz łatwo skalować w górę lub w dół pojemność maszyny wirtualnej, zgodnie z potrzebami klasy. Należy pamiętać, że zwiększenie pojemności maszyny wirtualnej może potrwać kilka godzin, ponieważ trwa konfigurowanie nowych maszyn wirtualnych. Zobacz następujące artykuły:
    - [Konfigurowanie puli maszyn wirtualnych i zarządzanie nią](./how-to-set-virtual-machine-passwords.md)
    - [Zarządzanie pulą maszyn wirtualnych w usłudze Lab Services w zespołach](./how-to-manage-vm-pool-within-teams.md)

1. **Dodawanie użytkowników laboratorium i zarządzanie nimi**. Aby dodać użytkowników do laboratorium, zobacz:
   - [Dodawanie użytkowników do laboratorium](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Wyślij zaproszenia do użytkowników](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Zarządzanie listami użytkowników usług Lab Services w zespołach](./how-to-manage-user-lists-within-teams.md)

    Aby uzyskać informacje o typach kont, których mogą używać studenci, zobacz [konta uczniów](./how-to-configure-student-usage.md#student-accounts).
  
1. **Ustaw kontrolki kosztu**. Aby ustawić harmonogram, ustalić przydziały i włączyć automatyczne zamykanie, zobacz następujące samouczki:

   - [Ustawianie harmonogramu](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > W zależności od zainstalowanego systemu operacyjnego uruchomienie maszyny wirtualnej może potrwać kilka minut. Aby upewnić się, że maszyna wirtualna laboratorium jest gotowa do użycia w zaplanowanych godzinach, zalecamy rozpoczęcie od 30 minut z wyprzedzeniem.

   - [Ustawianie przydziałów dla użytkowników](./how-to-configure-student-usage.md#set-quotas-for-users) i [Ustawianie dodatkowych przydziałów dla określonych użytkowników](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Włączanie automatycznego zamykania po rozłączeniu](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Harmonogramy i przydziały nie mają zastosowania do szablonu maszyny wirtualnej, ale obowiązują ustawienia automatycznego zamykania. 
        > 
        > Podczas tworzenia laboratorium jest tworzona maszyna wirtualna szablonu, ale nie została uruchomiona. Możesz uruchomić szablon maszyny wirtualnej, nawiązać z nią połączenie, zainstalować dowolne wstępnie wymagane oprogramowanie dla laboratorium, a następnie opublikować go. Po opublikowaniu szablonu maszyna wirtualna jest automatycznie zamykana, jeśli nie została wykonana ręcznie. 
        > 
        > W przypadku maszyn wirtualnych z szablonem naliczane są *koszty* , dlatego należy się upewnić, że maszyna wirtualna jest ZAMKNIĘTA, gdy nie będzie potrzebna.

    - [Tworzenie harmonogramów usług Lab i zarządzanie nimi w zespołach](./how-to-create-schedules-within-teams.md) 

1. **Użyj pulpitu nawigacyjnego**. Aby uzyskać instrukcje, zobacz [Korzystanie z pulpitu nawigacyjnego klasy z laboratorium](./use-dashboard.md).

    > [!NOTE]
    > Szacowany koszt pokazany na pulpicie nawigacyjnym to maksymalny koszt, który można zaliczyć na korzystanie z laboratorium studenta. Na przykład *nie* będzie naliczana opłata za niewykorzystane godziny według uczniów. Szacowane koszty *nie* uwzględniają żadnych opłat za korzystanie z szablonu maszyny wirtualnej, galerii obrazów udostępnionych ani gdy twórca laboratorium uruchamia komputer użytkownika.

## <a name="next-steps"></a>Następne kroki

W ramach zarządzania laboratoriami zapoznaj się z następującymi artykułami:
- [Śledź użycie laboratorium zajęć](tutorial-track-usage.md)  
- [Dostęp do laboratorium na potrzeby zajęć](tutorial-connect-virtual-machine-classroom-lab.md)
