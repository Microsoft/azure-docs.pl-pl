---
title: Samouczek dotyczący przygotowywania witryny Azure Portal do wdrożenia usługi Data Box Gateway | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Gateway obejmuje przygotowywanie witryny Azure Portal.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706099"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Samouczek: przygotowanie do wdrożenia Azure Data Box Gateway

To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Gateway. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrażania zasobu usługi Data Box Gateway.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

## <a name="get-started"></a>Rozpoczęcie pracy

Proces wdrażania usługi Data Box Gateway opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Gateway](data-box-gateway-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Gateway przed aprowizowaniem urządzenia wirtualnego usługi Data Box Gateway. |
| 2. |**[Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](data-box-gateway-deploy-provision-vmware.md)**|W przypadku funkcji Hyper-V przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta funkcji Hyper-V z systemem Windows Server 2016 lub Windows Server 2012 R2. <br><br><br> W przypadku oprogramowania VMware przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta z oprogramowaniem VMware ESXi 6.0, 6.5 lub 6.7.<br></br> |
| 3. |**[Łączenie, konfigurowanie i aktywowanie usługi Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Następnie można aprowizować udziały SMB.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Gateway, urządzenia usługi Data Box Gateway i sieci centrum danych.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure obsługuje zasób rozwiązania Azure Stack Edge. Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla Azure Stack Edge/Data Box Gateway, IoT Hub i zasobów usługi Azure Storage.
    - Aby utworzyć dowolny zasób Azure Stack Edge/Data Box Gateway, należy mieć uprawnienia jako współautora (lub wyższe) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, przejdź do sekcji [Rejestrowanie dostawcy zasobów](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz uprawnienia administratora lub użytkownika do Microsoft Graph interfejsu API. Aby uzyskać więcej informacji, zobacz [Microsoft Graph informacje o uprawnieniach](/graph/permissions-reference).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-gateway-device"></a>Urządzenie usługi Data Box Gateway

Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

- Masz dostęp do hosta funkcji Hyper-V z systemem Windows Server 2012 R2 lub nowszym bądź z oprogramowaniem VMware (ESXi 6.0, 6.5 lub 6.7), którego można użyć do aprowizacji urządzenia.
- System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego usługi Data Box:
  
  - Co najmniej 4 procesory wirtualne.
  - Co najmniej 8 GB pamięci RAM. Zdecydowanie zalecamy co najmniej 16 GB pamięci RAM.
  - Jeden interfejs sieciowy.
  - Dysk systemu operacyjnego o rozmiarze 250 GB.
  - Dysk wirtualny o rozmiarze 2 TB do przechowywania danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia usługi Data Box Gateway. Aby uzyskać więcej informacji, zobacz [wymagania systemowe Data Box Gateway](data-box-gateway-system-requirements.md).

- W normalnych warunkach operacyjnych Data Box Gateway należy:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Gateway, którego możesz użyć do zarządzania urządzeniami wirtualnymi, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób Data Box Gateway, w Azure Portal wykonaj następujące czynności.

1. Użyj poświadczeń Microsoft Azure, aby zalogować się do dowolnego z tych portali:

    - Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com) .
    - Portal Azure Government pod tym adresem URL: [https://portal.azure.us](https://portal.azure.us) . Aby uzyskać szczegółowe informacje, przejdź do [obszaru łączenie z Azure Government przy użyciu portalu](../azure-government/documentation-government-get-started-connect-with-portal.md).
    
2. Wybierz pozycję **+ Utwórz zasób**.

    ![Azure Data Box Gateway utworzyć zasobu przycisk](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. Wpisz **Data Box Gateway** w polu wyszukiwania, a następnie naciśnij klawisz ENTER.

    ![Wyszukaj usługę Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Następnie wybierz pozycję **Azure Data Box Gateway**.

    ![Wybierz jednostkę SKU Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Wybierz przycisk **Utwórz**.

    ![Kliknij przycisk Utwórz, aby utworzyć zasób Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. Na karcie **Ustawienia podstawowe**:

    Wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Wybierz subskrypcję, która ma być używana na potrzeby urządzenia Data Box Gatewayowego. Subskrypcja jest połączona z kontem rozliczeniowym.|
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).|

   Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   |Przyjazna nazwa identyfikująca zasób.<br>Nazwa ma od 2 do 50 znaków zawierających litery, cyfry i łączniki. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą. |
    |Region (Region)  |Wybierz region, w którym chcesz wdrożyć zasób. Wybierz lokalizację blisko regionu geograficznego, w którym chcesz wdrożyć urządzenie. <br> Aby zapoznać się z listą wszystkich regionów, w których dostępne są usługi Data Base Gateway/Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). <br> W przypadku Azure Government dostępne są wszystkie regiony rządowe wymienione w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) .|

   Następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć zamówienie.

   ![Wpis szczegółu projektu i wystąpienia dla zamówienia Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania** i szczegóły dotyczące zasobu. Wybierz przycisk **Utwórz**.

    ![Szczegóły zasobów Data Box Gateway wyświetlane na potrzeby przeglądu](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Zamówienie zakończone Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>Pobieranie obrazu urządzenia wirtualnego

Po utworzeniu zasobu usługi Data Box Gateway pobierz odpowiedni obraz urządzenia wirtualnego do aprowizowania urządzenia wirtualnego w systemie hosta. Obrazy urządzeń wirtualnych są specyficzne dla systemu operacyjnego.

> [!IMPORTANT]
> Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

Wykonaj następujące kroki w [Azure Portal](https://portal.azure.com/) , aby pobrać obraz urządzenia wirtualnego.

1. W utworzonym zasobie, a następnie wybierz pozycję **Przegląd**. Jeśli masz istniejący zasób Azure Data Box Gateway, wybierz zasób i przejdź do **omówienia**. Wybierz pozycję **Konfiguracja urządzenia**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na kafelku **obraz do pobrania** wybierz obraz urządzenia wirtualnego odpowiadający systemowi operacyjnemu na serwerze hosta używanym do aprowizacji maszyny wirtualnej. Pliki obrazów są około 5,6 GB.
   
   * [Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 R2 i nowszych](https://aka.ms/dbe-vhdx-2012).
   * [VMDK dla VMware ESXi 6,0, 6,5 lub 6,7](https://aka.ms/dbe-vmdk).

    ![Pobierz obraz Data Box Gateway urządzenia wirtualnego](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu zasobu Data Box Gateway należy uzyskać klucz aktywacji. Ten klucz jest używany do aktywowania urządzenia usługi Data Box Gateway i nawiązaniu połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**. W obszarze **Konfiguracja urządzenia** przejdź do kafelka **Konfigurowanie i aktywowanie** .

    ![Konfigurowanie i aktywowanie kafelka](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Wybierz pozycję **Generuj klucz**, aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować maszynę wirtualną na potrzeby usługi Data Box Gateway. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

LUB

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](./data-box-gateway-deploy-provision-vmware.md)