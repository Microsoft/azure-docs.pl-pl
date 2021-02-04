---
title: Użyj modułu IoT Edge, aby wdrożyć obciążenie obliczeniowe na Azure Stack EDGE Pro z procesorem GPU | Microsoft Docs
description: Dowiedz się, jak uruchamiać obciążenie obliczeniowe przy użyciu wstępnie utworzonego modułu IoT Edge na urządzeniu GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 44265efd4d125bb2701742a490fc883c48f1877b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550995"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Samouczek: uruchamianie obciążenia obliczeniowego za pomocą modułu IoT Edge na Azure Stack brzegowych procesorów GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano sposób uruchamiania obciążenia obliczeniowego przy użyciu modułu IoT Edge na urządzeniu z systemem Azure Stack Edge. Po skonfigurowaniu obliczeń urządzenie przekształci dane przed wysłaniem ich do platformy Azure.

Wykonanie tej procedury może potrwać około 10 do 15 minut.


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu z systemem Azure Stack EDGE Pro GPU upewnij się, że:

- Twoje urządzenie Azure Stack EDGE Pro zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).
- Masz moduł IoT Edge, który można uruchomić na danych. W tym samouczku użyto `filemove2` modułu, który przenosi dane z udziału lokalnego Edge na urządzenie do udziału brzegowego, z którego dane są przesyłane do konta usługi Azure Storage.


## <a name="configure-compute"></a>Konfigurowanie obliczeń

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Dodaj udziały

W przypadku prostego wdrażania w tym samouczku potrzebne są dwa udziały: jeden udział graniczny i inny udział lokalny Edge.

1. Aby dodać udział graniczny na urządzeniu, wykonaj następujące czynności:

    1. W zasobie usługi Azure Stack Edge przejdź do pozycji **bramy magazynu w chmurze > udziały**.
    2. Na pasku poleceń wybierz pozycję **+ Dodaj udział**.
    3. W bloku **Dodaj udział** Podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział brzegowy, zaznacz pole wyboru **Użyj opcji Udostępnij przy użyciu obliczeń brzegowych**.
    5. Wybierz **konto magazynu**, **usługę magazynu**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału brzegowego](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Aby można było zainstalować udział NFS w ramach obliczeń, Sieć obliczeniowa musi być skonfigurowana w tej samej podsieci co wirtualny adres IP systemu plików NFS. Aby uzyskać szczegółowe informacje na temat konfigurowania sieci obliczeniowej, przejdź do pozycji [Włącz sieć obliczeniową w Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Zostanie utworzony udział graniczny i otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Aby dodać udział lokalny na urządzeniu, powtórz wszystkie kroki w poprzednim kroku i zaznacz pole wyboru **Konfiguruj jako krawędź lokalnego udziału**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego krawędzi](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji o `rsync` poleceniu, przejdź do [dokumentacji rsync](https://www.computerhope.com/unix/rsync.htm).
 
3. Przejdź do pozycji **brama magazynu w chmurze > udziały** , aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodawanie modułu

Możliwe jest dodanie niestandardowego lub wstępnie skompilowanego modułu. Urządzenie nie jest dołączone do modułów wstępnie utworzonych lub niestandardowych. Aby dowiedzieć się, jak utworzyć niestandardowy moduł, przejdź do pozycji [opracowywanie modułu języka C# dla urządzenia z Azure Stack EDGE Pro](azure-stack-edge-j-series-create-iot-edge-module.md).

W tej sekcji dowiesz się, jak dodać niestandardowy moduł do urządzenia IoT Edge utworzonego w programie [opracowywanie modułu C# dla Azure Stack EDGE Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego z krawędzi na urządzeniu brzegowym i przenosi je do udziału brzegowego (chmurowego) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

Aby dodać moduł, wykonaj następujące czynności:

1. Przejdź do **modułów > IoT Edge**. Na pasku poleceń wybierz pozycję **+ Dodaj moduł**. 

2. W bloku **Dodaj moduł** wprowadź następujące wartości:

    
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

    ![Dodawanie i Konfigurowanie modułu](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł zostanie dodany. Strona **modułów > IoT Edge** jest aktualizowana w celu wskazania, że moduł został wdrożony. Powinien być *uruchomiony* stan środowiska uruchomieniowego modułu, który został dodany.

    ![Wdrożony moduł](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł działa i przetwarza dane zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Aby sprawdzić, czy moduł działa i przetwarzał dane zgodnie z oczekiwaniami, wykonaj następujące czynności:


1. W Eksploratorze plików Połącz się z utworzonymi wcześniej udziałami lokalnymi i krawędziami krawędzi. Zobacz kroki 

    ![Łączenie z lokalnymi i granicznymi udziałami w chmurze](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Dodaj dane do udziału lokalnego.

    ![Skopiowano plik do udziału lokalnego Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Dane zostaną przeniesione do udziału chmurowego.

    ![Plik przeniesiony do udziału w chmurze Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, możesz użyć Eksplorator usługi Storage lub usługi Azure Storage w portalu.

    ![Weryfikowanie danych na koncie magazynu](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
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
> [Korzystanie z lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
