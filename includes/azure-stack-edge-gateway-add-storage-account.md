---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105578"
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

    Klucze dostępu można uzyskać, łącząc się z [lokalnymi interfejsami API urządzenia przy użyciu Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).