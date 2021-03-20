---
title: Samouczek dotyczący konfigurowania odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu Azure Site Recovery
description: W tym samouczku skonfigurujesz odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w innym regionie platformy Azure przy użyciu usługi Site Recovery.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656873"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Samouczek: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md). W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Weryfikowanie ustawień i uprawnień platformy Azure
> * Przygotuj maszyny wirtualne, które chcesz replikować
> * Tworzenie magazynu usługi Recovery Services
> * Włącz replikację maszyny wirtualnej

Po włączeniu replikacji maszyny wirtualnej w celu skonfigurowania odzyskiwania po awarii, rozszerzenie usługi mobilności Site Recovery zostanie zainstalowane na maszynie wirtualnej i zostanie zarejestrowane przy użyciu Azure Site Recovery. Podczas replikacji zapisy z dysku maszyny wirtualnej są wysyłane do konta magazynu pamięci podręcznej w regionie źródłowym. Dane są wysyłane z lokalizacji do regionu docelowego, a punkty odzyskiwania są generowane na podstawie danych. Po przełączeniu maszyny wirtualnej w tryb failover podczas odzyskiwania po awarii punkt odzyskiwania jest używany do przywracania maszyny wirtualnej w regionie docelowym.

> [!NOTE]
> Samouczki zawierają instrukcje z najprostszymi ustawieniami domyślnymi. Jeśli chcesz skonfigurować odzyskiwanie awaryjne maszyny wirtualnej platformy Azure z dostosowanymi ustawieniami, przejrzyj [ten artykuł](azure-to-azure-how-to-enable-replication.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem:

- [Przejrzyj Obsługiwane regiony](azure-to-azure-support-matrix.md#region-support). Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure można skonfigurować między dwoma regionami w tej samej lokalizacji geograficznej.
- Potrzebna jest co najmniej jedna maszyna wirtualna platformy Azure. Sprawdź, czy maszyny wirtualne z [systemem Windows](azure-to-azure-support-matrix.md#windows) lub [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) są obsługiwane.
- Przejrzyj wymagania dotyczące [obliczeń](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [magazynu](azure-to-azure-support-matrix.md#replicated-machines---storage)i [sieci](azure-to-azure-support-matrix.md#replicated-machines---networking) maszyny wirtualnej.
- Ten samouczek zakłada, że maszyny wirtualne nie są szyfrowane. Jeśli chcesz skonfigurować odzyskiwanie po awarii dla szyfrowanych maszyn wirtualnych, [postępuj zgodnie z tym artykułem](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Sprawdź ustawienia platformy Azure

Sprawdź uprawnienia i ustawienia w regionie docelowym.

### <a name="check-permissions"></a>Sprawdź uprawnienia

Twoje konto platformy Azure wymaga uprawnień do tworzenia magazynu Recovery Services i tworzenia maszyn wirtualnych w regionie docelowym.

- Jeśli właśnie została utworzona bezpłatna subskrypcja platformy Azure, jesteś administratorem konta i nie są potrzebne żadne dalsze działania.
- Jeśli nie jesteś administratorem, możesz skontaktować się z administratorem w celu uzyskania potrzebnych uprawnień.
    - **Utwórz magazyn**: uprawnienia administratora lub właściciela subskrypcji. 
    - **Zarządzanie operacjami Site Recovery w magazynie**: Wbudowana rola platformy Azure *Site Recovery współautor* .
    - **Tworzenie maszyn wirtualnych platformy Azure w regionie docelowym**: Wbudowana rola *współautor maszyny wirtualnej* lub określone uprawnienia do:
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapisz na koncie usługi Azure Storage.
        - Zapisz na dysku zarządzanym przez platformę Azure.

### <a name="verify-target-settings"></a>Weryfikuj ustawienia docelowe

Podczas awaryjnego odzyskiwania po awarii z regionu źródłowego maszyny wirtualne są tworzone w regionie docelowym. 

Sprawdź, czy Twoja subskrypcja ma wystarczającą ilość zasobów w regionie docelowym. Musisz mieć możliwość tworzenia maszyn wirtualnych o rozmiarach, które pasują do maszyn wirtualnych w regionie źródłowym. Podczas konfigurowania odzyskiwania po awarii Site Recovery wybiera ten sam rozmiar (lub najbliższy możliwy rozmiar) dla docelowej maszyny wirtualnej.


## <a name="prepare-vms"></a>Przygotowywanie maszyn wirtualnych

Upewnij się, że maszyny wirtualne mają łączność wychodzącą i najnowsze certyfikaty główne. 


### <a name="set-up-vm-connectivity"></a>Konfigurowanie łączności maszyny wirtualnej

Maszyny wirtualne, które mają być replikowane, wymagają łączności sieciowej wychodzącej.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

#### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) w celu kontrolowania łączności, utwórz reguły sieciowej grupy zabezpieczeń oparte na znacznikach usług, które zezwalają na ruch wychodzący HTTPS do portu 443 dla tych [tagów usługi](../virtual-network/service-tags-overview.md#available-service-tags)(grupy adresów IP):

**Tag** | **Zezwalaj** 
--- | ---
Tag magazynu  |Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej.   
Tag usługi Azure AD | Zezwala na dostęp do wszystkich adresów IP odnoszące się do usługi Azure AD.   
Tag EventsHub | Umożliwia dostęp do monitorowania Site Recovery.  
Tag AzureSiteRecovery | Zezwala na dostęp do usługi Site Recovery w dowolnym regionie.   
Tag GuestAndHybridManagement | Użyj, jeśli chcesz automatycznie uaktualnić agenta mobilności Site Recovery, który jest uruchomiony na maszynach wirtualnych obsługujących replikację.

[Dowiedz się więcej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) na temat wymaganych tagów i przykładów tagowania.

### <a name="verify-vm-certificates"></a>Weryfikowanie certyfikatów maszyn wirtualnych

Sprawdź, czy maszyny wirtualne mają najnowsze certyfikaty główne. W przeciwnym razie nie można zarejestrować maszyny wirtualnej z Site Recovery ze względu na ograniczenia zabezpieczeń.

- **Maszyny wirtualne z systemem Windows**: Zainstaluj wszystkie najnowsze aktualizacje systemu Windows na maszynie wirtualnej, dzięki czemu wszystkie zaufane certyfikaty główne znajdują się na komputerze. W środowisku odłączonym postępuj zgodnie ze standardowymi procesami dotyczącymi Windows Update i aktualizacji certyfikatów.
- **Maszyny wirtualne z systemem Linux**: Postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora systemu Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i listy odwołania certyfikatów (CRL).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Utwórz magazyn Recovery Services w dowolnym regionie, z wyjątkiem regionu źródłowego, z którego chcesz replikować maszyny wirtualne.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W polu wyszukiwania wpisz *odzyskiwanie*. W obszarze **usługi** wybierz pozycję **magazyny Recovery Services**.

    ![Wyszukaj Recovery Services magazyny](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. W obszarze **magazyny Recovery Services** wybierz pozycję **Dodaj**.
4. W obszarze **Tworzenie magazynu Recovery Services**  >  **podstawowe** wybierz subskrypcję, w której chcesz utworzyć magazyn.
5. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów dla magazynu lub Utwórz nową.
6. W polu **Nazwa magazynu** Określ przyjazną nazwę identyfikującą magazyn.
7. W **obszarze region** wybierz region świadczenia usługi Azure, w którym ma zostać umieszczony magazyn. [Sprawdź Obsługiwane regiony](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Wybierz pozycję **Przejrzyj i utwórz**.

   ![Ustawienia magazynu na stronie na potrzeby tworzenia nowego magazynu](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. Na stronie **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

10. Rozpoczęcie wdrażania magazynu. Postępuj zgodnie z postępem w powiadomieniach.
11. Po wdrożeniu magazynu wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby zapisać go na potrzeby szybkiego odwoływania się do niego. Wybierz pozycję **Przejdź do zasobu** , aby otworzyć nowy magazyn. 
    
    ![Przyciski umożliwiające otwarcie magazynu po wdrożeniu i przypinanie do pulpitu nawigacyjnego](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Włącz Site Recovery

W obszarze Ustawienia magazynu wybierz pozycję **włącz Site Recovery**.

![Wybór do włączenia Site Recovery w magazynie](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Włączanie replikacji

Wybierz ustawienia źródłowe i Włącz replikację maszyny wirtualnej. 

### <a name="select-source-settings"></a>Wybierz ustawienia źródła

1. Na stronie > magazynu **Site Recovery** w obszarze **usługi Azure Virtual Machines** wybierz pozycję **Włącz replikację**.

    ![Wybór, aby włączyć replikację dla maszyn wirtualnych platformy Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. W **obszarze źródłowa** >  **lokalizacja źródłowa** wybierz źródłowy region platformy Azure, w którym są aktualnie uruchomione maszyny wirtualne.
3. W **modelu wdrażania maszyn wirtualnych platformy Azure** Pozostaw domyślne ustawienie **Menedżer zasobów** .
4. W obszarze **subskrypcja źródłowa** wybierz subskrypcję, w której działają maszyny wirtualne. Możesz wybrać dowolną subskrypcję znajdującą się w tej samej dzierżawie usługi Azure Active Directory (AD) jako magazyn.
5. W obszarze **źródłowa Grupa zasobów** wybierz grupę zasobów zawierającą maszyny wirtualne.
6. W przypadku **odzyskiwania po awarii między strefami dostępności** pozostaw ustawienie domyślne **no** .

     ![Konfiguracja źródła](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Wybierz opcję **Dalej**.

### <a name="select-the-vms"></a>Wybieranie maszyn wirtualnych

Site Recovery pobiera maszyny wirtualne skojarzone z wybraną subskrypcją/grupą zasobów.

1. W **Virtual Machines** Wybierz Maszyny wirtualne, które chcesz włączyć na potrzeby odzyskiwania po awarii.

     ![Strona do wybierania maszyn wirtualnych do replikacji](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Wybierz opcję **Dalej**.

### <a name="review-replication-settings"></a>Przegląd ustawień replikacji

1. W obszarze **Ustawienia replikacji** Sprawdź ustawienia. Site Recovery tworzy domyślne ustawienia/zasady dla regionu docelowego. Na potrzeby tego samouczka używane są ustawienia domyślne.
2. Wybierz pozycję **Włącz replikację**.

    ![Strona umożliwiająca dostosowanie ustawień i włączenie replikacji](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Śledź postęp replikacji w powiadomieniach.

     ![Śledzenie postępu w powiadomieniach ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ śledzenie pomyślnej replikacji](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Maszyny wirtualne, które włączasz, są wyświetlane na stronie Magazyn > **zreplikowane elementy** .

    ![Maszyna wirtualna na stronie zreplikowane elementy](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono funkcję odzyskiwania po awarii dla maszyny wirtualnej platformy Azure. Teraz uruchom przechodzenie do szczegółów, aby sprawdzić, czy tryb failover działa zgodnie z oczekiwaniami.

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md)
