---
title: Samouczek przygotowania Azure Portal, środowiska centrum danych do wdrożenia Azure Data Box Edge | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Edge obejmuje przygotowywanie witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79474479"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Samouczek: przygotowanie do wdrożenia Azure Data Box Edge  

To jest pierwszy samouczek z serii samouczków dotyczących wdrażania, wymaganych do pełnego wdrożenia usługi Azure Data Box Edge. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrożenia zasobu usługi Data Box Edge.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

### <a name="get-started"></a>Wprowadzenie

Proces wdrażania usługi Data Box Edge opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Edge](data-box-edge-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Edge, zanim zainstalujesz urządzenie fizyczne Data Box Edge. |
| 2. |**[Zainstaluj Data Box Edge](data-box-edge-deploy-install.md)**|Rozpakuj, umieść na stojaku i podłącz kable urządzenia fizycznego Data Box Edge.  |
| 3. |**[Łączenie, konfigurowanie i aktywowanie urządzenia Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Edge](data-box-edge-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
| 5. |**[Przekształcanie danych za pomocą Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Skonfiguruj moduły obliczeniowe na urządzeniu, aby przekształcić dane w miarę ich przenoszenia na platformę Azure. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Edge, urządzenia Data Box Edge i sieci centrum danych.

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Subskrypcja Microsoft Azure jest włączona dla zasobu Azure Stack Edge. Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla zasobów Data Box Edge/Data Box Gateway, IoT Hub i magazynu platformy Azure.
    - Aby utworzyć dowolny zasób Data Box Edge/Data Box Gateway, należy mieć uprawnienia jako współautor (lub wyższy) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, przejdź do pozycji [zarejestruj dostawcę zasobów](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć dowolny zasób IoT Hub, upewnij się, że jest zarejestrowany dostawca Microsoft. Devices. Aby uzyskać informacje na temat rejestrowania, przejdź do pozycji [zarejestruj dostawcę zasobów](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz uprawnienia administratora lub użytkownika do Microsoft Graph interfejsu API. Aby uzyskać więcej informacji, zobacz [Microsoft Graph informacje o uprawnieniach](https://docs.microsoft.com/graph/permissions-reference).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-edge-device"></a>Urządzenie Data Box Edge

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Zawarto przegląd informacji o bezpieczeństwie uwzględnionych w pakiecie dostawy.
- Dostępne jest gniazdo o rozmiarze 1U w standardowym 19 "stojaku w centrum danych na potrzeby instalowania urządzenia.
- Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
- Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Masz dostęp do urządzenia fizycznego.


### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia Data Box Edge. Aby uzyskać więcej informacji, zobacz [wymagania systemowe usługi Data Box Edge](data-box-edge-system-requirements.md).

- W normalnych warunkach operacyjnych Data Box Edge masz:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Edge, którego możesz użyć do zarządzania urządzeniem fizycznym, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób usługi Data Box Edge, wykonaj poniższe czynności w witrynie Azure Portal.

1. Zaloguj się do programu przy użyciu poświadczeń Microsoft Azure 
    
    - Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com).
    - Lub Portal Azure Government pod tym adresem URL: [https://portal.azure.us](https://portal.azure.us). Aby uzyskać więcej informacji, przejdź do [obszaru łączenie z Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj **Data Box Edge/Data Box Gateway**. Wybierz pozycję **Data Box Edge/Data Box Gateway**. Wybierz przycisk **Utwórz**.
3. Wybierz subskrypcję, która ma być używana dla Data Box Edge urządzenia. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Region przechowuje tylko metadane dotyczące zarządzania urządzeniami. Rzeczywiste dane mogą być przechowywane na dowolnym koncie magazynu.
    
    W opcji usługi **Data Box Edge** wybierz pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |

4. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region     |Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Szczegóły projektu i wystąpienia](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Wybierz pozycję **Dalej: adres wysyłkowy**.

    - Jeśli masz już urządzenie, zaznacz pole kombi z **urządzeniem Data Box Edge**.
    - Jeśli jest to nowe urządzenie, które ma być uporządkowane, wprowadź nazwę kontaktu, firmę, adres, który ma zostać wysłany do urządzenia, i informacje kontaktowe.

    ![Adres wysyłkowy dla nowego urządzenia](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Wybierz pozycję **Dalej: przegląd + Utwórz**.

7. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania**i szczegóły dotyczące zasobu. Zaznacz pole kombi dla **zrecenzowanych warunków zachowania poufności informacji**.

    ![Przejrzyj szczegóły zasobów Data Box Edge i warunki prywatności](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Wybierz przycisk **Utwórz**.

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Po złożeniu zamówienia firma Microsoft przegląda zamówienie i dotrze do Ciebie (za pośrednictwem poczty e-mail), podając szczegóły dotyczące wysyłki.

![Powiadomienie o przeglądzie kolejności Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po skonfigurowaniu i uruchomieniu zasobu usługi Data Box Edge konieczne będzie uzyskanie klucza aktywacji. Ten klucz jest używany do aktywowania urządzenia Data Box Edge i nawiązania połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób. Wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Konfiguracja urządzenia**.

    ![Wybierz konfigurację urządzenia](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na kafelku **Aktywuj** wybierz pozycję **Generuj klucz** , aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować urządzenie Data Box Edge.

> [!div class="nextstepaction"]
> [Instalowanie urządzenia Data Box Edge](./data-box-edge-deploy-install.md)



