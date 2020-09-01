---
title: Samouczek przygotowania Azure Portal, środowiska centrum danych do wdrożenia Azure Stack Edge GPU | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania Azure Stack Edge GPU obejmuje przygotowywanie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c02af8983489651d8b5b83d5d3107a534c51f067
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254682"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-with-gpu"></a>Samouczek: przygotowanie do wdrożenia Azure Stack Edge z procesorem GPU 

Jest to pierwszy samouczek z serii samouczków wdrażania, które są wymagane do całkowitego wdrożenia Azure Stack Edge z procesorem GPU. W tym samouczku opisano sposób przygotowania Azure Portal w celu wdrożenia Azure Stackego zasobu brzegowego.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Uzyskiwanie klucza aktywacji

### <a name="get-started"></a>Wprowadzenie

W celu wdrożenia Azure Stack Edge należy najpierw przygotować swoje środowisko. Gdy środowisko będzie gotowe, wykonaj wymagane kroki i w razie potrzeby opcjonalne kroki i procedury, aby całkowicie wdrożyć urządzenie. Instrukcje krok po kroku wskazują, kiedy należy wykonać każdy z tych wymaganych i opcjonalnych kroków.

| Krok | Opis |
| --- | --- |
| **Przygotowanie** |Te kroki należy wykonać w celu przygotowania do nadchodzącego wdrożenia. |
| **[Lista kontrolna konfiguracji wdrożenia](#deployment-configuration-checklist)** |Ta lista kontrolna umożliwia zbieranie i rejestrowanie informacji przed wdrożeniem i w jego trakcie. |
| **[Wymagania wstępne dotyczące wdrażania](#prerequisites)** |Służą do sprawdzania, czy środowisko jest gotowe do przeprowadzenia wdrożenia. |
|  | |
|**Samouczki dotyczące wdrażania** |Te samouczki są wymagane do wdrożenia urządzenia brzegowego Azure Stack w środowisku produkcyjnym. |
|**[1. Przygotuj Azure Portal dla Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)** |Utwórz i skonfiguruj zasób Azure Stack Edge przed zainstalowaniem urządzenia fizycznego z krawędzią okna Azure Stack. |
|**[2. Zainstaluj Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md)**|Rozpakowywanie, stojake i kablowe urządzenia fizyczne w Azure Stack Edge.  |
|**[3. Nawiązywanie połączenia z Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)** |Po zainstalowaniu urządzenia Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia.  |
|**[4. Skonfiguruj ustawienia sieci dla Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Skonfiguruj sieć, w tym ustawienia sieci Web i internetowego serwera proxy dla Twojego urządzenia.   |
|**[5. Skonfiguruj ustawienia urządzenia dla Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Przypisz nazwę urządzenia i domenę DNS, skonfiguruj serwer aktualizacji i godzinę urządzenia. |
|**[6. Skonfiguruj ustawienia zabezpieczeń dla Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Skonfiguruj certyfikaty dla urządzenia. Użyj certyfikatów wygenerowanych przez urządzenia lub Przenieś własne certyfikaty.   |
|**[7. Aktywuj Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md)** |Aktywuj urządzenie przy użyciu klucza aktywacji z usługi. Urządzenie jest gotowe do skonfigurowania udziałów SMB lub NFS lub łączenia się za pośrednictwem interfejsu REST. |
|**[8. Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md)** |Skonfiguruj rolę obliczeń na urządzeniu. Spowoduje to również utworzenie klastra Kubernetes. |
|**[9a. Transferowanie danych za pomocą udziałów brzegowych](azure-stack-edge-j-series-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |
|**[9b. Transferowanie danych za pomocą kont magazynu Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Dodawanie kont magazynu i nawiązywanie połączenia z usługą BLOB Storage za pośrednictwem interfejsów API REST. |


Teraz możesz zacząć zbierać informacje dotyczące konfiguracji oprogramowania dla urządzenia Azure Stack Edge.

## <a name="deployment-configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji wdrożenia

Przed wdrożeniem urządzenia należy zebrać informacje w celu skonfigurowania oprogramowania na urządzeniu Azure Stack Edge. Wcześniejsze przygotowanie niektórych z tych informacji pomaga usprawnić proces wdrażania urządzenia w środowisku. [Lista kontrolna konfiguracji wdrożenia Azure Stack Edge](azure-stack-edge-gpu-deploy-checklist.md) służy do zanotowania szczegółów konfiguracji podczas wdrażania urządzenia.


## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące konfiguracji zasobów usługi Azure Stack Edge, urządzenia brzegowego Azure Stack i sieci centrum danych.

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

Przed rozpoczęciem upewnij się, że:

- Subskrypcja Microsoft Azure jest włączona dla zasobu Azure Stack Edge. Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). Subskrypcje z płatność zgodnie z rzeczywistym użyciem nie są obsługiwane.
- Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla Azure Stack Edge/Data Box Gateway, IoT Hub i zasobów usługi Azure Storage.

    - Aby utworzyć dowolny zasób Azure Stack Edge/Data Box Gateway, należy mieć uprawnienia jako współautora (lub wyższe) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, przejdź do pozycji [zarejestruj dostawcę zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć dowolny zasób IoT Hub, upewnij się, że jest zarejestrowany dostawca Microsoft. Devices. Aby uzyskać informacje na temat rejestrowania, przejdź do pozycji [zarejestruj dostawcę zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz uprawnienia administratora lub użytkownika do Azure Active Directory interfejs API programu Graph. Aby uzyskać więcej informacji, zobacz [Azure Active Directory interfejs API programu Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-azure-stack-edge-device"></a>Dla urządzenia brzegowego Azure Stack

Przed wdrożeniem urządzenia fizycznego upewnij się, że są spełnione następujące warunki:

- Zawarto przegląd informacji o bezpieczeństwie uwzględnionych w pakiecie dostawy.
- Dostępne jest gniazdo o rozmiarze 1U w standardowym 19 "stojaku w centrum danych na potrzeby instalowania urządzenia.
- Masz dostęp do płaskiej, stabilnej i poziomej powierzchni roboczej, gdzie można bezpiecznie umieścić urządzenie.
- Miejsce, w którym chcesz skonfigurować urządzenie, ma standardowe zasilanie prądem przemiennym z niezależnego źródła lub jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Masz dostęp do urządzenia fizycznego.


### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest konfigurowana zgodnie z wymaganiami sieci dla urządzenia brzegowego Azure Stack. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge wymagania systemowe](azure-stack-edge-system-requirements.md).

- W normalnych warunkach operacyjnych Azure Stack Edge:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób Azure Stack Edge do zarządzania urządzeniem fizycznym, Pomiń ten krok i przejdź do pozycji [Pobierz klucz aktywacji](#get-the-activation-key).

Aby utworzyć zasób Azure Stack Edge, wykonaj następujące czynności w Azure Portal.

1. Użyj poświadczeń Microsoft Azure, aby zalogować się do Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com) .

2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj i wybierz pozycję **Azure Stack Edge/Data Box Gateway**. Wybierz przycisk **Utwórz**. Jeśli zobaczysz jakiekolwiek problemy, przejdź do obszaru [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

3. Wybierz subskrypcję, która ma być używana na potrzeby urządzenia Azure Stack Edge. Wybierz kraj, w którym chcesz wysłać to urządzenie fizyczne. Wybierz pozycję **Pokaż urządzenia**.

    ![Utwórz zasób 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Wybierz pozycję typ urządzenia. W obszarze **Azure Stack krawędź komercyjna**wybierz pozycję **Azure Stack Edge z procesorem GPU** i wybierz pozycję **Utwórz konto**. 

    ![Tworzenie zasobu 2](media/azure-stack-edge-gpu-deploy-prep/create-resource-2.png)

5. Zostanie wyświetlony krótki formularz. Wypełnij formularz i wybierz pozycję **Prześlij**. Firma Microsoft umożliwi korzystanie z subskrypcji.

    ![Tworzenie zasobu 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Po włączeniu subskrypcji powinno być możliwe przeprowadzenie operacji tworzenia zasobów. W bloku **Wybierz typ urządzenia** wybierz **pozycję Wybierz**.

    ![Tworzenie zasobu 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)


6. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |
    |Region     |Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz [dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). W przypadku korzystania z Azure Government wszystkie regiony rządowe są dostępne, jak pokazano w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|

    ![Tworzenie zasobu 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


5. Wybierz pozycję **Dalej: adres wysyłkowy**.

    - Jeśli masz już urządzenie, zaznacz pole kombi dla **urządzenia z Azure Stack Edge**.

        ![Tworzenie zasobu 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Jeśli jest to nowe urządzenie, które ma być uporządkowane, wprowadź nazwę kontaktu, firmę, adres, który ma zostać wysłany do urządzenia, i informacje kontaktowe.

        ![Tworzenie zasobu 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

6. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

7. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania**i szczegóły dotyczące zasobu. Zaznacz pole kombi dla **zrecenzowanych warunków zachowania poufności informacji**.

    ![Tworzenie zasobu 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

8. Wybierz przycisk **Utwórz**.

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Po złożeniu zamówienia firma Microsoft przegląda zamówienie i dotrze do Ciebie (za pośrednictwem poczty e-mail), podając szczegóły dotyczące wysyłki.

![Powiadomienie na potrzeby przeglądu Azure Stack kolejności krawędzi](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

W przypadku wystąpienia problemów występujących w procesie zamówienia zobacz [Rozwiązywanie problemów z kolejnością](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu Azure Stack brzegowej należy uzyskać klucz aktywacji. Ten klucz służy do uaktywniania i łączenia urządzenia brzegowego Azure Stack z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób. Wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Konfiguracja urządzenia**.

    ![Wybierz konfigurację urządzenia](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na kafelku **Aktywuj** wybierz pozycję **Generuj klucz** , aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/azure-stack-edge-deploy-prep/get-activation-key.png)

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
> [Instalowanie Azure Stack Edge](./azure-stack-edge-gpu-deploy-install.md)



