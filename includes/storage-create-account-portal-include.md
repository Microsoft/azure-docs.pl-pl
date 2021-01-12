---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109431"
---
Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w witrynie Azure Portal, wykonaj następujące kroki:

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz wartość **Konta magazynu**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **konta magazynu**.
1. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Na karcie **podstawowe** wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz żądaną grupę zasobów lub Utwórz nową grupę zasobów.  Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [omówienie Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
1. Wybierz warstwę wydajności. Warstwa domyślna to *standardowa*.
1. Ustaw wartość pola **rodzaj konta** na *Magazyn v2 (ogólnego przeznaczenia w wersji 2)*.
1. Określ sposób replikowania konta magazynu. Domyślna opcja replikacji to *Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)*. Aby uzyskać więcej informacji na temat dostępnych opcji replikacji, zobacz [nadmiarowość usługi Azure Storage](../articles/storage/common/storage-redundancy.md).
1. Dodatkowe opcje są dostępne na kartach **sieci**, **Ochrona danych**, **Zaawansowane** i **Tagi** . Aby użyć Azure Data Lake Storage, wybierz kartę **Zaawansowane** , a następnie ustaw **hierarchiczną przestrzeń nazw** na wartość **włączone**. Aby uzyskać więcej informacji, zobacz [wprowadzenie Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz przycisk **Utwórz**.

Na poniższej ilustracji przedstawiono ustawienia na karcie **podstawowe** dla nowego konta magazynu:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia konta magazynu w witrynie Azure Portal":::
