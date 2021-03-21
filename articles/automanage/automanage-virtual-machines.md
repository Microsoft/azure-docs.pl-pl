---
title: Usługa Azure automanage dla maszyn wirtualnych
description: Dowiedz się więcej o usłudze Azure automanage dla maszyn wirtualnych.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643536"
---
# <a name="azure-automanage-for-virtual-machines"></a>Usługa Azure automanage dla maszyn wirtualnych

W tym artykule opisano informacje o usłudze Azure automanage dla maszyn wirtualnych, które mają następujące zalety:

- Inteligentne dołączanie maszyn wirtualnych w celu wybrania usług platformy Azure z najlepszymi rozwiązaniami
- Automatycznie konfiguruje poszczególne usługi według najlepszych rozwiązań dotyczących platformy Azure
- Monitoruje do dryfowania i poprawia dla niego po wykryciu
- Zapewnia proste środowisko (punkt, kliknij, ustaw, zapomnij)


## <a name="overview"></a>Omówienie

Azure automanage dla maszyn wirtualnych to usługa, która eliminuje potrzebę odnajdowania, znajomości sposobu dołączania i konfigurowania określonych usług na platformie Azure, które byłyby korzystne dla Twojej maszyny wirtualnej. Te usługi są uznawane za usługi Azure Best Practices i pomagają w ulepszaniu niezawodności, zabezpieczeń i zarządzania maszynami wirtualnymi. Przykładowe usługi obejmują [Update Management platformy Azure](../automation/update-management/overview.md) i [Azure Backup](../backup/backup-overview.md).

Po dołączeniu maszyn wirtualnych do usługi Azure automanage każda usługa Best Practices została skonfigurowana pod kątem zalecanych ustawień. Najlepsze rozwiązania są różne dla każdej usługi. Przykładem może być Azure Backup, w którym najlepszym rozwiązaniem może być utworzenie kopii zapasowej maszyny wirtualnej raz dziennie i okres przechowywania wynoszący sześć miesięcy.

Usługa Azure automanage automatycznie monitoruje również pod kątem dryfowania i naprawia je w przypadku wykrycia. Co oznacza to, że Twoja maszyna wirtualna została dołączona do usługi Azure automanage, nie tylko skonfigurujemy ją na potrzeby najlepszych rozwiązań dotyczących platformy Azure, ale będziemy monitorować swoją maszynę, aby upewnić się, że będzie ona nadal zgodna z najlepszymi rozwiązaniami w całym cyklu życia. Jeśli maszyna wirtualna wykonuje dryf lub odróżni się od tych praktyk (na przykład jeśli usługa jest offboarded), poprawimy ją i ściągają maszynę z powrotem do żądanego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed próbą włączenia usługi Azure automanage na maszynach wirtualnych należy wziąć pod uwagę kilka wymagań wstępnych.

- Obsługiwane [wersje systemów Windows Server](automanage-windows-server.md#supported-windows-server-versions) i [Linux dystrybucje](automanage-linux.md#supported-linux-distributions-and-versions)
- Maszyny wirtualne muszą znajdować się w obsługiwanym regionie (zobacz poniżej).
- Użytkownik musi mieć odpowiednie uprawnienia (patrz poniżej)
- W tej chwili usługa autozarządzania nie obsługuje subskrypcji piaskownicy

### <a name="supported-regions"></a>Obsługiwane regiony
Autozarządzanie obsługuje tylko maszyny wirtualne znajdujące się w następujących regionach:
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

### <a name="required-rbac-permissions"></a>Wymagane uprawnienia RBAC
Twoje konto będzie wymagało nieco innych ról RBAC w zależności od tego, czy włączasz Autozarządzanie przy użyciu nowego konta autozarządzania.

Jeśli włączysz Autozarządzanie przy użyciu nowego konta autozarządzania:
* Rola **właściciela** w subskrypcjach zawierających maszyny wirtualne _**lub**_
* **Współautorzy** i role **administratora dostępu użytkowników** w subskrypcjach zawierających Twoje maszyny wirtualne

Jeśli włączysz Autozarządzanie przy użyciu istniejącego konta autozarządzania:
* Rola **współautor** w grupie zasobów zawierającej maszyny wirtualne

> [!NOTE]
> Jeśli chcesz używać autozarządzania na maszynie wirtualnej, która jest połączona z obszarem roboczym w innej subskrypcji, musisz mieć uprawnienia opisane powyżej dla każdej subskrypcji.

## <a name="participating-services"></a>Usługi uczestniczące

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Usługi inteligentnego dołączania.":::

Pełną listę uczestniczących usług platformy Azure, a także ich obsługiwane środowisko, można znaleźć w następujących tematach:
- [Autozarządzanie dla systemu Linux](automanage-linux.md)
- [Autozarządzanie dla systemu Windows Server](automanage-windows-server.md)

 Zostanie automatycznie dołączony do tych usług uczestniczących. Są one istotne dla naszych najlepszych rozwiązań, które można znaleźć w naszej [strukturze wdrożenia chmury](/azure/cloud-adoption-framework/manage/azure-server-management).

W przypadku wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować pod kątem dryfowania i korygować w przypadku wykrycia dryfu.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Włączanie autozarządzania dla maszyn wirtualnych w Azure Portal

W Azure Portal można włączyć Autozarządzanie na istniejącej maszynie wirtualnej lub podczas tworzenia nowej maszyny wirtualnej. Zwięzłe kroki tego procesu można znaleźć w sekcji [Autozarządzanie dla maszyn wirtualnych — szybki start](quick-create-virtual-machines-portal.md).

Jeśli po raz pierwszy włączysz Autozarządzanie dla maszyny wirtualnej, możesz wyszukać w Azure Portal do **autozarządzania — najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure**. Kliknij pozycję **Włącz na istniejącej maszynie wirtualnej**, wybierz maszyny wirtualne, które chcesz dołączyć, kliknij pozycję **Wybierz**, kliknij pozycję **Włącz**, a wszystko gotowe.

Jedyną czynnością, którą należy wykonać, aby móc korzystać z tej maszyny wirtualnej w celu zarządzania tymi usługami, jest to, że podjęto próbę skorygowania maszyny wirtualnej, ale to nie powiodło się. W przypadku pomyślnego skorygowania maszyny wirtualnej nastąpi powrót do zgodności bez nawet powiadamiania użytkownika. Aby uzyskać więcej informacji, zobacz [stan maszyn wirtualnych](#status-of-vms).


## <a name="environment-configuration"></a>Konfiguracja środowiska

Gdy włączysz Autozarządzanie dla maszyny wirtualnej, jest wymagane środowisko. Środowiska stanowią podstawę tej usługi. Określają, które usługi są używane do dołączania maszyn do i do pewnego stopnia.

### <a name="default-environments"></a>Środowiska domyślne

Dostępne są dwa środowiska.

- Środowisko **deweloperskie/testowe** jest przeznaczone dla maszyn deweloperskich i testowych.
- Środowisko **produkcyjne** jest przeznaczone do produkcji.

Przyczyną tego odróżniania jest fakt, że pewne usługi są zalecane w oparciu o uruchomione obciążenie. Na przykład na komputerze produkcyjnym automatycznie dołączysz Cię do Azure Backup. Jednak w przypadku maszyny deweloperskiej/testowej usługa tworzenia kopii zapasowych będzie niezbędny koszt, ponieważ maszyny deweloperskie/testowe zazwyczaj obniżają wpływ na działalność biznesową.

### <a name="customizing-an-environment-using-preferences"></a>Dostosowywanie środowiska przy użyciu preferencji

Oprócz usług standardowych, do których dodaliśmy Cię, umożliwiamy skonfigurowanie pewnego podzestawu preferencji. Te preferencje są dozwolone w ramach różnych opcji konfiguracji. Na przykład w przypadku Azure Backup będzie można zdefiniować częstotliwość tworzenia kopii zapasowych i dzień tygodnia, w którym występuje.

> [!NOTE]
> W środowisku deweloperskim/testowym nie zostanie utworzona kopia zapasowa maszyny wirtualnej.

Ustawienia domyślnego środowiska można dostosować za pomocą preferencji. Dowiedz się, jak utworzyć [tutaj](virtual-machines-custom-preferences.md)preferencję.

> [!NOTE]
> Nie można zmienić konfiguracji enivonrment na maszynie wirtualnej, gdy jest włączona funkcja autozarządzania. Należy wyłączyć Autozarządzanie dla tej maszyny wirtualnej, a następnie ponownie włączyć funkcję Autozarządzanie przy użyciu wymaganego środowiska i preferencji.

Aby zapoznać się z pełną listą uczestniczących usług platformy Azure i jeśli są one obsługiwane, zobacz tutaj:
- [Autozarządzanie dla systemu Linux](automanage-windows-server.md)
- [Autozarządzanie dla systemu Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Autozarządzanie kontem

Konto autozarządzaj jest kontekstem zabezpieczeń lub tożsamością, w której występują operacje zautomatyzowane. Zazwyczaj opcja konta automanage nie jest wymagana do wybrania, ale jeśli w scenariuszu delegowania chcesz podzielić zautomatyzowane zarządzanie zasobami (być może między dwoma administratorami systemu), ta opcja umożliwia zdefiniowanie tożsamości platformy Azure dla każdego z tych administratorów.

W Azure Portal środowiska, gdy włączasz funkcję autozarządzania na maszynach wirtualnych, istnieje zaawansowana lista rozwijana w bloku **Włączanie usługi Azure VM Best Practice** , który umożliwia przypisanie lub ręczne utworzenie konta Autozarządzanie.

Kontu autozarządzania zostanie przyznany **współautor** i role **współautor zasad zasobów** do subskrypcji zawierających maszyny, które zostaną dołączone do autozarządzania. Możesz użyć tego samego konta autozarządzania na maszynach w wielu subskrypcjach, co spowoduje przyznanie uprawnienia do autozarządzania **współautor** konta i **współautor zasad zasobów** dla wszystkich subskrypcji.

Jeśli maszyna wirtualna jest połączona z obszarem roboczym Log Analytics w innej subskrypcji, do konta automanage zostanie przyznany **współautor i** **współautor zasad zasobów** w tej innej subskrypcji.

Jeśli włączysz Autozarządzanie przy użyciu nowego konta Autozarządzanie, potrzebne są następujące uprawnienia do subskrypcji: rola **właściciela** lub **współautor** wraz z rolami **administratora dostępu użytkowników** .

Jeśli włączysz Autozarządzanie przy użyciu istniejącego konta Autozarządzanie, musisz mieć rolę **współautor** w grupie zasobów zawierającej maszyny wirtualne.

> [!NOTE]
> Po wyłączeniu najlepszych rozwiązań z zakresu autozarządzania uprawnienia konta Autozarządzanie dla wszystkich skojarzonych subskrypcji pozostaną. Ręcznie usuń uprawnienia, przechodząc do strony usługi IAM subskrypcji lub Usuń konto autozarządzaj. Konta automanage nie można usunąć, jeśli nadal zarządza wszystkimi maszynami.


## <a name="status-of-vms"></a>Stan maszyn wirtualnych

W Azure Portal przejdź do strony " **Autozarządzanie" najlepszych rozwiązań dotyczących maszyn wirtualnych platformy Azure** , w której znajduje się lista wszystkich maszyn wirtualnych zarządzanych przez program. W tym miejscu zobaczysz ogólny stan każdej maszyny wirtualnej.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista skonfigurowanych maszyn wirtualnych.":::

Dla każdej z wymienionych maszyn wirtualnych wyświetlane są następujące informacje: nazwa, środowisko, preferencje konfiguracji, stan, system operacyjny, konto, subskrypcja i Grupa zasobów.

W kolumnie **stan** można wyświetlić następujące stany:
- *W toku* — maszyna wirtualna została właśnie włączona i jest konfigurowana
- *Skonfigurowane* — maszyna wirtualna jest skonfigurowana i nie wykryto dryfu
- *Niepowodzenie* — maszyna wirtualna została przedryfna i nie można jej skorygować
- *Oczekujące* — maszyna wirtualna nie jest obecnie uruchomiona, a Autozarządzanie podejmie próbę dołączenia lub skorygowania maszyny wirtualnej po jej następnym uruchomieniu

Jeśli zobaczysz **stan** jako *Niepowodzenie*, możesz rozwiązać problemy ze wdrożeniem za pomocą grupy zasobów, w której znajduje się maszyna wirtualna. Przejdź do pozycji **grupy zasobów**, wybierz grupę zasobów, kliknij pozycję **wdrożenia** , a następnie  Zobacz szczegóły błędu.


## <a name="disabling-automanage-for-vms"></a>Wyłączanie autozarządzania dla maszyn wirtualnych

Aby wyłączyć funkcję autozarządzania na niektórych maszynach wirtualnych, możesz wybrać jeden dzień. Na przykład na komputerze jest uruchomione niepoufne bezpieczne obciążenie i należy zablokować je nawet jeszcze dalej niż na platformie Azure, dlatego należy skonfigurować komputer poza najlepszymi rozwiązaniami dotyczącymi platformy Azure.

Aby to zrobić, w Azure Portal przejdź do strony **Autozarządzanie — najlepsze rozwiązania dotyczącej maszyn wirtualnych platformy Azure** , która zawiera listę wszystkich maszyn wirtualnych zarządzanych przez program. Zaznacz pole wyboru obok maszyny wirtualnej, która ma zostać wyłączona z opcji Autozarządzanie, a następnie kliknij przycisk **Wyłącz automanagment** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Wyłączanie autozarządzania na maszynie wirtualnej.":::

Uważnie przeczytaj wiadomości w wyskakującym okienku, aby wyrazić zgodę na **wyłączenie**.

> [!NOTE]
> Wyłączenie autozarządzania w maszynie wirtualnej powoduje następujące zachowanie:
>
> - Konfiguracja maszyny wirtualnej i usług, które nie zostały zmienione.
> - Opłaty naliczane przez te usługi pozostają w obciążeniu i nadal będą naliczane.
> - Wszystkie zachowania autozarządzaj natychmiast Zatrzymaj.


Najpierw nie należy wyłączać maszyny wirtualnej z dowolnych usług, które zostały do niej dołączone i skonfigurowane. W związku z tym wszelkie opłaty naliczane przez te usługi będą nadal obciążane opłatami. Jeśli to konieczne, należy w razie potrzeby wyłączyć. Każde zachowanie autozarządzania zostanie natychmiast zatrzymane. Na przykład nie będziemy już monitorować maszyny wirtualnej pod kątem dryfowania.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, że Autozarządzanie maszynami wirtualnymi zapewnia rozwiązanie, dla którego można wyeliminować konieczność znajomości, dołączania i konfigurowania usług Azure Best Practices. Ponadto, jeśli komputer, do którego chcesz przeprowadzić Autozarządzanie w przypadku przechodzenia maszyn wirtualnych z konfiguracji środowiska, zostanie automatycznie przywrócony do zgodności.

Spróbuj włączyć Autozarządzanie dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)