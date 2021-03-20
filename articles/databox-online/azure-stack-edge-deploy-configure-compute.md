---
title: Samouczek służący do filtrowania, analizowania danych przy użyciu obliczeń na Azure Stack EDGE Pro | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową w programie Azure Stack EDGE Pro i używać jej do przekształcania danych przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 10741fbf2deb61d63f444ff9e2247bc59f41af38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97968725"
---
# <a name="tutorial-transform-the-data-with-azure-stack-edge-pro"></a>Samouczek: Przekształcanie danych przy użyciu Azure Stack EDGE Pro

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

- Aktywowano urządzenie Azure Stack EDGE Pro zgodnie z opisem w temacie [Connect, Set up i activate Azure Stack EDGE Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

W celu skonfigurowania obliczeń na Azure Stack EDGE Pro utworzysz zasób IoT Hub.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia**. W okienku po prawej stronie wybierz pozycję **IoT Edge**.

    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

1. Na kafelku **włącz IoT Edge** wybierz pozycję **Dodaj**. Dzięki temu usługa IoT Edge, która umożliwia wdrażanie modułów IoT Edge lokalnie na urządzeniu.

    ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

1. W bloku **Tworzenie usługi IoT Edge** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Subskrypcja     |Wybierz subskrypcję dla zasobu IoT Hub. Możesz użyć tej samej subskrypcji, która jest używana przez zasób Azure Stack Edge.         |
    |Grupa zasobów     |Wybierz grupę zasobów dla zasobu IoT Hub. Możesz użyć tej samej grupy zasobów, która jest używana przez zasób Azure Stack Edge.         |
    |Usługa IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, które są używane przez zasób usługi Azure Stack Edge.     |
    |Nazwa     |Wprowadź nazwę dla zasobu IoT Hub.         |

    ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

4. Wybierz pozycję **Przejrzyj i utwórz**. Tworzenie zasobów IoT Hub trwa kilka minut. Po utworzeniu zasobu IoT Hub **Omówienie** aktualizacji, aby wskazać, że usługa IoT Edge jest uruchomiona. 

    ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-deploy-configure-compute/configure-compute-4.png)    
    
    Gdy usługa IoT Edge jest skonfigurowana na urządzeniu brzegowym, tworzy dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.

    Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **IoT Edge > właściwości usługi** i Wyświetl urządzenie IoT i IoT Edge.

    ![Wprowadzenie do obliczeń obliczeniowych 5](./media/azure-stack-edge-deploy-configure-compute/configure-compute-5.png) 

## <a name="add-shares"></a>Dodaj udziały

W przypadku prostego wdrażania w tym samouczku potrzebne są dwa udziały: jeden udział graniczny i inny udział lokalny Edge.

1. Dodaj udział graniczny na urządzeniu, wykonując następujące czynności:

    1. W zasobie Azure Stack Edge przejdź do pozycji **IoT Edge > udziały**.
    2. Na pasku poleceń wybierz pozycję **+ Dodaj udział**.
    3. W bloku **Dodaj udział** Podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział brzegowy, zaznacz pole wyboru **Użyj opcji Udostępnij przy użyciu obliczeń brzegowych**.
    5. Wybierz **konto magazynu**, **usługę magazynu**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału brzegowego](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm).

    Zostanie utworzony udział graniczny i otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Dodaj udział lokalny z krawędzią na urządzeniu brzegowym, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako krawędź lokalna udziału**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego krawędzi](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Przejdź do **IoT Edge > udziałów** , aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodawanie modułu

Możliwe jest dodanie niestandardowego lub wstępnie skompilowanego modułu. Na tym urządzeniu brzegowym nie ma modułów niestandardowych. Aby dowiedzieć się, jak utworzyć niestandardowy moduł, przejdź do pozycji [opracowywanie modułu języka C# dla urządzenia z Azure Stack EDGE Pro](azure-stack-edge-create-iot-edge-module.md).

W tej sekcji dowiesz się, jak dodać niestandardowy moduł do urządzenia IoT Edge utworzonego w programie [opracowywanie modułu C# dla Azure Stack EDGE Pro](azure-stack-edge-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego z krawędzi na urządzeniu brzegowym i przenosi je do udziału brzegowego (chmurowego) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do **modułów > IoT Edge**. Na pasku poleceń urządzenia wybierz pozycję **+ Dodaj moduł**.
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

    ![Dodawanie i Konfigurowanie modułu](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł zostanie dodany. Na stronie **przegląd > IoT Edge** są aktualizowane aktualizacje wskazujące, że moduł został wdrożony. 

    ![Wdrożony moduł](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz kafelek **Dodawanie modułu** . Spowoduje to przejście do bloku **moduły** . Na liście modułów Zidentyfikuj wdrożony moduł. Powinien być *uruchomiony* stan środowiska uruchomieniowego modułu, który został dodany.

    ![Weryfikowanie przekształcania danych](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. W Eksploratorze plików Połącz się z utworzonymi wcześniej udziałami lokalnymi i krawędziami krawędzi.

    ![Weryfikowanie przekształcenia danych 2](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcenia danych 3](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcenia danych 4](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do Eksploratora usługi Storage.

    ![Weryfikowanie przekształcenia danych 5](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
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
> [Używanie lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
