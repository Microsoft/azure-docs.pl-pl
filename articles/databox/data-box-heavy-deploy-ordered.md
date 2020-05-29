---
title: Samouczek dotyczący zamawiania urządzenia Azure Data Box Heavy | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących wdrażania oraz sposobie zamawiania urządzenia Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 91d78975106299c3b3a6df6d8dee05f337dd339a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742990"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Samouczek: Zamawianie urządzenia Azure Data Box Heavy


Azure Data Box Heavy to hybrydowe rozwiązanie, które umożliwia szybkie, łatwe i bezproblemowe importowanie danych lokalnych na platformę Azure. Przesyłasz swoje dane na urządzenie magazynujące dostarczane przez firmę Microsoft, którego przybliżona pojemność do wykorzystania to 770 TB, a następnie odsyłasz urządzenie z powrotem do firmy Microsoft. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania urządzenia Azure Data Box Heavy. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące urządzenia Data Box Heavy
> * Zamawianie urządzenia Data Box Heavy
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z usługą i urządzeniem Data Box.

### <a name="for-installation-site"></a>Miejsce instalacji

Przed rozpoczęciem upewnij się, że:

- Urządzenie mieści się w standardowe otwory drzwiowe i przejścia. Upewnij się jednak, że urządzenie zmieści się we wszystkich otworach drzwiowych, przez które będzie transportowane. Wymiary urządzenia — szerokość: 66 cm (26 cali) długość: 122 cm (48 cali) wysokość: 71 cm (28 cali).
- Jeśli urządzenie ma zostać zainstalowane na innym piętrze budynku niż parter, musisz mieć możliwość przetransportowania urządzenia za pomocą windy lub rampy. Urządzenie ma masę około 227 kg (500 funtów).
- Upewnij się, że masz dostęp do płaskiej powierzchni w centrum danych znajdującej się blisko dostępnego połączenia sieciowego, gdzie umieścisz urządzenie.

### <a name="for-service"></a>Na potrzeby usługi

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:
- Urządzenie zostało rozpakowane.
- Należy posiadać komputer hosta podłączony do sieci centrum danych. Za pomocą urządzenia Data Box Heavy zostaną skopiowane dane z tego komputera. Na komputerze hosta musi działać obsługiwany system operacyjny, zgodnie z opisem w [wymaganiach dotyczących systemu Azure Data Box Heavy](data-box-system-requirements.md).
- Aby połączyć się z lokalnym interfejsem użytkownika i skonfigurować urządzenie, potrzebny jest komputer przenośny i kabel RJ-45. Za pomocą komputera przenośnego skonfigurujesz jednorazowo poszczególne węzły na urządzeniu.
- Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE.
- Potrzebny jest jeden kabel o przepustowości 40 Gb/s lub po jednym kablu o przepustowości 10 Gb/s dla każdego węzła urządzenia. Wybierz kable, które są zgodne z interfejsem sieciowym [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - W przypadku kabla o przepustowości 40 Gb/s po stronie urządzenia musi być on zgodny ze standardem QSFP+.
    - W przypadku kabla o przepustowości 10 Gb/s potrzebny jest kabel SFP+ zakończony z jednej strony wtyczką umożliwiającą podłączenie do przełącznika 10 G, a ze strony podłączanej do urządzenia zakończony adapterem QSFP+ do SFP+ (lub adapterem QSA).
    - Kable zasilające są dołączone do urządzenia.

## <a name="order-data-box-heavy"></a>Zamawianie urządzenia Data Box Heavy

Wykonaj poniższe czynności w witrynie Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Wybierz pozycję **Azure Data Box**.
    
   [![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Wybierz pozycję **Utwórz**.

4. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Kraj/region źródłowy     | Wybierz kraj/region, w którym aktualnie znajdują się dane.         |
    |Docelowy region platformy Azure     | Wybierz region platformy Azure, do którego chcesz przenieść dane.        |

    [![Wybieranie dostępności rodziny produktów Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Wybierz pozycję **Data Box Heavy**. Maksymalna użyteczna pojemność dla pojedynczego zamówienia to 770 TB.

    [![Wybieranie urządzenia Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje, a następnie wybierz przycisk **Dalej**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa     | Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |
    |Grupa zasobów     | Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Docelowy region platformy Azure     | Wybierz region swojego konta magazynu. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Miejsce docelowe magazynu     | Wybierz konto magazynu, dyski zarządzane lub obie te opcje. <br> W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. <br>Urządzenie Data Box Heavy można połączyć z maksymalnie 10 kontami magazynu. <br> Można również utworzyć nowe **konto ogólnego przeznaczenia w wersji 1**, **konto ogólnego przeznaczenia w wersji 2** lub **konto usługi Blob Storage**. <br>Przejrzyj [konta magazynu obsługiwane z urządzeniem](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze Data Box współpracę z zabezpieczonymi kontami magazynu, włącz usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz, jak [dodać usługę Azure Data Box jako usługę zaufaną](../storage/common/storage-network-security.md#exceptions).|

    Jeśli korzystasz z konta magazynu jako miejsca docelowego magazynu, zobaczysz następujący ekran:

    ![Zamówienie urządzenia Data Box Heavy dla konta magazynu](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Jeśli oprócz używania konta magazynu jako miejsca docelowego magazynu korzystasz również z urządzenia Data Box Heavy do utworzenia dysków zarządzanych na podstawie lokalnych dysków VHD, musisz podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Utwórz nowe grupy zasobów, jeśli zamierzasz tworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Istniejącej grupy zasobów można użyć tylko wtedy, gdy ta grupa zasobów została utworzona wcześniej podczas tworzenia zamówienia urządzenia Data Box Heavy na potrzeby dysku zarządzanego za pośrednictwem usługi Data Box. <br> Nazwy poszczególnych grup zasobów rozdziel średnikami. Obsługiwanych jest maksymalnie 10 grup zasobów.|

    ![Zamówienie urządzenia Data Box Heavy dla dysku zarządzanego](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako przejściowe konto magazynu. Usługa Data Box przekazuje wirtualne dyski twarde jako stronicowe obiekty blob do przejściowego konta magazynu przed przekonwertowaniem ich na dyski zarządzane i przeniesieniem do grup zasobów. Aby uzyskać więcej informacji, zobacz [Weryfikowanie przekazania danych na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Wybierz pozycję **Zweryfikuj adres**. 

    Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. Wybierz opcję **Dalej**.

8. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

9. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

10. Wybierz pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.


## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia urządzenia Data Box Heavy, a następnie przejdź do obszaru **Przegląd**, aby sprawdzić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Jeśli urządzenie nie jest niedostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

- Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
- Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
- Generowane jest również hasło urządzenia, które pomaga odblokować urządzenie.
- Urządzenie Data Box Heavy jest blokowane w celu uniemożliwienia nieupoważnionego dostępu do urządzenia w dowolnym momencie.

Po zakończeniu przygotowywania urządzenia w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

![Przetworzone zamówienie urządzenia Data Box Heavy](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Wysłane zamówienie urządzenia Data Box Heavy](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal przejdź do obszaru **Przegląd**, a następnie kliknij pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.
 
Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Zamawianie urządzenia Data Box Heavy
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować urządzenie Data Box Heavy.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
