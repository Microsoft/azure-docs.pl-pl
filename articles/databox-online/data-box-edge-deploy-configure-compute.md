---
title: Samouczek służący do filtrowania, analizowania danych przy użyciu obliczeń na Azure Data Box Edge | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na urządzeniu Data Box Edge i przy jej użyciu przekształcić dane przed wysłaniem ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79239021"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Samouczek: Przekształcanie danych za pomocą Azure Data Box Edge

W tym samouczku opisano sposób konfigurowania roli obliczeniowej na urządzeniu Azure Data Box Edge. Po skonfigurowaniu roli obliczeniowej urządzenie Data Box Edge może przekształcać dane przed wysłaniem ich na platformę Azure.

Wykonanie tej procedury może potrwać około 10 do 15 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu Data Box Edge upewnij się, że zostały spełnione następujące warunki:

- Urządzenie Data Box Edge zostało aktywowane zgodnie z opisem w temacie [Podłączanie, konfigurowanie i aktywowanie urządzenia Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczenia na Data Box Edge, utworzysz zasób IoT Hub.

1. W Azure Portal zasobu Data Box Edge przejdź do omówienia. W okienku po prawej stronie na kafelku **Oblicz** wybierz pozycję **Rozpocznij**.

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na kafelku **Konfigurowanie obliczeń krawędzi** wybierz pozycję **Konfiguruj obliczenia**.
3. W bloku **Konfigurowanie obliczeń krawędzi** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Data Box Edge.     |
    |Nazwa     |Wprowadź nazwę dla zasobu IoT Hub.         |

    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Wybierz przycisk **Utwórz**. Tworzenie zasobów IoT Hub trwa kilka minut. Po utworzeniu zasobu IoT Hub, **Skonfiguruj kafelek Oblicz** aktualizacje, aby pokazać konfigurację obliczeń. Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **Wyświetl obliczenia** na kafelku **Konfiguruj obliczenia** .
    
    ![Wprowadzenie do obliczeń](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Jeśli okno dialogowe **Konfigurowanie obliczeń** zostanie zamknięte przed skojarzeniem IoT Hub z urządzeniem Data Box Edge, IoT Hub zostanie utworzony, ale nie będzie wyświetlany w konfiguracji obliczeniowej. 
    
    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodaj udziały

W przypadku prostego wdrażania w tym samouczku potrzebne są dwa udziały: jeden udział graniczny i inny udział lokalny Edge.

1. Dodaj udział graniczny na urządzeniu, wykonując następujące czynności:

    1. W zasobie Data Box Edge przejdź do pozycji **Oblicz compute > Rozpocznij**.
    2. Na kafelku **Dodaj udziały** wybierz pozycję **Dodaj**.
    3. W bloku **Dodaj udział** Podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział brzegowy, zaznacz pole wyboru **Użyj opcji Udostępnij przy użyciu obliczeń brzegowych**.
    5. Wybierz **konto magazynu**, **usługę magazynu**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału brzegowego](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Jeśli utworzono lokalny udział NFS, użyj następującej opcji synchronizacji zdalnej (rsync), aby skopiować pliki do udziału:

    `rsync <source file path> < destination file path>`

    Aby uzyskać więcej informacji na temat polecenia rsync, przejdź do [dokumentacji narzędzia Rsync](https://www.computerhope.com/unix/rsync.htm).

    Zostanie utworzony udział graniczny i otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów może zostać zaktualizowana, ale musisz poczekać na ukończenie tworzenia udziału.

2. Dodaj udział lokalny z krawędzią na urządzeniu brzegowym, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako krawędź lokalna udziału**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego krawędzi](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Wybierz pozycję **Dodaj udziały** , aby wyświetlić zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Dodawanie modułu

Możliwe jest dodanie niestandardowego lub wstępnie skompilowanego modułu. Na tym urządzeniu brzegowym nie ma modułów niestandardowych. Aby dowiedzieć się, jak utworzyć moduł niestandardowy, przejdź do artykułu [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md).

W tej sekcji dodasz do urządzenia usługi IoT Edge moduł niestandardowy utworzony w temacie [Tworzenie modułu C# dla urządzenia Data Box Edge](data-box-edge-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego z krawędzi na urządzeniu brzegowym i przenosi je do udziału brzegowego (chmurowego) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do pozycji **obliczenia graniczne > wprowadzenie**. Na kafelku **Dodawanie modułów** wybierz typ scenariusza jako **prosty**. Wybierz pozycję **Dodaj**.
2. W bloku **Konfigurowanie i Dodawanie modułu** wprowadź następujące wartości:

    
    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa     | Unikatowa nazwa modułu. Ten moduł jest kontenerem platformy Docker, który można wdrożyć na urządzeniu IoT Edge, które jest skojarzone z Data Box Edge.        |
    |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiedniego obrazu kontenera modułu.        |
    |Wymagane są poświadczenia     | Jeśli ta opcja jest zaznaczona, nazwa użytkownika i hasło są używane do pobierania modułów z pasującym adresem URL.        |
    |Udział wejściowy     | Wybierz udział wejściowy. Udział lokalny krawędzi jest w tym przypadku udziałem wejściowym. Używany tutaj moduł przenosi pliki z udziału lokalnego Edge do udziału granicznego, w którym są przekazywane do chmury.        |
    |Udział danych wyjściowych     | Wybierz udział wyjściowy. Udział graniczny jest w tym przypadku udziałem wyjściowym.        |
    |Typ wyzwalacza     | Wybierz z **pliku** lub **harmonogram**. Wyzwalacz pliku jest uruchamiany za każdym razem, gdy wystąpi zdarzenie pliku, takie jak plik jest zapisywana w udziale wejściowym. Zaplanowany wyzwalacz jest uruchamiany na podstawie harmonogramu zdefiniowanego przez użytkownika.         |
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Zmienne środowiskowe| Informacje opcjonalne, które ułatwią Definiowanie środowiska, w którym będzie uruchamiany moduł.   |

    ![Dodawanie i Konfigurowanie modułu](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Wybierz pozycję **Dodaj**. Moduł zostanie dodany. Kafelek **Dodaj moduł** zostanie zaktualizowany, aby wskazać, że moduł został wdrożony. 

    ![Wdrożony moduł](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Weryfikowanie przekształcania danych i transferu

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. Wybierz kafelek **Dodawanie modułu** . Spowoduje to przejście do bloku **moduły** . Na liście modułów Zidentyfikuj wdrożony moduł. Powinien być *uruchomiony*stan środowiska uruchomieniowego modułu, który został dodany.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  W Eksploratorze plików Połącz się z utworzonymi wcześniej udziałami lokalnymi i krawędziami krawędzi.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Dodaj dane do udziału lokalnego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do Eksploratora usługi Storage.

    ![Weryfikowanie przekształcania danych](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Ukończono proces walidacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Edge za pomocą lokalnego internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md)
