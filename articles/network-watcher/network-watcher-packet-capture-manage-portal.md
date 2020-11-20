---
title: Zarządzanie przechwytywaniem pakietów — Azure Portal
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak zarządzać funkcją przechwytywania pakietów Network Watcher przy użyciu Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 099ead37b8d6b46b767ef59ff24a7e7ff9dc9e3c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966449"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu portalu

Przechwytywanie pakietów Network Watcher umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów pomaga zdiagnozować anomalie w sieci, zarówno w sposób aktywny, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach w sieci, debugowanie komunikacji klient-serwer i wiele innych. Możliwe jest zdalne wyzwalanie przechwytywania pakietów, co ułatwia nawiązanie ręcznego uruchamiania przechwycenia pakietu na odpowiedniej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas.

Ten artykuł zawiera informacje na temat uruchamiania, zatrzymywania, pobierania i usuwania przechwytywania pakietów. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przechwytywanie pakietów wymaga następującej łączności wychodzącej TCP:
- na wybrane konto magazynu przez port 443
- do 169.254.169.254 przez port 80
- do 168.63.129.16 przez port 8037

> [!NOTE]
> Porty wymienione w dwóch ostatnich przypadkach są wspólne dla wszystkich funkcji Network Watcher, które obejmują Network Watcher rozszerzenia i mogą być czasami zmieniane.


Jeśli sieciowa Grupa zabezpieczeń jest skojarzona z interfejsem sieciowym lub podsiecią, w której znajduje się interfejs sieciowy, upewnij się, że istnieją reguły zezwalające na poprzednie porty. Podobnie Dodawanie zdefiniowanych przez użytkownika tras ruchu do sieci może uniemożliwić łączność z powyższymi wymienionymi adresami IP i portami. Upewnij się, że są one dostępne. 

## <a name="start-a-packet-capture"></a>Rozpocznij przechwytywanie pakietu

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com) i wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **Network Watcher** w **sekcji Sieć**.
2. Wybierz pozycję **przechwytywanie pakietów** w obszarze **Narzędzia diagnostyczne sieci**. Wszystkie istniejące przechwycenia pakietu są wyświetlane niezależnie od ich stanu.
3. Wybierz pozycję **Dodaj** , aby utworzyć przechwycenie pakietu. Można wybrać wartości dla następujących właściwości:
   - **Subskrypcja**: subskrypcja, w której ma zostać utworzona przechwycenie pakietu, znajduje się w temacie.
   - **Grupa zasobów**: Grupa zasobów maszyny wirtualnej.
   - **Docelowa maszyna wirtualna**: maszyna wirtualna, dla której chcesz utworzyć przechwycenie pakietu.
   - **Nazwa przechwytywania pakietów**: Nazwa przechwycenia pakietu.
   - **Konto magazynu lub plik**: wybierz pozycję **konto magazynu**, **plik** lub oba. Po wybraniu opcji **plik** przechwytywania zostanie zapisany w ścieżce na maszynie wirtualnej.
   - **Ścieżka do pliku lokalnego**: ścieżka lokalna na maszynie wirtualnej, w której zostanie zapisane przechwytywanie pakietów (prawidłowy tylko wtedy, gdy *plik* jest zaznaczony). Ścieżka musi być prawidłową ścieżką. W przypadku korzystania z maszyny wirtualnej z systemem Linux ścieżka musi rozpoczynać się od */var/Captures*.
   - **Konta magazynu**: Wybierz istniejące konto magazynu, jeśli wybrano pozycję *konto* magazynu. Ta opcja jest dostępna tylko w przypadku wybrania opcji **Magazyn**.
   
     > [!NOTE]
     > Konta usługi Premium Storage nie są obecnie obsługiwane do przechowywania przechwytywania pakietów.

   - **Maksymalne bajty na pakiet**: liczba bajtów z każdego przechwytywanego pakietu. Jeśli pole pozostanie puste, wszystkie bajty są przechwytywane.
   - **Maksymalna liczba bajtów na sesję**: łączną liczbę przechwytywanych bajtów. Po osiągnięciu wartości zostanie zatrzymane przechwytywanie pakietów.
   - **Limit czasu (w sekundach)**: limit czasu przed zatrzymaniem przechwytywania pakietów. Wartość domyślna to 18 000 sekund.
   - Filtrowanie (opcjonalne). Wybierz pozycję **+ Dodaj filtr**
     - **Protokół**: protokół służący do filtrowania przechwytywania pakietów. Dostępne wartości to TCP, UDP i any.
     - **Lokalny adres IP**: filtruje przechwycenie pakietu dla pakietów, w których lokalny adres IP jest zgodny z tą wartością.
     - **Port lokalny**: filtruje przechwytywanie pakietów dla pakietów, w których port lokalny jest zgodny z tą wartością.
     - **Zdalny adres IP**: filtruje przechwytywanie pakietów dla pakietów, w których zdalny adres IP jest zgodny z tą wartością.
     - **Port zdalny**: filtruje przechwytywanie pakietów dla pakietów, w których Port zdalny jest zgodny z tą wartością.
    
     > [!NOTE]
     > Wartości portów i adresów IP mogą być pojedynczą wartością, zakresem wartości lub zakresem, na przykład 80-1024, dla portu. Można zdefiniować dowolną liczbę filtrów.

4. Wybierz przycisk **OK**.

Po upływie limitu czasu skonfigurowanego dla przechwycenia pakietu przechwycenie pakietu zostanie zatrzymane i będzie można je przejrzeć. Możesz również ręcznie zatrzymać sesję przechwytywania pakietów.

> [!NOTE]
> Portal automatycznie:
>  * Tworzy obserwator sieciowy w tym samym regionie co region, w którym zaznaczono maszynę wirtualną, jeśli region nie ma już obserwatora sieci.
>  * Dodaje rozszerzenie maszyny wirtualnej *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) lub [Windows](../virtual-machines/extensions/network-watcher-windows.md) do maszyny wirtualnej, jeśli nie jest jeszcze zainstalowana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietu

1. W widoku przechwytywania pakietów wybierz pozycję **...** po prawej stronie przechwytywania pakietów lub kliknij prawym przyciskiem myszy istniejący przechwycenie pakietu, a następnie wybierz polecenie **Usuń**.
2. Zostanie wyświetlony monit o potwierdzenie, że chcesz usunąć przechwycenie pakietu. Wybierz pozycję **Tak**.

> [!NOTE]
> Usunięcie przechwycenia pakietu nie powoduje usunięcia pliku przechwytywania na koncie magazynu ani na maszynie wirtualnej.

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietu

W widoku przechwytywania pakietów wybierz pozycję **...** po prawej stronie przechwytywania pakietów lub kliknij prawym przyciskiem myszy istniejący przechwycenie pakietu, a następnie wybierz polecenie **Zatrzymaj**.

## <a name="download-a-packet-capture"></a>Pobieranie przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów plik przechwytywania zostanie przekazany do magazynu obiektów blob lub do pliku lokalnego na maszynie wirtualnej. Lokalizacja przechowywania przechwycenia pakietu jest definiowana podczas tworzenia przechwytywania pakietów. Wygodnym narzędziem do uzyskiwania dostępu do plików przechwytywania zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można [pobrać](https://storageexplorer.com/).

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

W przypadku wybrania **pliku** podczas tworzenia przechwytywania można wyświetlić lub pobrać plik ze ścieżki skonfigurowanej na maszynie wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zautomatyzować przechwytywanie pakietów przy użyciu alertów maszyny wirtualnej, zobacz artykuł [Tworzenie wyzwalanego przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md).
- Aby określić, czy określony ruch jest dozwolony w lub poza maszyną wirtualną, zobacz [diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej](diagnose-vm-network-traffic-filtering-problem.md).