---
title: Utwórz zasoby techniczne dla oferty maszyny wirtualnej w portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć zasoby techniczne dla oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: c6ca017e18a83f0745140cba15d9c894e5a21c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148134"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Utwórz zasoby techniczne dla oferty maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

Ta sekcja zawiera szczegółowe instrukcje dotyczące tworzenia i konfigurowania zasobów technicznych dla oferty maszyny wirtualnej (VM) dla witryny Azure Marketplace.  Maszyna wirtualna zawiera dwa składniki: wirtualny dysk twardy (VHD) rozwiązania i opcjonalne skojarzone dyski danych.  

- *Wirtualne dyski twarde (VHD)*, zawierające system operacyjny i rozwiązanie, które zostaną wdrożone w ramach oferty portalu Azure Marketplace. Proces przygotowywania dysku VHD różni się w zależności od tego, czy jest to maszyna wirtualna oparta na systemie Linux, Windows, czy niestandardowa.
- *Dyski z danymi* reprezentują dedykowany magazyn trwały dla maszyny wirtualnej. *Nie* należy używać dysku VHD rozwiązania (na przykład `C:` dysku) do przechowywania informacji trwałych.

Obraz maszyny wirtualnej zawiera dysk z systemem operacyjnym i co najmniej jeden dysk z danymi. Na dysku jest wymagany jeden wirtualny dysk twardy. Nawet puste dyski danych wymagają utworzenia dysku VHD.
Musisz skonfigurować system operacyjny maszyny wirtualnej, rozmiar maszyny wirtualnej, porty do otwarcia oraz do 15 dołączonych dysków danych.

> [!TIP] 
> Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez jednostkę SKU. Klienci nie mogą usunąć dysków, które są częścią obrazu w czasie wdrażania, ale mogą zawsze dodawać dyski w trakcie wdrażania lub po nim. 

> [!IMPORTANT]
> *Nie zmieniaj liczby dysków w nowej wersji obrazu.* Jeśli konieczne jest ponowne skonfigurowanie dysków danych w obrazie, należy zdefiniować nową jednostkę SKU. Opublikowanie nowej wersji obrazu z różnymi liczbami dysków będzie miało możliwość przerwania nowego wdrożenia opartego na nowej wersji obrazu w przypadku automatycznego skalowania, automatycznego wdrażania rozwiązań za pośrednictwem Azure Resource Manager szablonów i innych scenariuszy.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów zabiera czas i wymaga znajomości wiedzy technicznej platformy Azure oraz technologii używanych do tworzenia oferty. Poza domeną rozwiązania Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft: 
-    Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/) 
-    [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-    Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
-    Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-    Wiedza o pracy w formacie [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Sugerowane narzędzia 

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie dyskami VHD i maszynami wirtualnymi:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zalecamy Dodawanie następujących narzędzi do środowiska deweloperskiego: 

-    [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przeglądanie dostępnych narzędzi na stronie [usługi Azure narzędzia deweloperskie](https://azure.microsoft.com/tools/) i, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Następne kroki

W kolejnych artykułach w tej sekcji omówiono kroki tworzenia i rejestrowania tych zasobów maszyn wirtualnych:

1. [Tworzenie wirtualnego dysku twardego zgodnego z platformą Azure](./cpp-create-vhd.md) wyjaśnia, jak utworzyć dysk VHD oparty na systemie Linux lub Windows, który jest zgodny z platformą Azure.  Zawiera najlepsze rozwiązania, takie jak rozmiar, poprawki i przygotowywanie maszyny wirtualnej do przekazania.

2. [Aby nawiązać połączenie z maszyną wirtualną](./cpp-connect-vm.md) , Wyjaśnij, jak zdalnie nawiązać połączenie z nowo utworzoną maszyną wirtualną i zalogować się do niej.  W tym artykule wyjaśniono również, jak zatrzymać maszynę wirtualną, aby zaoszczędzić na kosztach użycia.

3. [Konfiguracja maszyny wirtualnej](./cpp-configure-vm.md) wyjaśnia, jak wybrać prawidłowy rozmiar wirtualnego dysku twardego, uogólnić obraz, zastosować najnowsze aktualizacje (poprawki) i zaplanować konfiguracje niestandardowe.

4. [Wdróż maszynę wirtualną na podstawie wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md) objaśnia sposób rejestrowania maszyny wirtualnej na podstawie dysku VHD wdrożonego na platformie Azure.  Zawiera listę wymaganych narzędzi oraz sposób ich użycia w celu utworzenia obrazu maszyny wirtualnej użytkownika, a następnie wdrożenia go na platformie Azure przy użyciu skryptów [Microsoft Azure Portal](https://ms.portal.azure.com/) lub PowerShell. 

5. [Certyfikowanie obrazu maszyny wirtualnej](./cpp-certify-vm.md) wyjaśnia sposób testowania i przesyłania obrazu maszyny wirtualnej na potrzeby certyfikacji w portalu Azure Marketplace. Wyjaśniono, gdzie uzyskać *Narzędzie do testowania certyfikacji dla certyfikowanych narzędzi platformy Azure* oraz jak używać tego narzędzia do certyfikowania obrazu maszyny wirtualnej. 

6. [Uzyskaj identyfikator URI SAS](./cpp-get-sas-uri.md) wyjaśnia, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego (SAS) dla obrazów maszyn wirtualnych.
 
W ramach [wspólnego artykułu często występujące problemy z adresem URL sygnatury dostępu współdzielonego](./cpp-common-sas-url-issues.md) zawierają kilka typowych problemów, które mogą wystąpić przy użyciu identyfikatorów URI SAS i odpowiednich możliwych rozwiązań.

Po wykonaniu wszystkich powyższych kroków będziesz gotowy do [opublikowania oferty maszyny wirtualnej](./cpp-publish-offer.md) w portalu Azure Marketplace.
