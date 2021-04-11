---
title: Samouczek dotyczący filtrowania, analizowania danych przy użyciu obliczeń na Azure Stack EDGE Pro z procesorem GPU | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu GPU z systemem Azure Stack Edge i korzystać z niego do przekształcania danych przed wysłaniem ich do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 1dcf429ddc697dfbfdac721cb8de137b9382b234
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058786"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Samouczek: Przekształcanie danych za pomocą Azure Stack EDGE Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu z systemem Azure Stack Edge w wersji Pro. Po skonfigurowaniu roli obliczeniowej Azure Stack EDGE Pro może przekształcić dane przed wysłaniem ich do platformy Azure.

Wykonanie tej procedury może potrwać około 10 do 15 minut.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu z systemem Azure Stack EDGE Pro upewnij się, że:
- Twoje urządzenie Azure Stack EDGE Pro zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

W celu skonfigurowania obliczeń na Azure Stack EDGE Pro utworzysz zasób IoT Hub.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia** i wybierz pozycję **IoT Edge**.

   ![Wprowadzenie do obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. W obszarze **Włączanie usługi IoT Edge** wybierz pozycję **Dodaj**.

   ![Konfigurowanie obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. W obszarze **Tworzenie usługi IoT Edge** wprowadź ustawienia dla zasobu IoT Hub:

   |Pole   |Wartość    |
   |--------|---------|
   |Subskrypcja      | Subskrypcja używana przez zasób Azure Stack Edge. |
   |Grupa zasobów    | Grupa zasobów używana przez zasób Azure Stack Edge. |
   |Usługa IoT Hub           | Wybierz opcję **Utwórz nową** lub **Użyj istniejącej**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, które są używane przez zasób usługi Azure Stack Edge.     |
   |Nazwa              | Jeśli nie chcesz używać domyślnej nazwy podanej dla nowego zasobu IoT Hub, wprowadź inną nazwę. |

    ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Po zakończeniu ustawień wybierz pozycję **Przegląd + Utwórz**. Sprawdź ustawienia zasobu IoT Hub i wybierz pozycję **Utwórz**.

   Tworzenie zasobów dla zasobu IoT Hub trwa kilka minut. Po utworzeniu zasobu **Przegląd** wskazuje, że usługa IoT Edge jest teraz uruchomiona.

    ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Aby upewnić się, że skonfigurowano rolę obliczeń brzegowych, wybierz pozycję **Właściwości**.

   ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

   Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodaj udziały

W przypadku prostego wdrażania w tym samouczku potrzebne są dwa udziały: jeden udział graniczny i inny udział lokalny Edge.

1. Dodaj udział graniczny na urządzeniu, wykonując następujące czynności:

    1. W przystawce zasób Azure Stack Edge przejdź do pozycji **Oblicz compute > Rozpocznij**.
    2. Na kafelku **Dodaj udziały** wybierz pozycję **Dodaj**.

        ![Dodaj kafelek udostępniania](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. W bloku **Dodaj udział** Podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział brzegowy, zaznacz pole wyboru **Użyj opcji Udostępnij przy użyciu obliczeń brzegowych**.
    5. Wybierz **konto magazynu**, **usługę magazynu**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału brzegowego](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji polecenia Remote Sync ( `rsync` ), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji o `rsync` poleceniu, przejdź do [ `Rsync` dokumentacji](https://www.computerhope.com/unix/rsync.htm).

    > [!NOTE]
    > Aby można było zainstalować udział NFS w ramach obliczeń, Sieć obliczeniowa musi być skonfigurowana w tej samej podsieci co wirtualny adres IP systemu plików NFS. Aby uzyskać szczegółowe informacje na temat konfigurowania sieci obliczeniowej, przejdź do pozycji [Włącz sieć obliczeniową w Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Zostanie utworzony udział graniczny i otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Dodaj udział lokalny z krawędzią na urządzeniu brzegowym, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako krawędź lokalna udziału**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego krawędzi](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Wybierz pozycję **Dodaj udziały** , aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodawanie modułu

Możliwe jest dodanie niestandardowego lub wstępnie skompilowanego modułu. Na tym urządzeniu brzegowym nie ma modułów niestandardowych. Aby dowiedzieć się, jak utworzyć niestandardowy moduł, przejdź do pozycji [opracowywanie modułu języka C# dla urządzenia z Azure Stack EDGE Pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

W tej sekcji dowiesz się, jak dodać niestandardowy moduł do urządzenia IoT Edge utworzonego w programie [opracowywanie modułu C# dla Azure Stack EDGE Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego z krawędzi na urządzeniu brzegowym i przenosi je do udziału brzegowego (chmurowego) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do pozycji **obliczenia graniczne > wprowadzenie**. Na kafelku **Dodawanie modułów** wybierz typ scenariusza jako **prosty**. Wybierz pozycję **Dodaj**.
2. W bloku **Konfigurowanie i Dodawanie modułu** wprowadź następujące wartości:

    
    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa     | Unikatowa nazwa modułu. Ten moduł jest kontenerem platformy Docker, który można wdrożyć na urządzeniu IoT Edge, które jest skojarzone z Azure Stack krawędzią Pro.        |
    |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiedniego obrazu kontenera modułu.        |
    |Wymagane są poświadczenia     | Jeśli ta opcja jest zaznaczona, nazwa użytkownika i hasło są używane do pobierania modułów z pasującym adresem URL.        |
    |Udział wejściowy     | Wybierz udział wejściowy. Udział lokalny krawędzi jest w tym przypadku udziałem wejściowym. Używany tutaj moduł przenosi pliki z udziału lokalnego Edge do udziału granicznego, w którym są przekazywane do chmury.        |
    |Udział danych wyjściowych     | Wybierz udział wyjściowy. Udział graniczny jest w tym przypadku udziałem wyjściowym.        |
    |Typ wyzwalacza     | Wybierz z **pliku** lub **harmonogram**. Wyzwalacz pliku jest uruchamiany za każdym razem, gdy występuje zdarzenie pliku, takie jak zapisanie pliku w udziale wejściowym. Zaplanowany wyzwalacz jest uruchamiany na podstawie harmonogramu zdefiniowanego przez Ciebie.         |
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Zmienne środowiskowe| Informacje opcjonalne, które ułatwią Definiowanie środowiska, w którym będzie uruchamiany moduł.   |

    ![Dodawanie i Konfigurowanie modułu](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł zostanie dodany. Przejdź do strony **Przegląd** . Kafelek **moduły** aktualizuje, aby wskazać, że moduł został wdrożony. 

    ![Wdrożony moduł](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz kafelek **Dodawanie modułu** . Spowoduje to przejście do bloku **moduły** . Na liście modułów Zidentyfikuj wdrożony moduł. Powinien być *uruchomiony* stan środowiska uruchomieniowego modułu, który został dodany.

    ![Wyświetl wdrożony moduł](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. W Eksploratorze plików Połącz się z utworzonymi wcześniej udziałami lokalnymi i krawędziami krawędzi.

    ![Weryfikowanie przekształcenia danych-1](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcenia danych-2](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcenia danych-3](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, można użyć Eksplorator usługi Storage.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
Ukończono proces walidacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Korzystanie z lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)