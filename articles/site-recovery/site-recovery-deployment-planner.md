---
title: Planista wdrażania usługi Azure Site Recovery na potrzeby odzyskiwania po awarii oprogramowania VMware
description: Dowiedz się więcej o Planista wdrażania usługi Azure Site Recovery na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mayg
ms.openlocfilehash: 4291e8438f70e2e7190cd4dc6c890b5b325f2324
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360911"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Informacje o Planiście wdrażania usługi Azure Site Recovery dla środowiska VMware na platformie Azure
Ten artykuł to podręcznik użytkownika planisty wdrażania usługi Azure Site Recovery dla wdrożeń produkcyjnych oprogramowania VMware na platformie Azure.

## <a name="overview"></a>Omówienie

Zanim zaczniesz chronić maszyny wirtualne oprogramowania VMware za pomocą usługi Azure Site Recovery, przydziel odpowiednią przepustowość zgodnie z częstotliwością dziennych zmian danych, aby osiągnąć założony cel punktu odzyskiwania. Pamiętaj o lokalnym wdrożeniu odpowiedniej liczby serwerów konfiguracji i serwerów przetwarzania.

Musisz także utworzyć właściwą liczbę kont magazynów platformy Azure odpowiedniego typu. Możesz utworzyć konta magazynu w warstwie Standardowa lub Premium, biorąc pod uwagę wzrost w zakresie źródłowych serwerów produkcyjnych z powodu zwiększania użycia w czasie. Wybierz typ magazynu dla maszyny wirtualnej na podstawie charakterystyk obciążenia, np. operacji we/wy odczytu/zapisu na sekundę (IOPS) lub współczynnika zmian danych, oraz limitów usługi Site Recovery.

 Planista wdrażania usługi Site Recovery to narzędzie wiersza polecenia na potrzeby scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure i z oprogramowania VMware do platformy Azure. To narzędzie pozwala zdalnie profilować maszyny wirtualne oprogramowania VMware (bez żadnego wpływu na środowisko produkcyjne), aby poznać wymagania dotyczące przepustowości i magazynu dla udanej replikacji oraz testu pracy w trybie failover. Narzędzie możesz uruchomić bez instalowania składników usługi Site Recovery w środowisku lokalnym. W celu uzyskania dokładnych wyników osiągniętej przepływności uruchom planistę na serwerze z systemem Windows Server spełniającym minimalne wymagania dotyczące serwera konfiguracji usługi Site Recovery, który trzeba będzie wdrożyć na początku procesu wdrażania w środowisku produkcyjnym.

Narzędzie udostępnia następujące szczegóły:

**Ocena zgodności**

* Ocena uprawnień maszyny wirtualnej na podstawie liczby dysków, rozmiaru dysku, liczby operacji we/wy na sekundę, współczynnika zmian, typu rozruchu (EFI/BIOS) i wersji systemu operacyjnego

**Zapotrzebowanie na przepustowość sieci w porównaniu z oceną celu punktu odzyskiwania**

* Szacowana przepustowość sieci wymagana na potrzeby replikacji przyrostowej
* Przepływność, którą usługa Site Recovery może uzyskać między środowiskiem lokalnym i platformą Azure
* Liczba maszyn wirtualnych do przetworzenia wsadowego na podstawie szacowanej przepustowości wymagana do ukończenia replikacji początkowej w określonym czasie
* Cel punktu odzyskiwania, który można osiągnąć przy danej przepustowości
* Wpływ na wymagany cel punktu odzyskiwania w przypadku zapewnienia mniejszej przepustowości

**Wymagania dotyczące infrastruktury platformy Azure**

* Wymagania dotyczące typu magazynu (w warstwie Standardowa lub Premium) dla każdej maszyny wirtualnej
* Łączna liczba kont magazynu w warstwie Standardowa i Premium do skonfigurowania na potrzeby replikacji (w tym kont magazynu pamięci podręcznej)
* Propozycje nazw kont magazynu oparte na wskazówkach usługi Storage
* Liczba rdzeni platformy Azure do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testu pracy w trybie failover w ramach subskrypcji
* Zalecany rozmiar poszczególnych maszyn wirtualnych platformy Azure

**Wymagania dotyczące infrastruktury lokalnej**
* Wymagana liczba serwerów konfiguracji i serwerów przetwarzania do wdrożenia lokalnie

**Szacowany koszt odzyskiwania po awarii na platformie Azure**
* Szacowany łączny koszt odzyskiwania po awarii do platformy Azure: koszt obliczeń, magazynu, sieci i licencji usługi Site Recovery
* Szczegółowa analiza kosztów dla maszyny wirtualnej


>[!IMPORTANT]
>
>Ponieważ obciążenie będzie prawdopodobnie zwiększać się wraz z upływem czasu, wszystkie poprzednie obliczenia narzędzia są wykonywane przy założeniu 30-procentowego współczynnika wzrostu wartości charakterystyk obciążenia. W obliczeniach jest też używana wartość 95. percentyla wszystkich metryk profilowania, takich jak liczba operacji we/wy odczytu/zapisu na sekundę i współczynnik zmian danych. Zarówno współczynnik wzrostu, jak i obliczenie procentowe można konfigurować. Więcej informacji na temat współczynnika wzrostu można znaleźć w sekcji „Zagadnienia związane ze współczynnikiem wzrostu”. Więcej informacji na temat wartości percentyla można znaleźć w sekcji „Wartość percentyla użyta w obliczeniach”.
>

## <a name="support-matrix"></a>Tabela obsługi

| **Kategoria** | **Z programu VMware do platformy Azure** |**Z funkcji Hyper-V do platformy Azure**|**Azure–Azure**|**Z funkcji Hyper-V do lokacji dodatkowej**|**Z oprogramowania VMware do lokacji dodatkowej**
--|--|--|--|--|--
Obsługiwane scenariusze |Tak|Tak|Nie|Tak*|Nie
Obsługiwana wersja | vCenter 7,0, 6,7, 6,5, 6,0 lub 5,5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Obsługiwana konfiguracja|vCenter, ESXi| Klaster funkcji Hyper-V, host funkcji Hyper-V|NA|Klaster funkcji Hyper-V, host funkcji Hyper-V|NA|
Liczba serwerów, które mogą być profilowane, na uruchomione wystąpienie planisty wdrażania usługi Site Recovery |Jeden (w tym samym czasie można profilować maszyny wirtualne należące do jednego serwera vCenter lub jednego serwera ESXi)|Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| NA |Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| NA

* Narzędzie jest przeznaczone głównie dla scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure. W przypadku odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej można używać go tylko do sprawdzania zaleceń po stronie źródła, takich jak wymagana przepustowość, wymagana ilość wolnego miejsca na każdym serwerze źródłowym funkcji Hyper-V oraz wartości dzielenia na partie replikacji początkowej i definicje partii. Zignoruj zalecenia i koszty dotyczące platformy Azure z raportu. Ponadto nie można używać operacji uzyskiwania informacji o przepływności w scenariuszu odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej.

## <a name="prerequisites"></a>Wymagania wstępne
Narzędzie obejmuje dwa główne etapy — profilowanie i generowanie raportu. Jest też dostępny trzeci etap umożliwiający obliczanie tylko przepływności. Wymagania dotyczące serwera, z którego inicjowane jest profilowanie i pomiar przepływności, zostały przedstawione w poniższej tabeli.

| Wymaganie dotyczące serwera | Opis|
|---|---|
|Profilowanie i pomiar przepływności| <ul><li>System operacyjny: Windows Server 2016 lub Windows Server 2012 R2<br>(w idealnej sytuacji spełniający co najmniej [zalecenia dotyczące rozmiaru serwera konfiguracji](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server))</li><li>Konfiguracja maszyny: 8 wirtualnych procesorów CPU, 16 GB pamięci RAM, dysk twardy o rozmiarze 300 GB</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Dostęp przez Internet do platformy Azure (*. blob.core.windows.net) z tego serwera, port 443<br>[To jest opcjonalne. Możesz wybrać opcję udostępnienia przepustowości podczas ręcznego generowania raportu.]</li><li>Konto magazynu Azure</li><li>Dostęp administratora na serwerze</li><li>Minimalnie 100 GB wolnego miejsca na dysku (przy założeniu 1000 maszyn wirtualnych z średnio trzema dyskami na każdej z nich i profilowanych przez 30 dni)</li><li>Ustawienia poziomu statystyk programu VMware vCenter mogą mieć poziom 1 lub wyższy</li><li>Zezwalaj na port vCenter (domyślnie 443): Site Recovery Planista wdrażania używa tego portu do nawiązywania połączenia z serwerem vCenter/hostem ESXi</ul></ul>|
| Generowanie raportu | KOMPUTER z systemem Windows lub Windows Server z programem Excel 2013 lub nowszym.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6,0 R3](https://aka.ms/download_powercli) jest wymagana tylko w przypadku przekazywania opcji użytkownika w poleceniu generowania raportu w celu pobrania najnowszych informacji o konfiguracji maszyny wirtualnej maszyn wirtualnych. Planista wdrażania nawiązuje połączenie z serwerem vCenter. Zezwalaj portowi vCenter port (domyślnie 443) na połączenie z serwerem vCenter.</li>|
| Uprawnienia użytkowników | Uprawnienia tylko do odczytu dla konta użytkownika używanego do uzyskiwania dostępu do serwera VMware vCenter/hosta VMware vSphere ESXi podczas profilowania |

> [!NOTE]
>
>Narzędzie może profilować tylko maszyny wirtualne z dyskami VMDK i RDM. Nie pozwala ono profilować maszyn wirtualnych z dyskami iSCSI ani NFS. Usługa Site Recovery obsługuje dyski iSCSI i NFS w przypadku serwerów VMware. Ponieważ jednak planista wdrażania nie znajduje się na gościu i profiluje tylko przy użyciu liczników wydajności programu vCenter, narzędzie nie ma wglądu w te typy dysków.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Pobieranie i wyodrębnianie narzędzia planisty wdrażania
1. Pobierz najnowszą wersję [planisty wdrożenia usługi Site Recovery](https://aka.ms/asr-deployment-planner).
Narzędzie jest spakowane w folderze ZIP. Bieżąca wersja narzędzia obsługuje tylko replikację z oprogramowania VMware na platformę Azure.

2. Skopiuj folder ZIP na serwer z systemem Windows, z którego chcesz uruchomić narzędzie.
Narzędzie można uruchomić z systemu Windows Server 2012 R2, jeśli serwer ma dostęp do sieci umożliwiający połączenie z serwerem vCenter/hostem vSphere ESXi przechowującym maszyny wirtualne do profilowania. Zalecamy jednak uruchamianie narzędzia na serwerze, którego konfiguracja sprzętowa spełnia [wytyczne dotyczące określania rozmiaru serwera konfiguracji](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server). Jeśli wdrożono już lokalnie składniki usługi Site Recovery, uruchom narzędzie na serwerze konfiguracji.

    Na serwerze, na którym uruchamiasz narzędzie, zalecamy korzystanie z takiej samej konfiguracji sprzętu jak serwer konfiguracji (z wbudowanym serwerem przetwarzania). Dzięki takiej konfiguracji masz pewność, że osiągnięta przepływność zgłaszana przez narzędzie jest zgodna z rzeczywistą przepływnością, którą usługa Site Recovery może osiągnąć podczas replikacji. Obliczanie przepływności zależy od dostępnej przepustowości sieci na serwerze i konfiguracji sprzętu (np. procesora i magazynu) serwera. Jeśli uruchamiasz narzędzie z innego serwera, obliczana jest przepływność z tego serwera na platformę Azure. Ponadto ponieważ konfiguracja sprzętu serwera może różnić się od konfiguracji serwera konfiguracji, dane osiągniętej przepływności zgłaszanej przez narzędzie mogą być niedokładne.

3. Wyodrębnij folder ZIP.
Folder zawiera wiele plików i podfolderów. Plik wykonywalny nosi nazwę ASRDeploymentPlanner.exe i znajduje się w folderze nadrzędnym.

    Przykład: skopiuj plik zip na dysk E:\ i wyodrębnij go.
    Planner_v2.3.zip wdrażania E:\ASR

    Planner_v2.3\ASRDeploymentPlanner.exe wdrażania E:\ASR

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Aktualizowanie planisty wdrażania do najnowszej wersji

Najnowsze aktualizacje są podsumowane w [historii wersji](site-recovery-deployment-planner-history.md)planista wdrażania.

Jeśli masz wcześniejszą wersję planisty wdrażania, wykonaj jedną z następujących czynności:
 * Jeśli najnowsza wersja nie zawiera poprawki profilowania i profilowanie jest już w toku w bieżącej wersji planisty, kontynuuj profilowanie.
 * Jeśli najnowsza wersja zawiera poprawkę profilowania, zalecamy zatrzymanie profilowania w bieżącej wersji, a następnie jego ponowne uruchomienie w nowej wersji.


 >[!NOTE]
 >
 >W przypadku uruchamiania profilowania w nowej wersji przekaż taką samą ścieżkę katalogu wyjściowego, aby narzędzie dołączało dane profilu do istniejących plików. Do wygenerowania raportu zostanie użyty kompletny zestaw profilowanych danych. Jeśli przekażesz inny katalog danych wyjściowych, zostaną utworzone nowe pliki, a stare profilowane dane nie będą używane podczas generowania raportu.
 >
 >Każda nowa wersja planisty wdrażania jest aktualizacją zbiorczą pliku ZIP. Nie musisz kopiować najnowszych plików do poprzedniego folderu. Można utworzyć nowy folder i użyć go.


## <a name="version-history"></a>Historia wersji
Najnowsza Site Recovery wersja narzędzia Planista wdrażania to 2,5.
Informacje na temat poprawek, które zostały dodane w poszczególnych aktualizacjach, możesz znaleźć na stronie [Historia wersji narzędzia Planista wdrażania usługi Site Recovery](./site-recovery-deployment-planner-history.md).

## <a name="next-steps"></a>Następne kroki
[Uruchamianie planisty wdrażania usługi Site Recovery](site-recovery-vmware-deployment-planner-run.md)
