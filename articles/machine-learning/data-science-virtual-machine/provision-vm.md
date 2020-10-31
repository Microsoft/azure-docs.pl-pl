---
title: 'Szybki Start: Tworzenie Data Science Virtual Machine systemu Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i Utwórz Data Science Virtual Machine na platformie Azure na potrzeby analiz i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: e807cde2d99cf23d459b395444d642e46ada32ab
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93071457"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Szybki Start: Konfigurowanie Data Science Virtual Machine dla systemu Windows

Zapoznaj się z systemem Windows Server 2019 Data Science Virtual Machine.

## <a name="prerequisite"></a>Wymaganie wstępne

Aby utworzyć Data Science Virtual Machine systemu Windows, musisz mieć subskrypcję platformy Azure. [Wypróbuj bezpłatnie platformę Azure](https://azure.com/free).
Pamiętaj, że bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych z możliwością obsługi procesora GPU.

## <a name="create-your-dsvm"></a>Tworzenie DSVM

Aby utworzyć wystąpienie DSVM:

1. Przejdź do [Azure Portal](https://portal.azure.com) może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli jeszcze nie zalogowano się.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do nauki o danych" i wybierając pozycję "Data Science Virtual Machine-Windows 2019".

1. Wybierz przycisk **Utwórz** u dołu.

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".

1. Wypełnij kartę **podstawowe** :
      * **Subskrypcja** : Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
      * **Grupa zasobów** : Utwórz nową grupę lub Użyj istniejącej.
      * **Nazwa maszyny wirtualnej** : Wprowadź nazwę maszyny wirtualnej. Jest to sposób ich wyświetlania w Azure Portal.
      * **Lokalizacja** : wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obraz** : pozostaw wartość domyślną.
      * **Rozmiar** : powinno to być automatycznie wypełniane rozmiarem odpowiednim dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/windows/sizes.md).
      * **Nazwa użytkownika** : Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure.
      * **Hasło** : wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
1. Wybierz pozycję **Przejrzyj i utwórz** .
1. **Przegląd + tworzenie**
   * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
   * Wybierz pozycję **Utwórz** .


> [!NOTE]
> * Nie są naliczane opłaty licencyjne za oprogramowanie, które jest wstępnie załadowane na maszynę wirtualną. Koszt obliczeń jest płacony dla rozmiaru serwera wybranego w kroku **rozmiar** .
> * Inicjowanie obsługi trwa od 10 do 20 minut. Stan maszyny wirtualnej można wyświetlić na Azure Portal.

## <a name="access-the-dsvm"></a>Dostęp do DSVM

Po utworzeniu maszyny wirtualnej i zainicjowaniu jej obsługi postępuj zgodnie z instrukcjami wyświetlanymi w celu [nawiązania połączenia z maszyną wirtualną opartą na platformie Azure](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base). Użyj poświadczeń konta administratora, które zostały skonfigurowane w kroku **podstawowe informacje** o tworzeniu maszyny wirtualnej. 

Możesz zacząć korzystać z narzędzi, które są zainstalowane i skonfigurowane na maszynie wirtualnej. Do wielu narzędzi można uzyskać dostęp za pomocą kafelków menu **Start** i ikon pulpitu.

Możesz również dołączyć DSVM do Azure Notebooks uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy Bezpłatna usługa. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów notesów](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z narzędziami w DSVM, otwierając menu **Start** .
* Dowiedz się więcej na temat Azure Machine Learning, odczytując informacje o tym, [co jest Azure Machine Learning?](../overview-what-is-azure-ml.md) i wypróbować [samouczki](../index.yml).
* Zapoznaj się z artykułem [nauka danych w systemie Windows Data Science Virtual Machine na platformie Azure](./vm-do-ten-things.md)

