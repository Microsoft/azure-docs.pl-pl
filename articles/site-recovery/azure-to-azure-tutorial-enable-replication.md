---
title: Konfigurowanie odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w innym regionie świadczenia usługi Azure za pomocą usługi Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421420"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez zarządzanie replikacją, trybem failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure.

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przez replikowanie ich z jednego regionu świadczenia usługi Azure do innego. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu usługi Recovery Services
> * Sprawdzanie ustawień zasobów docelowych
> * Konfigurowanie wychodzącej łączności sieciowej dla maszyn wirtualnych
> * Włączanie replikacji maszyny wirtualnej

> [!NOTE]
> Ten artykuł zawiera instrukcje dotyczące wdrażania odzyskiwania po awarii przy użyciu najprostszych ustawień. Jeśli chcesz dowiedzieć się więcej o niestandardowych ustawieniach, zapoznaj się z artykułami w [sekcji jak to zrobić](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zapoznaj się ze [składnikami i architekturą scenariusza](./azure-to-azure-architecture.md).
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](./azure-to-azure-support-matrix.md) przed rozpoczęciem.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Następnie wybierz **ją & narzędzia**  >  **do zarządzania kopia zapasowa i Site Recovery**.
1. W polu **Nazwa**Określ przyjazną nazwę identyfikującą magazyn. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
1. Utwórz grupę zasobów lub wybierz istniejącą grupę. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Aby uzyskać dostęp do magazynu z pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , a następnie wybierz pozycję **Utwórz**.

   ![Nowy magazyn](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.

## <a name="verify-target-resource-settings"></a>Sprawdzanie ustawień zasobów docelowych

Sprawdź subskrypcję platformy Azure w regionie docelowym.

- Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.
- Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Usługa Site Recovery wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Konfigurowanie wychodzącej łączności sieciowej dla maszyn wirtualnych

Aby zapewnić zgodne z oczekiwaniami działanie usługi Site Recovery, musisz zmodyfikować łączność sieciową wychodzącą z maszyn wirtualnych, które mają być replikowane.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Magazyn                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Usługa Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), utwórz reguły sieciowej grupy zabezpieczeń oparte na znacznikach usługi, aby uzyskać dostęp do usługi Azure Storage, Azure Active Directory, Site Recovery i monitorowania Site Recovery. [Dowiedz się więcej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Weryfikowanie certyfikatów maszyn wirtualnych platformy Azure

Sprawdź, czy maszyny wirtualne, które chcesz replikować, mają najnowsze certyfikaty główne. Jeśli nie, nie można zarejestrować maszyny wirtualnej w celu Site Recovery ze względu na ograniczenia zabezpieczeń.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
- Aby zainstalować najnowsze wymagane certyfikaty główne i listę odwołania certyfikatów na maszynach wirtualnych z systemem Linux, postępuj zgodnie ze wskazówkami dystrybutora systemu Linux.

## <a name="set-permissions-on-the-account"></a>Ustawianie uprawnień konta

Usługa Azure Site Recovery udostępnia trzy role wbudowane, umożliwiające kontrolowanie operacji zarządzania usługą Site Recovery.

- **Współautor usługi Site Recovery** — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy mogą włączać odzyskiwanie po awarii dla aplikacji lub całych organizacji oraz zarządzać tą funkcją.

- **Operator usługi Site Recovery** — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który może przełączać maszyny wirtualne lub aplikacje w tryb failover po uzyskaniu odpowiednich instrukcji od właścicieli aplikacji i administratorów IT. Po rozwiązaniu awarii operator odzyskiwania po awarii może ponownie chronić maszyny wirtualne i przewracać do nich.

- **Czytelnik usługi Site Recovery** — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednia dla członka kadry zarządzającej, który zajmuje się monitorowaniem infrastruktury IT i bieżącego stanu jej ochrony oraz może wysyłać zgłoszenia do pomocy technicznej.

Dowiedz się więcej o [rolach wbudowanych platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Włączanie replikacji maszyny wirtualnej

W poniższych sekcjach opisano sposób włączania replikacji.

### <a name="select-the-source"></a>Wybieranie źródła

Aby rozpocząć konfigurację replikacji, wybierz źródło, w którym działają maszyny wirtualne platformy Azure.

1. Przejdź do obszaru **Recovery Services magazyny**, wybierz nazwę magazynu, a następnie wybierz pozycję **+ Replikuj**.
1. W polu **Źródło**wybierz pozycję **Azure**.
1. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
1. Wybierz **Subskrypcję źródłową**, w której działają maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
1. Wybierz **źródłową grupę zasobów**i wybierz pozycję **OK** , aby zapisać ustawienia.

   ![Konfiguracja źródła](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Wybieranie maszyn wirtualnych

Usługa Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów/usługą w chmurze.

1. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
1. Wybierz przycisk **OK**.

### <a name="configure-replication-settings"></a>Konfigurowanie ustawień replikacji

Usługa Site Recovery tworzy ustawienia domyślne i zasady replikacji w regionie docelowym. Te ustawienia można zmienić zgodnie z wymaganiami.

1. Wybierz pozycję **Ustawienia** , aby wyświetlić ustawienia elementu docelowego i replikacji.

1. Aby zastąpić domyślne ustawienia docelowe, wybierz pozycję **Dostosuj** obok pozycji **Grupa zasobów, Sieć, magazyn i dostępność**.

   ![Konfigurowanie ustawień](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Dostosuj ustawienia docelowe zgodnie z podsumowaniem w tabeli.

   | **Ustawienie** | **Szczegóły** |
   | --- | --- |
   | **Subskrypcja docelowa** | Domyślnie subskrypcja docelowa jest taka sama jak w przypadku subskrypcji źródłowej. Wybierz pozycję **Dostosuj** , aby wybrać inną subskrypcję docelową w ramach tej samej dzierżawy Azure Active Directory. |
   | **Lokalizacja docelowa** | Region docelowy używany na potrzeby odzyskiwania po awarii.<br/><br/> Zaleca się, aby lokalizacja docelowa odpowiadała lokalizacji magazynu usługi Site Recovery. |
   | **Docelowa Grupa zasobów** | Grupa zasobów w regionie docelowym, w której są przechowywane maszyny wirtualne platformy Azure po przejściu w tryb failover.<br/><br/> Domyślnie Site Recovery tworzy nową grupę zasobów w regionie docelowym przy użyciu `asr` sufiksu. Lokalizacją docelowej grupy zasobów może być dowolny region, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne. |
   | **Docelowa sieć wirtualna** | Sieć w regionie docelowym, w której znajdują się maszyny wirtualne po przejściu w tryb failover.<br/><br/> Domyślnie Site Recovery tworzy nową sieć wirtualną (i podsieci) w regionie docelowym z `asr` sufiksem. |
   | **Konta magazynu pamięci podręcznej** | Usługa Site Recovery używa konta magazynu w regionie źródłowym. Do tego konta są wysyłane zmiany źródłowych maszyn wirtualnych przed uruchomieniem replikacji do lokalizacji docelowej.<br/><br/> Jeśli używasz konta magazynu pamięci podręcznej z włączoną obsługą zapory, upewnij się, że włączono opcję **Zezwalaj na zaufane usługi firmy Microsoft**. [Dowiedz się więcej](../storage/common/storage-network-security.md#exceptions). Upewnij się również, że zezwolisz na dostęp do co najmniej jednej podsieci źródłowej sieci wirtualnej. |
   | **Docelowe konta magazynu (źródłowa maszyna wirtualna używa dysków innych niż zarządzane)** | Domyślnie usługa Site Recovery tworzy w regionie docelowym nowe konto magazynu, które jest duplikatem źródłowego konta magazynu maszyn wirtualnych.<br/><br/> Włącz opcję **Zezwalaj na zaufane usługi firmy Microsoft** , jeśli używasz konta magazynu pamięci podręcznej z włączoną obsługą zapory. |
   | **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna korzysta z dysków zarządzanych)** | Domyślnie program Site Recovery tworzy dyski zarządzane repliki w regionie docelowym, aby dublować dyski zarządzane źródłowej maszyny wirtualnej z tym samym typem magazynu (w warstwie Standardowa lub Premium) jako dyskiem zarządzanym źródłowej maszyny wirtualnej. Można dostosować tylko typ dysku. |
   | **Docelowe zestawy dostępności** | Domyślnie program Azure Site Recovery tworzy nowy zestaw dostępności w regionie docelowym z nazwą `asr` sufiksu dla maszyn wirtualnych w zestawie dostępności w regionie źródłowym. W przypadku zestawu dostępności, który został utworzony przez Azure Site Recovery już istnieje, jest on ponownie używany. |
   | **Docelowe strefy dostępności** | Domyślnie usługa Site Recovery przypisuje w regionie docelowym ten sam numer strefy co regionu źródłowego, jeśli region docelowy obsługuje strefy dostępności.<br/><br/> Jeśli region docelowy nie obsługuje stref dostępności, docelowe maszyny wirtualne są domyślnie konfigurowane jako pojedyncze wystąpienia.<br/><br/> Wybierz pozycję **Dostosuj** , aby skonfigurować maszyny wirtualne jako część zestawu dostępności w regionie docelowym.<br/><br/> Po włączeniu replikacji nie można zmienić typu dostępności (pojedyncze wystąpienie, zestaw dostępności lub strefa dostępności). Aby zmienić typ dostępności, wyłącz i Włącz replikację. |

1. Aby dostosować ustawienia zasad replikacji, wybierz opcję **Dostosuj** obok **zasad replikacji**, a następnie zmodyfikuj ustawienia zgodnie z wymaganiami.

   | **Ustawienie** | **Szczegóły** |
   | --- | --- |
   | **Nazwa zasad replikacji** | Nazwa zasad. |
   | **Przechowywanie punktów odzyskiwania** | Domyślnie usługa Site Recovery przechowuje punkty odzyskiwania przez 24 godziny. Można skonfigurować wartość z zakresu od 1 do 72 godzin. |
   | **Częstotliwość migawek spójnych na poziomie aplikacji** | Domyślnie usługa Site Recovery wykonuje migawki na poziomie aplikacji co 4 godziny. Można skonfigurować wartość z zakresu od 1 do 12 godzin.<br/><br/> Migawka spójna na poziomie aplikacji to migawka danych aplikacji znajdujących się w danym momencie w ramach maszyny wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia stan spójności aplikacji podczas wykonywania migawki. |
   | **Grupa replikacji** | Jeśli aplikacja wymaga spójności obejmującej wiele maszyn wirtualnych, można utworzyć grupę replikacji dla tych maszyn wirtualnych. Domyślnie wybrane maszyny wirtualne nie są częścią żadnej grupy replikacji. |

1. W obszarze **Dostosowywanie** wybierz opcję **Tak**, aby zachować spójność wielu maszyn wirtualnych, jeśli chcesz dodać maszyny wirtualne do nowej lub istniejącej grupy replikacji. Następnie wybierz przycisk **OK**.

   > [!NOTE]
   > - W przypadku przełączenia w tryb failover wszystkie maszyny w grupie replikacji mają wspólne punkty odzyskiwania spójne ze spójnością.
   > - Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążeń (intensywnie procesora CPU). Powinna być używana tylko wtedy, gdy na maszynach działa to samo obciążenie, a dla wielu maszyn jest wymagana spójność.
   > - Grupa replikacji może zawierać maksymalnie 16 maszyn wirtualnych.
   > - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że Zapora nie blokuje komunikacji wewnętrznej między maszynami wirtualnymi za pośrednictwem tego portu.
   > - W przypadku maszyn wirtualnych z systemem Linux w grupie replikacji upewnij się, że ruch wychodzący na porcie 20004 został ręcznie otwarty zgodnie ze wskazówkami dotyczącymi wersji systemu Linux.

### <a name="configure-encryption-settings"></a>Konfigurowanie ustawień szyfrowania

Jeśli źródłowa maszyna wirtualna ma włączoną usługę Azure Disk Encryption (ADE), przejrzyj ustawienia.

1. Sprawdź ustawienia:
   1. **Magazyny kluczy szyfrowania dysku**: domyślnie Site Recovery tworzy nowy magazyn kluczy dla źródłowych kluczy szyfrowania dysku maszyny wirtualnej z `asr` sufiksem. Jeśli magazyn kluczy już istnieje, jest ponownie używany.
   1. **Magazyny kluczy szyfrowania kluczy**: domyślnie Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa ma `asr` sufiks, który jest oparty na kluczach szyfrowania źródłowych maszyn wirtualnych. Jeśli magazyn kluczy utworzony przez Site Recovery już istnieje, jest ponownie używany.
1. Wybierz pozycję **Dostosuj** , aby wybrać niestandardowe magazyny kluczy.

>[!NOTE]
> Site Recovery obecnie obsługuje ADE, z i bez Azure Active Directory (AAD) dla maszyn wirtualnych z systemami operacyjnymi Windows. W przypadku systemów operacyjnych Linux obsługujemy tylko ADE bez usługi AAD. Ponadto w przypadku maszyn z uruchomionym programem ADE 1,1 (bez usługi AAD) maszyny wirtualne muszą używać dysków zarządzanych. Maszyny wirtualne z dyskami niezarządzanymi nie są obsługiwane. Jeśli przełączysz się z programu ADE 0,1 (z usługą AAD) do 1,1, musisz wyłączyć replikację i włączyć replikację dla maszyny wirtualnej po włączeniu opcji 1,1.

### <a name="track-replication-status"></a>Śledzenie stanu replikacji

Po włączeniu replikacji można śledzić stan zadania.

1. W obszarze **Ustawienia**wybierz pozycję **Odśwież** , aby uzyskać najnowszy stan.
1. Postęp i status możesz śledzić w następujący sposób:
   1. Postęp zadania **Włącz ochronę** możesz śledzić w obszarze **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
   1. W obszarze **Ustawienia**  >  **zreplikowane elementy**można wyświetlić stan maszyn wirtualnych i postęp początkowej replikacji. Wybierz maszynę wirtualną, aby przejść do szczegółów jej ustawień.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano odzyskiwanie po awarii dla maszyny wirtualnej platformy Azure. Teraz można uruchomić sprawdzanie odzyskiwania po awarii, aby sprawdzić, czy tryb failover działa zgodnie z oczekiwaniami.

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania](azure-to-azure-tutorial-dr-drill.md)
