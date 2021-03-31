---
title: Samouczek przygotowujący Azure Portal, środowisko centrum danych do wdrożenia Azure Stack EDGE Pro R
description: Pierwszy samouczek dotyczący wdrażania Azure Stack EDGE Pro R obejmuje przygotowywanie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 5e220759a46ad9098f81a9534fa64145adade2b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613128"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Samouczek: przygotowanie do wdrożenia Azure Stack EDGE Pro R

Ten samouczek jest pierwszą częścią serii samouczków wdrażania, które są wymagane do całkowitego wdrożenia Azure Stack EDGE Pro R. W tym samouczku opisano sposób przygotowania Azure Portal w celu wdrożenia Azure Stackego zasobu brzegowego. W tym samouczku jest stosowane urządzenie z 1-węzłowym Azure Stack EDGE Pro R z zasilaczem UPS.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

### <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć Azure Stack EDGE Pro R, zapoznaj się z poniższymi samouczkami w określonej kolejności.

| Aby wykonać ten krok | Skorzystaj z tych dokumentów |
| --- | --- |
| **Preparacie** |Te kroki należy wykonać w celu przygotowania do nadchodzącego wdrożenia. |
| **[Lista kontrolna konfiguracji wdrożenia](#deployment-configuration-checklist)** |Ta lista kontrolna umożliwia zbieranie i rejestrowanie informacji przed wdrożeniem i w jego trakcie. |
| **[Wymagania wstępne dotyczące wdrażania](#prerequisites)** |Te wymagania wstępne sprawdzają, czy środowisko jest gotowe do wdrożenia. |
|  | |
|**Samouczki dotyczące wdrażania** |Te samouczki są wymagane do wdrożenia w środowisku produkcyjnym urządzenia z usługą Azure Stack EDGE Pro. |
|**[1. Przygotuj Azure Portal dla urządzenia](azure-stack-edge-pro-r-deploy-prep.md)** |Utwórz i skonfiguruj zasób Azure Stack Edge przed zainstalowaniem urządzenia fizycznego z krawędzią okna Azure Stack. |
|**[2. Zainstaluj urządzenie](azure-stack-edge-pro-r-deploy-install.md)**|Inspekcja i okablowanie urządzenia fizycznego.  |
|**[3. Nawiąż połączenie z urządzeniem](azure-stack-edge-pro-r-deploy-connect.md)** |Po zainstalowaniu urządzenia Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia.  |
|**[4. Skonfiguruj ustawienia sieci](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Skonfiguruj sieć, w tym ustawienia sieci Web i internetowego serwera proxy dla Twojego urządzenia.   |
|**[5. Konfigurowanie ustawień urządzenia](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Przypisz nazwę urządzenia i domenę DNS, skonfiguruj serwer aktualizacji i godzinę urządzenia. |
|**[6. Skonfiguruj ustawienia zabezpieczeń](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Skonfiguruj certyfikaty, sieci VPN, szyfrowanie w spoczynku dla urządzenia. Użyj certyfikatów wygenerowanych przez urządzenia lub Przenieś własne certyfikaty.   |
|**[7. Aktywuj urządzenie](azure-stack-edge-pro-r-deploy-activate.md)** |Aktywuj urządzenie przy użyciu klucza aktywacji z usługi. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS lub łączenia się za pośrednictwem interfejsu REST. |
|**[8. Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md)** |Skonfiguruj rolę obliczeń na urządzeniu. Tworzony jest również klaster Kubernetes. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="deployment-configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji wdrożenia

Przed wdrożeniem urządzenia należy zebrać informacje w celu skonfigurowania oprogramowania na urządzeniu Azure Stack Edge. Wcześniejsze przygotowanie niektórych z tych informacji pomaga usprawnić proces wdrażania urządzenia w środowisku. [Lista kontrolna konfiguracji wdrożenia programu Azure Stack Edge](azure-stack-edge-pro-r-deploy-checklist.md) w programie jest używana w celu zanotowania szczegółów konfiguracji podczas wdrażania urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące konfiguracji zasobów usługi Azure Stack Edge, urządzenia brzegowego Azure Stack i sieci centrum danych.

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Dla urządzenia brzegowego Azure Stack

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Informacje o bezpieczeństwie tego urządzenia zostały sprawdzone pod adresem: [wytyczne dotyczące bezpieczeństwa urządzenia brzegowego Azure Stack](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest konfigurowana zgodnie z wymaganiami sieci dla urządzenia brzegowego Azure Stack. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge — wymagania systemowe dotyczące systemu](azure-stack-edge-pro-r-system-requirements.md).

- W normalnych warunkach użytkowania urządzenia masz:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób Azure Stack Edge do zarządzania urządzeniem fizycznym, Pomiń ten krok i przejdź do pozycji [Pobierz klucz aktywacji](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć zasób Azure Stack Edge, wykonaj następujące czynności w Azure Portal.

1. Użyj poświadczeń Microsoft Azure, aby zalogować się do Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com) .

2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj i wybierz pozycję **Azure Stack Edge/Data Box Gateway**. Wybierz przycisk **Utwórz**. 

3. Wybierz subskrypcję, która ma być używana na potrzeby urządzenia z Azure Stack EDGE Pro. Wybierz kraj, w którym chcesz wysłać to urządzenie fizyczne. Wybierz pozycję **Pokaż urządzenia**.

    ![Utwórz zasób 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Wybierz pozycję typ urządzenia. W obszarze **Azure Stack Edge** wybierz pozycję **Azure Stack EDGE Pro R** , a następnie wybierz **pozycję Wybierz**. Jeśli widzisz jakiekolwiek problemy lub nie można wybrać typu urządzenia, przejdź do obszaru [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

    ![Tworzenie zasobu 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. W zależności od potrzeb firmy można wybrać pozycję **Azure Stack Edge w ramach jednego węzła** lub **Azure Stack EDGE Pro węzeł w ramach programu UPS**.  

    ![Tworzenie zasobu 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Subskrypcja zostanie automatycznie wypełniona w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |

7. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region (Region)     |Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Tworzenie zasobu 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Wybierz pozycję **Dalej: adres wysyłkowy**.

    - Jeśli masz już urządzenie, zaznacz pole kombi w przypadku, gdy **jest dostępne urządzenie Azure Stack EDGE Pro R**.

        ![Tworzenie zasobu 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Jeśli to urządzenie jest nowe urządzenie, które ma być używane, wprowadź nazwę kontaktu, firmę, adres, który ma zostać wysłany do urządzenia, i informacje kontaktowe.

        ![Tworzenie zasobu 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Wybierz pozycję **Dalej: Tagi**. Opcjonalnie możesz podać znaczniki kategoryzacji zasobów i skonsolidować rozliczenia. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

10. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania** i szczegóły dotyczące zasobu. Zaznacz pole kombi dla **zrecenzowanych warunków zachowania poufności informacji**.

    ![Tworzenie zasobu 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Użytkownik otrzymuje również powiadomienie, że podczas tworzenia zasobu jest włączony tożsamość usługi zarządzanej (MSI), który umożliwia uwierzytelnianie w usługach w chmurze. Ta tożsamość istnieje pod warunkiem, że istnieje zasób.

11. Wybierz przycisk **Utwórz**.

    Tworzenie zasobu trwa kilka minut. Tworzony jest również plik MSI, który umożliwia Azure Stack urządzeniu brzegowego komunikowanie się z dostawcą zasobów na platformie Azure.

    Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

    ![Przejdź do zasobu Azure Stack EDGE Pro](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Po złożeniu zamówienia firma Microsoft przegląda zamówienie i kontaktuje się z nim za pośrednictwem poczty e-mail z informacjami dotyczącymi wysyłki.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png) - If this is restored, it must go above "After the resource is successfully created." The azure-stack-edge-resource-1.png would seem superfluous in that case.--> 

> [!NOTE]
> Jeśli chcesz utworzyć wiele zamówień w tym samym czasie lub sklonować istniejące zamówienie, możesz użyć [skryptów w przykładach platformy Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Aby uzyskać więcej informacji, zobacz plik Readme.

W przypadku wystąpienia problemów występujących w procesie zamówienia zobacz [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W razie potrzeby Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Aby utworzyć zasób Azure Stack Edge, uruchom następujące polecenia w interfejsie wiersza polecenia platformy Azure.

1. Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az_group_create) lub Użyj istniejącej grupy zasobów:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Aby utworzyć urządzenie, użyj polecenia [AZ databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgePR_Base
   ```

   Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Region przechowuje tylko metadane dotyczące zarządzania urządzeniami. Rzeczywiste dane mogą być przechowywane na dowolnym koncie magazynu.

   Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).

1. Aby utworzyć zamówienie, uruchom polecenie [AZ databoxedge Order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) :

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Tworzenie zasobu trwa kilka minut. Uruchom polecenie [AZ databoxedge Order Show](/cli/azure/databoxedge/order#az_databoxedge_order_show) , aby zobaczyć kolejność:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Po umieszczeniu zamówienia firma Microsoft przegląda zamówienie i kontaktuje się z nim za pośrednictwem poczty e-mail z informacjami dotyczącymi wysyłki.

---

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu Azure Stack brzegowej należy uzyskać klucz aktywacji. Ten klucz służy do uaktywniania i łączenia urządzenia Azure Stack EDGE Pro z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**.

2. W prawym okienku Podaj nazwę Azure Key Vault lub zaakceptuj nazwę domyślną. Nazwa magazynu kluczy może mieć długość od 3 do 24 znaków.

   Magazyn kluczy jest tworzony dla każdego zasobu usługi Azure Stack Edge, który został aktywowany z urządzeniem. Magazyn kluczy pozwala przechowywać klucze tajne i uzyskiwać do nich dostęp, na przykład klucz integralności kanału (CIK) dla usługi jest przechowywany w magazynie kluczy.

   Po określeniu nazwy magazynu kluczy wybierz pozycję **Generuj klucz aktywacji** , aby utworzyć klucz aktywacji.

   ![Pobieranie klucza aktywacji](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

   Poczekaj kilka minut, gdy zostanie utworzony magazyn kluczy i klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.<!--Verify that the new screen has a copy icon.-->

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Stack brzegowych tematów, takich jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować Azure Stack Edge.

> [!div class="nextstepaction"]
> [Instalowanie Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)
