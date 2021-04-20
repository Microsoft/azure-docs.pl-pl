---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718204"
---
Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w witrynie Azure Portal, wykonaj następujące kroki:

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz wartość **Konta magazynu**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **pozycję Konta magazynu.**
1. W **wyświetlonym oknie** Konta magazynu wybierz pozycję **+ Nowy.**
1. W **bloku Podstawowe** wybierz subskrypcję, w której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz żądaną grupę zasobów lub utwórz nową grupę zasobów.  Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, [zobacz Azure Resource Manager omówienie .](../articles/azure-resource-manager/management/overview.md)
1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi również mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz region dla konta magazynu lub użyj regionu domyślnego.
1. Wybierz warstwę wydajności. Warstwa domyślna to *Standardowa.*
1. Określ sposób replikowania konta magazynu. Domyślną opcją nadmiarowości jest *magazyn geograficznie nadmiarowy (GRS).* Aby uzyskać więcej informacji na temat dostępnych opcji replikacji, zobacz [Nadmiarowość usługi Azure Storage.](../articles/storage/common/storage-redundancy.md)
1. Dodatkowe opcje są dostępne w opcjach **Zaawansowane,** **Sieć,** **Ochrona danych** i **Tagi.** Aby użyć Azure Data Lake Storage, wybierz blok **Zaawansowane,** a następnie ustaw **opcję Hierarchiczna** przestrzeń nazw na wartość **Włączone.** Aby uzyskać więcej informacji, [zobacz Azure Data Lake Storage Gen2 Wprowadzenie](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz przycisk **Utwórz**.

Na poniższej ilustracji przedstawiono ustawienia nowego konta magazynu **w** bloku Podstawowe:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia konta magazynu w Azure Portal." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
