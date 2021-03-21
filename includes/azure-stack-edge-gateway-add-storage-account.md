---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 0c7e011cf8445164e0931f71e390813c9134dd89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200961"
---
1. W [Azure Portal](https://portal.azure.com/)wybierz Azure Stack zasobów brzegowych, a następnie przejdź do **omówienia**. Urządzenie powinno być w trybie online. Przejdź do **bramy magazynu w chmurze > kont magazynu**.

2. Wybierz pozycję **+ Dodaj konto magazynu** na pasku poleceń urządzenia. 

   ![Dodawanie konta magazynu](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. W okienku **Dodawanie konta magazynu Edge** określ następujące ustawienia:

    1. Podaj unikatową nazwę konta magazynu Edge na urządzeniu. Nazwy kont magazynu mogą zawierać tylko małe litery i cyfry. Znaki specjalne są niedozwolone. Nazwa konta magazynu musi być unikatowa w obrębie urządzenia (nie na urządzeniach).

    2. Podaj opcjonalny opis informacji dotyczących danych, które są przechowywane na koncie magazynu.  
    
    3. Domyślnie konto magazynu brzegowego jest mapowane na konto usługi Azure Storage w chmurze, a dane z konta magazynu są automatycznie wypychane do chmury. Określ konto usługi Azure Storage, do którego jest zamapowana konto magazynu Edge.

    4. Utwórz nowy kontener lub wybierz go z istniejącego kontenera na koncie usługi Azure Storage. Wszystkie dane z urządzenia, które są zapisywane na koncie magazynu brzegowego, są automatycznie przekazywane do wybranego kontenera magazynu na mapowanym koncie usługi Azure Storage.

    5. Po określeniu wszystkich opcji konta magazynu wybierz pozycję **Dodaj** , aby utworzyć konto magazynu Edge. Otrzymasz powiadomienie o pomyślnym utworzeniu konta magazynu Edge. Nowe konto magazynu Edge zostanie wyświetlone na liście kont magazynu w Azure Portal.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. W przypadku wybrania tego nowego konta magazynu i przejścia do opcji **klucze dostępu** można znaleźć punkt końcowy usługi BLOB i nazwę konta magazynu. Skopiuj te informacje wraz z kluczami dostępu, aby nawiązać połączenie z kontem magazynu Edge.

    ![Dodawanie konta magazynu 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Klucze dostępu można uzyskać, łącząc się z [lokalnymi interfejsami API urządzenia przy użyciu Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
