---
title: Samouczek przygotowania Azure Portal, środowiska centrum danych do wdrożenia Azure Stack EDGE Pro | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania Azure Stack EDGE Pro obejmuje przygotowywanie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 224131d1b17c4ed34ee847638633a5a1a494ccaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894134"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Samouczek: przygotowanie do wdrożenia Azure Stack EDGE Pro  

Jest to pierwszy samouczek z serii samouczków dotyczących wdrażania, które są wymagane do całkowitego wdrożenia Azure Stack EDGE Pro. W tym samouczku opisano sposób przygotowania Azure Portal w celu wdrożenia Azure Stackego zasobu brzegowego.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć Azure Stack EDGE Pro, zapoznaj się z poniższymi samouczkami w określonej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotuj Azure Portal dla Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md)** |Przed zainstalowaniem urządzenia z systemem Azure Stack Edge należy utworzyć i skonfigurować zasób brzegowy Azure Stack. |
| 2. |**[Zainstaluj Azure Stack EDGE Pro](azure-stack-edge-deploy-install.md)**|Rozpakuj, stojak i podłącz kable do urządzenia fizycznego w Azure Stack EDGE Pro.  |
| 3. |**[Łączenie, Konfigurowanie i aktywowanie Azure Stack EDGE Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS.  |
| 4. |**[Transferowanie danych za pomocą Azure Stack EDGE Pro](azure-stack-edge-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
| 5. |**[Przekształcanie danych za pomocą Azure Stack EDGE Pro](azure-stack-edge-deploy-configure-compute.md)** |Skonfiguruj moduły obliczeniowe na urządzeniu, aby przekształcić dane w miarę ich przenoszenia na platformę Azure. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące konfiguracji dla zasobu usługi Azure Stack Edge, urządzenia brzegowego Azure Stack Edge i sieci centrum danych.

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure obsługuje zasób rozwiązania Azure Stack Edge. Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). Subskrypcje z płatnością zgodnie z rzeczywistym użyciem nie są obsługiwane.

* Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla Azure Stack Edge/Data Box Gateway, IoT Hub i zasobów usługi Azure Storage.

  * Aby udzielić dostępu współautora, należy być **właścicielem** na poziomie subskrypcji. Aby dać autorowi dostęp do innej osoby, w Azure Portal przejdź do pozycji **wszystkie usługi**  >  **subskrypcje**  >  **dostęp do kontroli dostępu (IAM)**  >  **+ Dodaj**  >  **Dodaj przypisanie roli**. Aby uzyskać więcej informacji, zobacz [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

  * Aby utworzyć dowolny zasób Azure Stack Edge/Data Box Gateway, należy mieć uprawnienia jako współautora (lub wyższe) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca zasobów został zarejestrowany. Aby uzyskać informacje na temat rejestrowania dostawcy zasobów, zobacz [Rejestrowanie dostawcy zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Aby utworzyć dowolny zasób IoT Hub, upewnij się, że jest zarejestrowany dostawca Microsoft. Devices. Aby uzyskać informacje na temat rejestrowania, przejdź do sekcji [Rejestrowanie dostawcy zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
* Masz uprawnienia administratora lub użytkownika do Azure Active Directory interfejs API programu Graph. Aby uzyskać więcej informacji, zobacz [Azure Active Directory interfejs API programu Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Nie są blokowane przez żadną zasadę platformy Azure skonfigurowaną przez administratora systemu. Aby uzyskać więcej informacji na temat zasad, zobacz [Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal).

### <a name="for-the-azure-stack-edge-pro-device"></a>Dla urządzenia Azure Stack EDGE Pro

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

* Zawarto przegląd informacji o bezpieczeństwie uwzględnionych w pakiecie dostawy.
* Dostępne jest gniazdo o rozmiarze 1U w standardowym 19 "stojaku w centrum danych na potrzeby instalowania urządzenia.
* Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
* Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
* Masz dostęp do urządzenia fizycznego.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest konfigurowana zgodnie z wymaganiami dotyczącymi sieci dla urządzenia z Azure Stack Edge. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge — wymagania systemowe](azure-stack-edge-system-requirements.md).

* W normalnych warunkach operacyjnych Azure Stack EDGE Pro masz:

  * Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
  * Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób Azure Stack Edge do zarządzania urządzeniem fizycznym, Pomiń ten krok i przejdź do pozycji [Pobierz klucz aktywacji](#get-the-activation-key).

Aby utworzyć zasób Azure Stack Edge, wykonaj następujące czynności w Azure Portal.

1. Zaloguj się do programu przy użyciu poświadczeń Microsoft Azure 

    - Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com) .
    - Lub Portal Azure Government pod tym adresem URL: [https://portal.azure.us](https://portal.azure.us) . Aby uzyskać więcej informacji, przejdź do [obszaru łączenie z Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj i wybierz pozycję **Azure Stack Edge/Data Box Gateway**. Wybierz przycisk **Utwórz**.
3. Wybierz subskrypcję, która ma być używana na potrzeby urządzenia z Azure Stack EDGE Pro. Wybierz region, w którym chcesz wdrożyć zasób Azure Stack Edge. Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Region przechowuje tylko metadane dotyczące zarządzania urządzeniami. Rzeczywiste dane mogą być przechowywane na dowolnym koncie magazynu.
    
    W **Azure Stack EDGE Pro** wybierz pozycję **Utwórz**.

    ![Usługa Search Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region     |Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Szczegóły projektu i wystąpienia](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Wybierz pozycję **Dalej: adres wysyłkowy**.

    - Jeśli masz już urządzenie, zaznacz pole kombi dla **urządzenia z Azure Stack EDGE Pro**.
    - Jeśli jest to nowe urządzenie, które ma być uporządkowane, wprowadź nazwę kontaktu, firmę, adres, który ma zostać wysłany do urządzenia, i informacje kontaktowe.

    ![Adres wysyłkowy dla nowego urządzenia](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

7. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania**i szczegóły dotyczące zasobu. Zaznacz pole kombi dla **zrecenzowanych warunków zachowania poufności informacji**.

    ![Przejrzyj szczegóły zasobów Azure Stack Edge i warunki prywatności](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Wybierz przycisk **Utwórz**.

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Po złożeniu zamówienia firma Microsoft przegląda zamówienie i dotrze do Ciebie (za pośrednictwem poczty e-mail), podając szczegóły dotyczące wysyłki.

![Powiadomienie o przeglądzie kolejności Pro Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu Azure Stack brzegowej należy uzyskać klucz aktywacji. Ten klucz służy do uaktywniania i łączenia urządzenia Azure Stack EDGE Pro z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób. Wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Konfiguracja urządzenia**.

    ![Wybierz konfigurację urządzenia](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na kafelku **Aktywuj** wybierz pozycję **Generuj klucz** , aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> * Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Stack krawędzi programu Edge dla pakietu Pro, takich jak:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować program Azure Stack EDGE Pro.

> [!div class="nextstepaction"]
> [Zainstaluj Azure Stack EDGE Pro](./azure-stack-edge-deploy-install.md)
