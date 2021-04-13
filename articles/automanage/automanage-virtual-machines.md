---
title: Azure Automanage maszyn wirtualnych
description: Dowiedz się więcej Azure Automanage maszyn wirtualnych.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363380"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage maszyn wirtualnych

W tym artykule o Azure Automanage maszyn wirtualnych, które mają następujące zalety:

- Inteligentne dołączanie maszyn wirtualnych w celu wybrania najlepszych rozwiązań usług platformy Azure
- Automatycznie konfiguruje każdą usługę według najlepszych rozwiązań platformy Azure
- Monitoruje dryf i poprawia je po wykryciu
- Zapewnia proste środowisko (wskaż, kliknij, ustaw, zapomnij)


## <a name="overview"></a>Omówienie

Azure Automanage dla maszyn wirtualnych to usługa, która eliminuje konieczność odnajdywania, wiedzieć, jak do nich dołączać i jak skonfigurować niektóre usługi na platformie Azure, które byłyby korzystne dla maszyny wirtualnej. Te usługi są uważane za usługi najlepszych rozwiązań platformy Azure i pomagają zwiększyć niezawodność, bezpieczeństwo i zarządzanie maszynami wirtualnymi. Przykładowe usługi to [Azure Update Management](../automation/update-management/overview.md) i [Azure Backup](../backup/backup-overview.md).

Po dojecheniu maszyn wirtualnych do Azure Automanage, każda usługa najlepszych rozwiązań jest konfigurowana do zalecanych ustawień. Najlepsze rozwiązania są różne dla każdej z usług. Przykładem może być Azure Backup, gdzie najlepszym rozwiązaniem może być raz dziennie w celu przechowywania kopii zapasowej maszyny wirtualnej z okresem przechowywania przez sześć miesięcy.

Azure Automanage automatycznie monitoruje dryf i poprawia go po wykryciu. Oznacza to, że jeśli maszyna wirtualna jest do usługi Azure Automanage, nie tylko skonfigurujemy ją zgodnie z najlepszymi rozwiązaniami platformy Azure, ale również będziemy monitorować maszynę, aby zapewnić jej zgodność z tymi najlepszymi rozwiązaniami w całym cyklu życia. Jeśli maszyna wirtualna oddala się lub odbiega od tych praktyk (na przykład jeśli usługa została odseczętowana), poprawimy ją i ściągniemy maszynę z powrotem do żądanego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

Istnieje kilka wymagań wstępnych, które należy wziąć pod uwagę przed próbą włączenia Azure Automanage na maszynach wirtualnych.

- Obsługiwane [wersje systemu Windows Server](automanage-windows-server.md#supported-windows-server-versions) i [dystrybucje systemu Linux](automanage-linux.md#supported-linux-distributions-and-versions)
- Maszyny wirtualne muszą znajdować się w obsługiwanym regionie (patrz poniżej)
- Użytkownik musi mieć poprawne uprawnienia (zobacz poniżej)
- Automatycznemanage nie obsługuje obecnie subskrypcji piaskownicy

### <a name="supported-regions"></a>Obsługiwane regiony
Automanage obsługuje tylko maszyny wirtualne znajdujące się w następujących regionach:
* West Europe
* Europa Północna
* Central US
* East US
* Wschodnie stany USA 2
* Zachodnie stany USA
* Zachodnie stany USA 2
* Kanada Środkowa
* Zachodnio-środkowe stany USA
* South Central US
* Japan East
* Południowe Zjednoczone Królestwo
* Australia Wschodnia
* Australia Południowo-Wschodnia
* Southeast Asia

### <a name="required-rbac-permissions"></a>Wymagane uprawnienia RBAC
Twoje konto będzie wymagać nieco innych ról kontroli dostępu na podstawie ról w zależności od tego, czy jest wł. Automatycznemanage przy użyciu nowego konta automatycznego.

W przypadku włączania funkcji Automanage przy użyciu nowego konta automatycznegomanage:
* **Rola** właściciela w subskrypcjach zawierających maszyny wirtualne _**lub**_
* **Role** **współautora i administratora dostępu** użytkowników w subskrypcjach zawierających maszyny wirtualne

W przypadku włączania funkcji Automanage przy użyciu istniejącego konta automanage:
* **Rola** współautora w grupie zasobów zawierającej maszyny wirtualne

Konto Automatyczne zarządzanie będzie mieć uprawnienia **Współautor** i Współautor **zasad** zasobów do wykonywania akcji na maszynach automatycznie zarządzania.

> [!NOTE]
> Jeśli chcesz użyć funkcji Automatycznego wykonywania na maszynie wirtualnej, która jest połączona z obszarem roboczym w innej subskrypcji, musisz mieć uprawnienia opisane powyżej w każdej subskrypcji.

## <a name="participating-services"></a>Usługi biorące udział

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Inteligentne dołączanie usług.":::

Aby uzyskać pełną listę uczestniczących usług platformy Azure oraz obsługiwanych przez nie środowisk, zobacz następujące informacje:
- [Automanage for Linux](automanage-linux.md)
- [Automatyczne zarządzania dla systemu Windows Server](automanage-windows-server.md)

 Automatycznie dołączamy Cię do tych uczestniczących usług. Są one niezbędne w naszym dokumencie z najlepszymi rozwiązaniami, który można znaleźć w [naszym Cloud Adoption Framework.](/azure/cloud-adoption-framework/manage/azure-server-management)

Dla wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować dryf i mediate w przypadku wykrycia dryfu.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Włączanie automatycznegomanage dla maszyn wirtualnych w Azure Portal

W Azure Portal można włączyć automatyczne tworzenie na istniejącej maszynie wirtualnej lub podczas tworzenia nowej maszyny wirtualnej. Aby uzyskać zwięzłe instrukcje dotyczące tego procesu, zapoznaj się z przewodnikem Szybki start [Automanage for virtual machines (Automatycznemanage dla maszyn wirtualnych).](quick-create-virtual-machines-portal.md)

Jeśli po raz pierwszy w celu włączenia automatycznego zarządzania maszyną wirtualną, możesz wyszukać w witrynie azure Azure Portal najlepsze rozwiązania dotyczące automatycznego zarządzania maszynami **wirtualnymi platformy Azure.** Kliknij **pozycję Włącz na istniejącej** maszynie wirtualnej, wybierz maszyny wirtualne, które chcesz dołączyć, kliknij pozycję Wybierz, kliknij pozycję Włącz i wszystko gotowe.  

Jedyną koniecznością interakcji z tą maszyną wirtualną w celu zarządzania tymi usługami jest próba skorygowania maszyny wirtualnej, ale próba jej skorygowania zakończyła się niepowodzeniem. Jeśli pomyślnie naprawimy Twoją maszynę wirtualną, przywrócimy ją do zgodności bez nawet powiadamiania o tym. Aby uzyskać więcej informacji, [zobacz Status of VMs (Stan maszyn wirtualnych).](#status-of-vms)

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Włączanie automatycznegomanage dla maszyn wirtualnych przy użyciu Azure Policy
Możesz również włączyć automatycznemanage na maszynach wirtualnych na dużą skalę przy użyciu wbudowanych Azure Policy. Zasady mają efekt DeployIfNotExists, co oznacza, że wszystkie kwalifikujące się maszyny wirtualne znajdujące się w zakresie zasad zostaną automatycznie dołączane do najlepszych rozwiązań w zakresie automatycznego blokowania maszyn wirtualnych.

Bezpośredni link do zasad znajduje się [tutaj.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)

### <a name="how-to-apply-the-policy"></a>Jak zastosować zasady
1. Kliknij przycisk **Przypisz** podczas wyświetlania definicji zasad
1. Wybierz zakres, w którym chcesz zastosować zasady (może to być grupa zarządzania, subskrypcja lub grupa zasobów)
1. W **obszarze** Parametry określ parametry dla konta automatycznego zarządzania, profilu konfiguracji i efektu (efekt powinien zazwyczaj być określony jako DeployIfNotExists)
    1. Jeśli nie masz konta automatycznegomanage, musisz je [utworzyć.](./automanage-account.md)
1. W **obszarze Korygowanie** zaznacz pole wyboru "Kliknij zadanie korygowania". Spowoduje to wykonanie dołączania do funkcji automatycznegomanage.
1. Kliknij **pozycję Przeglądanie + tworzenie** i upewnij się, że wszystkie ustawienia wyglądają dobrze.
1. Kliknij pozycję **Utwórz**.

## <a name="environment-configuration"></a>Konfiguracja środowiska

W przypadku włączania funkcji Automanage dla maszyny wirtualnej wymagane jest środowisko. Środowiska są podstawą tej usługi. Definiują one usługi, do których dołączamy Twoje maszyny, i w pewnym stopniu określają konfigurację tych usług.

### <a name="default-environments"></a>Środowiska domyślne

Obecnie dostępne są dwa środowiska.

- **Środowisko tworzenia i testowania** jest przeznaczone dla maszyn dewelopera/testowania.
- **Środowisko** produkcyjne jest dla środowiska produkcyjnego.

Przyczyną tego jest to, że niektóre usługi są zalecane na podstawie uruchomionego obciążenia. Na przykład na maszynie produkcyjnej automatycznie dołączamy Cię do Azure Backup. Jednak w przypadku maszyny tworzenia i testowania usługa tworzenia kopii zapasowej byłaby niepotrzebnym kosztem, ponieważ maszyny tworzenia i testowania mają zwykle mniejszy wpływ na działalność biznesową.

### <a name="customizing-an-environment-using-preferences"></a>Dostosowywanie środowiska przy użyciu preferencji

Oprócz standardowych usług, do których cię dołączamy, umożliwiamy skonfigurowanie określonego podzestawu preferencji. Te preferencje są dozwolone w ramach zakresu opcji konfiguracji. Na przykład w przypadku Azure Backup umożliwimy zdefiniowanie częstotliwości wykonywania kopii zapasowej oraz dnia tygodnia, w którym się ona odbywa.

> [!NOTE]
> W środowisku tworzenia i testowania w ogóle nie będziemy kopię zapasową maszyny wirtualnej.

Ustawienia środowiska domyślnego można dostosować za pomocą preferencji. Dowiedz się, jak utworzyć preferencję [tutaj.](virtual-machines-custom-preferences.md)

> [!NOTE]
> Nie można zmienić konfiguracji zarządzania na maszynie wirtualnej, gdy jest włączona funkcja Automatycznego zarządzania. Konieczne będzie wyłączenie funkcji Automanage dla tej maszyny wirtualnej, a następnie ponowne włączenie funkcji Automanage z żądanym środowiskiem i preferencjami.

Pełną listę uczestniczących usług platformy Azure oraz informacje o tym, czy obsługują one preferencje, można znaleźć tutaj:
- [Automanage for Linux](automanage-windows-server.md)
- [Automatyczne zarządzania dla systemu Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage Account

Konto automatycznego zarządzania to kontekst zabezpieczeń lub tożsamość, w ramach której są wykonywane zautomatyzowane operacje. Zazwyczaj nie trzeba wybierać opcji Automatycznie zarządzanie kontem, ale jeśli istnieje scenariusz delegowania, w którym chcesz podzielić automatyczne zarządzanie zasobami (być może między dwoma administratorami systemu), opcja Automatycznie zarządzanie kontem w przepływie włączania umożliwia zdefiniowanie tożsamości platformy Azure dla każdego z tych administratorów.

Aby dowiedzieć się więcej na temat konta automatycznego i sposobu jego tworzenia, zapoznaj się z dokumentem [Automanage Account (Automatyczne tworzenie konta).](./automanage-account.md)

## <a name="status-of-vms"></a>Stan maszyn wirtualnych

W Azure Portal przejdź do strony Najlepszych rozwiązań dla maszyn wirtualnych **Automanage — Azure,** na której znajduje się lista wszystkich maszyn wirtualnych zarządzanych automatycznie. W tym miejscu zostanie wyświetlony ogólny stan każdej maszyny wirtualnej.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista skonfigurowanych maszyn wirtualnych.":::

Dla każdej maszyny wirtualnej na liście są wyświetlane następujące szczegóły: Nazwa, Środowisko, Preferencja konfiguracji, Stan, System operacyjny, Konto, Subskrypcja i Grupa zasobów.

W **kolumnie** Stan mogą być wyświetlane następujące stany:
- *W toku —* maszyna wirtualna została właśnie włączona i jest konfigurowana
- *Skonfigurowano* — maszyna wirtualna jest skonfigurowana i nie wykryto dryfu
- *Niepowodzenie —* maszyna wirtualna dryfowała i nie można było skorygować
- *Oczekujące* — maszyna wirtualna nie jest aktualnie uruchomiona, a funkcja Automatycznego uruchamiania podejmie próbę do dołączania lub korygowania maszyny wirtualnej, gdy będzie ona dalej uruchomiona

Jeśli stan to **Niepowodzenie,** *możesz* rozwiązać problemy z wdrożeniem za pomocą grupy zasobów, w ramach których znajduje się maszyna wirtualna. Przejdź do **grupy zasobów,** wybierz grupę zasobów,  kliknij pozycję **Wdrożenia** i zobacz tam stan Niepowodzenie wraz ze szczegółami błędu.


## <a name="disabling-automanage-for-vms"></a>Wyłączanie automatycznegomanage dla maszyn wirtualnych

Możesz zdecydować, że pewnego dnia wyłączysz opcję Automatycznemanage na niektórych maszyn wirtualnych. Na przykład na maszynie jest uruchomione bardzo poufne bezpieczne obciążenie i musisz zablokować je jeszcze bardziej niż platforma Azure zrobiłaby to naturalnie, dlatego musisz skonfigurować maszynę poza najlepszymi rozwiązaniami platformy Azure.

Aby to zrobić w Azure Portal, przejdź do strony Najlepszych rozwiązań w zakresie automatycznego zarządzania maszynami wirtualnymi platformy **Azure,** która zawiera listę wszystkich maszyn wirtualnych zarządzanych automatycznie. Zaznacz pole wyboru obok maszyny wirtualnej, którą chcesz wyłączyć za pomocą opcji Automatycznezazadanie, a następnie kliknij przycisk Wyłącz **automatycznemanagment.**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Wyłączanie funkcji Automanage na maszynie wirtualnej.":::

Przed wyrażeniem zgody na wyłączenie programu przeczytaj uważnie komunikaty w wyskakującym **oknie podręcznym.**

> [!NOTE]
> Wyłączenie automatycznegomanagementu na maszynie wirtualnej powoduje następujące zachowanie:
>
> - Konfiguracja maszyny wirtualnej i usług, które do tej maszyny wirtualnej są dołączane, nie zmieniają się.
> - Wszelkie opłaty naliczane przez te usługi pozostają rozliczane i nadal naliczane.
> - Automatyczne monitorowanie dryfu natychmiast zatrzymuje się.


Przede wszystkim nie będziemy wyłączać maszyny wirtualnej z żadnych usług, do których została ona do niego doskonfigurowana i skonfigurowana. W związku z tym wszelkie opłaty naliczane przez te usługi będą nadal rozliczane. W razie potrzeby konieczne będzie odjęcie tablicy. Każde zachowanie funkcji Automatycznegomanage zostanie natychmiast zatrzymane. Na przykład nie będziemy już monitorować dryfu maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono, że funkcja Automatycznego zarządzania maszynami wirtualnymi zapewnia środki, za pomocą których można wyeliminować konieczność poznawania, dołączania do i konfigurowania najlepszych rozwiązań usług platformy Azure. Ponadto, jeśli maszyna, która została do niego dowana do funkcji Automanage dla maszyn wirtualnych, oddala się od konfiguracji środowiska, automatycznie przywróci ją do zgodności.

Spróbuj w celu włączenia funkcji Automanage dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)