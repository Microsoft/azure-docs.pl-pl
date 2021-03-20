---
title: Planista wdrażania odzyskiwania po awarii funkcji Hyper-V z Azure Site Recovery
description: Dowiedz się więcej na temat Planista wdrażania usługi Azure Site Recovery odzyskiwania po awarii funkcji Hyper-V na platformie Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86528948"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Informacje o Planista wdrażania usługi Azure Site Recovery odzyskiwania po awarii funkcji Hyper-V na platformie Azure

Ten artykuł to podręcznik użytkownika planisty wdrażania usługi Azure Site Recovery dla wdrożeń produkcyjnych funkcji Hyper-V na platformie Azure.

Zanim zaczniesz chronić maszyny wirtualne funkcji Hyper-V za pomocą usługi Site Recovery, przydziel odpowiednią przepustowość zgodnie z częstotliwością dziennych zmian danych, aby osiągnąć założony cel punktu odzyskiwania, oraz przydziel odpowiednią ilość wolnego miejsca na każdym woluminie lokalnego magazynu funkcji Hyper-V.

Musisz także utworzyć właściwą liczbę kont magazynów platformy Azure odpowiedniego typu. Możesz utworzyć konta magazynu w warstwie Standardowa lub Premium, biorąc pod uwagę wzrost w zakresie źródłowych serwerów produkcyjnych z powodu zwiększania użycia w czasie. Wybierz typ magazynu dla maszyny wirtualnej w oparciu o charakterystyki obciążenia (np. operacje we/wy odczytu/zapisu na sekundę (IOPS) lub współczynnik zmian danych) oraz limity usługi Azure Site Recovery. 

Planista wdrażania usługi Azure Site Recovery to narzędzie wiersza polecenia na potrzeby scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure i z oprogramowania VMware do platformy Azure. To narzędzie pozwala zdalnie profilować maszyny wirtualne funkcji Hyper-V znajdujące się na wielu hostach funkcji Hyper-V (bez żadnego wpływu na środowisko produkcyjne), aby poznać wymagania dotyczące przepustowości i magazynu Azure Storage dla udanej replikacji oraz pracy w trybie failover lub testu pracy w trybie failover. Narzędzie możesz uruchomić bez instalowania składników usługi Azure Site Recovery w środowisku lokalnym. Jednak w celu uzyskania dokładnych wyników osiągniętej przepływności zaleca się uruchomienie planisty na serwerze z systemem Windows Server, którego konfiguracja sprzętowa jest taka sama jak konfiguracja jednego z serwerów funkcji Hyper-V używanych do włączania ochrony na potrzeby odzyskiwania po awarii. 

Narzędzie udostępnia następujące szczegóły:

**Ocena zgodności**

* Ocena uprawnień maszyny wirtualnej na podstawie liczby dysków, rozmiaru dysku, liczby operacji we/wy na sekundę, współczynnika zmian i pewnych charakterystyk maszyny wirtualnej.

**Zapotrzebowanie na przepustowość sieci w porównaniu z oceną celu punktu odzyskiwania**

* Szacowana przepustowość sieci wymagana na potrzeby replikacji przyrostowej
* Przepływność, którą usługa Azure Site Recovery może uzyskać między środowiskiem lokalnym i platformą Azure
* Cel punktu odzyskiwania, który można osiągnąć przy danej przepustowości
* Wpływ na wymagany cel punktu odzyskiwania w przypadku zapewnienia mniejszej przepustowości.

    
**Wymagania dotyczące infrastruktury platformy Azure**

* Wymagania dotyczące typu magazynu (konta w warstwie Standardowa lub Premium) dla poszczególnych maszyn wirtualnych
* Łączna liczba kont magazynu w warstwie Standardowa i Premium do skonfigurowania na potrzeby replikacji
* Propozycje nazw kont magazynu oparte na wskazówkach usługi Azure Storage
* Rozmieszczanie konta magazynu dla wszystkich maszyn wirtualnych
* Liczba rdzeni platformy Azure do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testu pracy w trybie failover w ramach subskrypcji
* Zalecany rozmiar poszczególnych maszyn wirtualnych platformy Azure

**Wymagania dotyczące infrastruktury lokalnej**
* Ilość wolnego miejsca wymagana na każdym woluminie funkcji Hyper-V do pomyślnej replikacji początkowej i replikacji różnicowej, dzięki której będziesz mieć pewność, że replikacja maszyny wirtualnej nie spowoduje niepożądanych przestojów w działaniu aplikacji produkcyjnych
* Maksymalna częstotliwość kopiowania do ustawienia na potrzeby replikacji funkcji Hyper-V

**Wskazówki dotyczące dzielenia na partie replikacji początkowej** 
* Liczba partii maszyn wirtualnych do użycia na potrzeby ochrony
* Lista maszyn wirtualnych w każdej partii
* Kolejność, w której mają być chronione partie
* Szacowany czas trwania replikacji początkowej każdej partii

**Szacowany koszt odzyskiwania po awarii do platformy Azure**
* Szacowany łączny koszt odzyskiwania po awarii do platformy Azure: koszt obliczeń, magazynu, sieci i licencji usługi Azure Site Recovery
* Szczegółowa analiza kosztów dla maszyny wirtualnej



>[!IMPORTANT]
>
>Użycie będzie prawdopodobnie zwiększać się wraz z upływem czasu, dlatego wszystkie poprzednie obliczenia narzędzia są wykonywane przy założeniu 30-procentowego współczynnika wzrostu wartości charakterystyk obciążenia i przy użyciu wartości 95. percentyla wszystkich metryk profilowania (operacje we/wy odczytu/zapisu na sekundę, współczynnik zmian danych itd.). Oba te elementy (współczynnik wzrostu i obliczenie wartości percentyla) można konfigurować. Więcej informacji na temat współczynnika wzrostu można znaleźć w sekcji „Zagadnienia związane ze współczynnikiem wzrostu”. Więcej informacji na temat wartości percentyla można znaleźć w sekcji „Wartość percentyla użyta w obliczeniach”.
>

## <a name="support-matrix"></a>Tabela obsługi

|**Kategorie** | **Z programu VMware do platformy Azure** |**Z funkcji Hyper-V do platformy Azure**|**Azure–Azure**|**Z funkcji Hyper-V do lokacji dodatkowej**|**Z oprogramowania VMware do lokacji dodatkowej**
--|--|--|--|--|--
Obsługiwane scenariusze |Tak|Tak|Nie|Tak*|Nie
Obsługiwana wersja | vCenter 6,7, 6,5, 6,0 lub 5,5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Obsługiwana konfiguracja|vCenter, ESXi| Klaster funkcji Hyper-V, host funkcji Hyper-V|NA|Klaster funkcji Hyper-V, host funkcji Hyper-V|NA|
Liczba serwerów, które mogą być profilowane, na uruchomione wystąpienie planisty wdrażania usługi Azure Site Recovery |Jeden (w tym samym czasie można profilować maszyny wirtualne należące do jednego serwera vCenter lub jednego serwera ESXi)|Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| NA |Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| NA

* Narzędzie jest przeznaczone głównie dla scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure. W przypadku odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej można używać go tylko do sprawdzania zaleceń po stronie źródła, takich jak wymagana przepustowość, wymagana ilość wolnego miejsca na każdym serwerze źródłowym funkcji Hyper-V oraz wartości dzielenia na partie replikacji początkowej i definicje partii.  Zignoruj zalecenia i koszty dotyczące platformy Azure z raportu. Ponadto nie można używać operacji uzyskiwania informacji o przepływności w scenariuszu odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej.

## <a name="prerequisites"></a>Wymagania wstępne
W przypadku funkcji Hyper-V narzędzie obejmuje trzy główne etapy: pobieranie listy maszyn wirtualnych, profilowanie i generowanie raportu. Jest też dostępny czwarty etap umożliwiający obliczanie tylko przepływności. W poniższej tabeli przedstawiono wymagania dotyczące serwera, na którym należy wykonać poszczególne etapy:

| Wymaganie dotyczące serwera | Opis |
|---|---|
|Pobieranie listy maszyn wirtualnych, profilowanie i pomiar przepływności |<ul><li>System operacyjny: Microsoft Windows Server 2016 lub Microsoft Windows Server 2012 R2 </li><li>Konfiguracja maszyny: 8 wirtualnych procesorów CPU, 16 GB pamięci RAM, dysk twardy o rozmiarze 300 GB</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Pakiet Microsoft Visual C++ Redistributable dla programu Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Dostęp przez Internet do platformy Azure (*. blob.core.windows.net) z tego serwera, port 443<br>[To jest opcjonalne. Możesz wybrać opcję udostępnienia przepustowości podczas ręcznego generowania raportu.]</li><li>Konto magazynu Azure</li><li>Dostęp administratora na serwerze</li><li>Minimalnie 100 GB wolnego miejsca na dysku (przy założeniu 1000 maszyn wirtualnych z średnio trzema dyskami na każdej z nich i profilowanych przez 30 dni)</li><li>Maszynę wirtualną, na której jest uruchomione narzędzie planisty wdrażania Azure Site Recovery, należy dodać do listy TrustedHosts wszystkich serwerów funkcji Hyper-V.</li><li>Wszystkie serwery funkcji Hyper-V, które mają być profilowane, muszą zostać dodane do listy TrustedHosts maszyny wirtualnej klienta, z której jest uruchamiane narzędzie. [Dowiedz się więcej na temat dodawania serwerów do listy TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> Narzędzie należy uruchomić z uprawnieniami administratora z poziomu programu PowerShell lub konsoli wiersza polecenia na kliencie</ul></ul>|
| Generowanie raportu | Dowolny komputer z systemem Windows lub Windows Server i programem Microsoft Excel 2013 lub nowszym |
| Uprawnienia użytkowników | Konto administratora umożliwiające dostęp do klastra lub hosta funkcji Hyper-V podczas operacji pobierania listy maszyn wirtualnych i profilowania.<br>Wszystkie hosty, dla których należy przeprowadzić profilowanie, muszą mieć konto administratora domeny z takimi samymi poświadczeniami, tj. nazwą użytkownika i hasłem
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Procedura dodawania serwerów do listy TrustedHosts
1. Lista TrustedHosts maszyny wirtualnej, z której ma zostać wdrożone narzędzie, musi zawierać wszystkie hosty do profilowania. Aby dodać klienta do listy Trustedhosts, uruchom poniższe polecenie w programie PowerShell z podwyższonym poziomem uprawnień na maszynie wirtualnej. Maszyna wirtualna może korzystać z systemu Windows Server 2012 R2 lub Windows Server 2016. 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. Każdy host funkcji Hyper-V, dla którego ma zostać przeprowadzone profilowanie, musi zawierać następujące elementy:

    a. Maszyna wirtualna, na której zostanie uruchomione narzędzie, na liście TrustedHosts. Uruchom poniższe polecenie w programie PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V.

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. Włączona komunikacja zdalna programu PowerShell.

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Pobieranie i wyodrębnianie narzędzia planisty wdrażania

1.  Pobierz najnowszą wersję [planisty wdrażania Azure Site Recovery](https://aka.ms/asr-deployment-planner).
Narzędzie jest spakowane w folderze ZIP. To samo narzędzie obsługuje scenariusze odzyskiwania po awarii z oprogramowania VMware do platformy Azure i z funkcji Hyper-V do platformy Azure. Możesz też używać tego narzędzia w scenariuszu odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej, ale w takiej sytuacji zignoruj zalecenie dotyczące infrastruktury platformy Azure z raportu.

1.  Skopiuj folder ZIP na serwer z systemem Windows Server, z którego chcesz uruchomić narzędzie. Możesz uruchomić narzędzie w systemie Windows Server 2012 R2 lub Windows Server 2016. Serwer musi mieć dostęp do sieci, aby móc łączyć się z klastrem funkcji Hyper-V lub hostem funkcji Hyper-V zawierającym maszyny wirtualne do profilowania. Zaleca się użycie na maszynie wirtualnej, na której zostanie uruchomione narzędzie, takiej samej konfiguracji sprzętowej jak konfiguracja serwera funkcji Hyper-V, który chcesz chronić. Dzięki takiej konfiguracji masz pewność, że osiągnięta przepływność zgłaszana przez narzędzie jest zgodna z rzeczywistą przepływnością, którą usługa Azure Site Recovery może osiągnąć podczas replikacji. Obliczanie przepływności zależy od dostępnej przepustowości sieci na serwerze i konfiguracji sprzętu (procesor CPU, magazyn itd.) serwera. Obliczana jest przepływność między serwerem, na którym jest uruchomione narzędzie, i platformą Azure. Jeśli konfiguracja sprzętowa serwera różni się od konfiguracji serwera funkcji Hyper-V, dane osiągniętej przepływności zgłaszanej przez narzędzie będą niedokładne.
Zalecana konfiguracja maszyny wirtualnej: 8 wirtualnych procesorów CPU, 16 GB pamięci RAM, dysk twardy o rozmiarze 300 GB.

1.  Wyodrębnij folder ZIP.
Folder zawiera wiele plików i podfolderów. Plik wykonywalny nosi nazwę ASRDeploymentPlanner.exe i znajduje się w folderze nadrzędnym.

Przykład: skopiuj plik zip na dysk E:\ i wyodrębnij go. Planner_v2.3.zip wdrażania E:\ASR

Planner_v2.3\ASRDeploymentPlanner.exe wdrażania E:\ASR

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Aktualizowanie planisty wdrażania do najnowszej wersji

Najnowsze aktualizacje są podsumowane w [historii wersji](site-recovery-deployment-planner-history.md)planista wdrażania.

Jeśli masz wcześniejszą wersję planisty wdrażania, wykonaj jedną z następujących czynności:
 * Jeśli najnowsza wersja nie zawiera poprawki profilowania i profilowanie jest już w toku w bieżącej wersji planisty, kontynuuj profilowanie.
 * Jeśli najnowsza wersja zawiera poprawkę profilowania, zalecamy zatrzymanie profilowania w bieżącej wersji, a następnie jego ponowne uruchomienie w nowej wersji.


  >[!NOTE]
  >
  >W przypadku uruchamiania profilowania w nowej wersji przekaż taką samą ścieżkę katalogu wyjściowego, aby narzędzie dołączało dane profilu do istniejących plików. Do wygenerowania raportu zostanie użyty kompletny zestaw profilowanych danych. Jeśli przekażesz inny katalog danych wyjściowych, zostaną utworzone nowe pliki, a stare profilowane dane nie będą używane podczas generowania raportu.
  >
  >Każdy nowy planista wdrożenia jest aktualizacją zbiorczą pliku ZIP. Nie musisz kopiować najnowszych plików do poprzedniego folderu. Można utworzyć nowy folder i użyć go.

## <a name="version-history"></a>Historia wersji
Najnowsza wersja narzędzia Planista wdrażania usługi Azure Site Recovery to 2,5.
Informacje na temat poprawek, które są dodawane do poszczególnych aktualizacji, znajdują się na stronie [historia wersji planista wdrażania usługi Azure Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) .


## <a name="next-steps"></a>Następne kroki
* [Uruchom planistę wdrażania](./hyper-v-deployment-planner-run.md).
