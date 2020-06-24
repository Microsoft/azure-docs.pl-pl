---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76759856"
---
Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w witrynie Azure Portal, wykonaj następujące kroki:

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz wartość **Konta magazynu**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **konta magazynu**.
2. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
3. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
4. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów, jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu przedstawiający sposób tworzenia grupy zasobów w portalu](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
6. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
7. Pozostaw poniższe pola ustawione na wartości domyślne:

   |Pole  |Wartość  |
   |---------|---------|
   |Model wdrażania     |Resource Manager         |
   |Wydajność     |Standardowa (Standard)         |
   |Rodzaj konta     |StorageV2 (ogólnego przeznaczenia wersja 2)         |
   |Replikacja     |Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)         |
   |Warstwa dostępu     |Gorąca         |

8. Jeśli planujesz używać [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), wybierz kartę **Zaawansowane** , a następnie ustaw **hierarchiczną przestrzeń nazw** na wartość **włączone**.
9. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
10. Wybierz przycisk **Utwórz**.

Aby uzyskać więcej informacji na temat typów kont magazynu i innych ustawień konta magazynu, zobacz [Azure storage account overview (Omówienie kont usługi Azure Storage)](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Azure Resource Manager przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
