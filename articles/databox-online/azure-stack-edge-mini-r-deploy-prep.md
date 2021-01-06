---
title: Samouczek przygotowywania Azure Portal, środowiska centrum danych do wdrażania Azure Stack Edge mini R Device | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania Azure Stack Edge mini R urządzenie obejmuje przygotowywanie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 4535368b7d8d044469a4b0effee914176aca78e4
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935411"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Samouczek: przygotowanie do wdrożenia Azure Stack Edge mini R

Ten samouczek jest pierwszą częścią serii samouczków wdrażania, które są wymagane do całkowitego wdrożenia urządzenia z systemem Azure Stack Edge mini R. W tym samouczku opisano sposób przygotowania Azure Portal w celu wdrożenia Azure Stackego zasobu brzegowego.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

### <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć Azure Stack Edge w trybie mini R, zapoznaj się z poniższymi samouczkami w określonej kolejności.

| Krok | Opis |
| --- | --- |
| **Preparacie** |Te kroki należy wykonać w celu przygotowania do nadchodzącego wdrożenia. |
| **[Lista kontrolna konfiguracji wdrożenia](#deployment-configuration-checklist)** |Ta lista kontrolna umożliwia zbieranie i rejestrowanie informacji przed wdrożeniem i w jego trakcie. |
| **[Wymagania wstępne dotyczące wdrażania](#prerequisites)** |Te wymagania wstępne sprawdzają, czy środowisko jest gotowe do wdrożenia. |
|  | |
|**Samouczki dotyczące wdrażania** |Te samouczki są wymagane do wdrożenia w środowisku produkcyjnym urządzenia z usługą Azure Stack Edge mini R. |
|**[1. Przygotuj Azure Portal dla urządzenia](azure-stack-edge-mini-r-deploy-prep.md)** |Utwórz i skonfiguruj zasób Azure Stack Edge przed zainstalowaniem urządzenia fizycznego. |
|**[2. Zainstaluj urządzenie](azure-stack-edge-mini-r-deploy-install.md)**|Inspekcja i okablowanie urządzenia fizycznego.  |
|**[3. Nawiąż połączenie z urządzeniem](azure-stack-edge-mini-r-deploy-connect.md)** |Po zainstalowaniu urządzenia Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia.  |
|**[4. Skonfiguruj ustawienia sieci](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Skonfiguruj sieć, w tym ustawienia sieci Web i internetowego serwera proxy dla Twojego urządzenia.   |
|**[5. Konfigurowanie ustawień urządzenia](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Przypisz nazwę urządzenia i domenę DNS, skonfiguruj serwer aktualizacji i godzinę urządzenia. |
|**[6. Skonfiguruj ustawienia zabezpieczeń](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Skonfiguruj certyfikaty przy użyciu własnych certyfikatów, skonfiguruj sieć VPN i skonfiguruj szyfrowanie na urządzeniu.   |
|**[7. Aktywuj urządzenie](azure-stack-edge-mini-r-deploy-activate.md)** |Aktywuj urządzenie przy użyciu klucza aktywacji z usługi. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS lub łączenia się za pośrednictwem interfejsu REST. |
|**[8. Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md)** |Skonfiguruj rolę obliczeń na urządzeniu. Tworzony jest również klaster Kubernetes. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="deployment-configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji wdrożenia

Przed wdrożeniem urządzenia należy zebrać informacje w celu skonfigurowania oprogramowania na urządzeniu z systemem Azure Stack Edge mini R. Wcześniejsze przygotowanie niektórych z tych informacji pomaga usprawnić proces wdrażania urządzenia w środowisku. [Lista kontrolna konfiguracja wdrożenia mini R programu Azure Stack Edge](azure-stack-edge-mini-r-deploy-checklist.md) służy do zanotowania szczegółów konfiguracji podczas wdrażania urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące konfiguracji zasobów usługi Azure Stack Edge, urządzenia brzegowego Azure Stack i sieci centrum danych.

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Dla urządzenia brzegowego Azure Stack

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Zawarto przegląd informacji o zabezpieczeniach tego urządzenia w [wytycznych dotyczących bezpieczeństwa dla urządzenia brzegowego Azure Stack](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest konfigurowana zgodnie z wymaganiami sieci dla urządzenia brzegowego Azure Stack. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge w systemie mini R wymagania systemowe](azure-stack-edge-mini-r-system-requirements.md).

- W normalnych warunkach operacyjnych Azure Stack Edge:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób Azure Stack Edge do zarządzania urządzeniem fizycznym, Pomiń ten krok i przejdź do pozycji [Pobierz klucz aktywacji](#get-the-activation-key).

Aby utworzyć zasób Azure Stack Edge, wykonaj następujące czynności w Azure Portal.

1. Użyj poświadczeń Microsoft Azure, aby zalogować się do Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com) .


2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj i wybierz pozycję **Azure Stack Edge/Data Box Gateway**. Wybierz przycisk **Utwórz**. 

3. Wybierz subskrypcję, która ma być używana na potrzeby urządzenia z Azure Stack EDGE Pro. Wybierz kraj, w którym chcesz wysłać to urządzenie fizyczne. Wybierz pozycję **Pokaż urządzenia**.

    ![Utwórz zasób 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Wybierz pozycję typ urządzenia. W obszarze **Azure Stack Edge** wybierz pozycję **Azure Stack Edge mini R** , a następnie wybierz **pozycję Wybierz**. Jeśli widzisz jakiekolwiek problemy lub nie można wybrać typu urządzenia, przejdź do obszaru [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

    [![Tworzenie zasobu 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Subskrypcja zostanie automatycznie wypełniona w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |


6. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa ma od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region (Region)     |Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Tworzenie zasobu 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. Wybierz pozycję **Dalej: adres wysyłkowy**.

    - Jeśli masz już urządzenie, zaznacz pole kombi, dla którego **masz już urządzenie**.

     ![Tworzenie zasobu 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

    - Jeśli jest to nowe urządzenie, które ma być uporządkowane, wprowadź nazwę kontaktu, firmę, adres, na który ma zostać wydane urządzenie, i informacje kontaktowe.

     ![Tworzenie zasobu 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. Wybierz pozycję **Dalej: Tagi**. Opcjonalnie możesz podać znaczniki kategoryzacji zasobów i skonsolidować rozliczenia. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

9. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania** i szczegóły dotyczące zasobu. Zaznacz pole kombi dla **zrecenzowanych warunków zachowania poufności informacji**.

    ![Tworzenie zasobu 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Zostanie również powiadomiony o tym, że podczas tworzenia zasobów jest włączone tożsamość usługi zarządzanej (MSI), które umożliwia uwierzytelnianie w usługach w chmurze. Ta tożsamość istnieje pod warunkiem, że istnieje zasób.

10. Wybierz przycisk **Utwórz**.

    Tworzenie zasobu trwa kilka minut. Tworzony jest również plik MSI, który umożliwia Azure Stack urządzeniu brzegowego komunikowanie się z dostawcą zasobów na platformie Azure.
    
    Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.
    
    ![Przejdź do zasobu Azure Stack EDGE Pro](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
    Po złożeniu zamówienia firma Microsoft przegląda zamówienie i dotrze do Ciebie (za pośrednictwem poczty e-mail), podając szczegóły dotyczące wysyłki.

   W przypadku wystąpienia problemów występujących w procesie zamówienia zobacz [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu Azure Stack brzegowej należy uzyskać klucz aktywacji. Ten klucz służy do uaktywniania i łączenia urządzenia Azure Stack Edge mini R z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**.

   ![Wybierz konfigurację urządzenia](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Na kafelku **Aktywuj** Podaj nazwę dla Azure Key Vault lub zaakceptuj nazwę domyślną. Nazwa magazynu kluczy może mieć długość od 3 do 24 znaków. 

    Magazyn kluczy jest tworzony dla każdego zasobu usługi Azure Stack Edge, który został aktywowany z urządzeniem. Magazyn kluczy umożliwia przechowywanie kluczy tajnych i uzyskiwanie do nich dostępu. Na przykład klucz integralności kanału (CIK) dla usługi jest przechowywany w magazynie kluczy.

    Po określeniu nazwy magazynu kluczy wybierz pozycję **Generuj klucz aktywacji** , aby utworzyć klucz aktywacji.

    [![Pobieranie klucza aktywacji](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Poczekaj kilka minut, gdy zostanie utworzony magazyn kluczy i klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

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
> [Instalowanie Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)
