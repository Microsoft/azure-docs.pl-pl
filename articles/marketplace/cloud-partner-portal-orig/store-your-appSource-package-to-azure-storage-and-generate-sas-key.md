---
title: Przechowuj pakiet AppSource w usłudze Azure Storage i Wygeneruj adres URL z kluczem sygnatury dostępu współdzielonego
description: Szczegóły kroków wymaganych do przekazania i zabezpieczenia pakietu AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285371"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Przechowuj pakiet AppSource w usłudze Azure Storage i Wygeneruj adres URL z kluczem sygnatury dostępu współdzielonego
=============================================================================

Aby zachować bezpieczeństwo plików, wszyscy partnerzy muszą przechowywać plik pakietu AppSource na koncie usługi Azure Blob Storage i używać klucza SAS do udostępniania go. Pobierzemy plik pakietu z lokalizacji usługi Azure Storage w celu certyfikacji i użyjemy go do AppSource prób.

Aby przekazać pakiet do magazynu obiektów blob, wykonaj następujące czynności:

1. Przejdź do <https://azure.microsoft.com> i utwórz bezpłatne konto wersji próbnej lub rachunku.

2. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

3. Utwórz nowe konto magazynu, klikając pozycję **+ Nowy** i przechodząc do konta **dane + magazyn** .

   ![Blok dane i magazyn w portalu Microsoft Azure](media/CRMScreenShot7.png)

4. Wprowadź **nazwę** i nazwę **grupy zasobów** , a następnie kliknij przycisk **Utwórz** .

   ![Utwórz konto magazynu w portalu Microsoft Azure](media/CRMScreenShot8.png)

5. Przejdź do nowo utworzonej grupy zasobów i Utwórz nowy kontener obiektów BLOB.

   ![Przekazywanie pakietu jako obiektu BLOB przy użyciu portalu Microsoft Azure](media/CRMScreenShot9.png)

6. Jeśli jeszcze tego nie zrobiono, Pobierz i zainstaluj [Eksplorator usługi Azure Storage](https://storageexplorer.com/)Microsoft.

7. Otwórz Eksplorator usługi Storage i użyj ikony, aby nawiązać połączenie z kontem usługi Azure Storage.

8. Przejdź do utworzonego kontenera obiektów blob, a następnie kliknij pozycję **Przekaż** , aby dodać plik zip pakietu.

   ![Przekazywanie pakietu przy użyciu Eksplorator usługi Storage firmy Microsoft](media/CRMScreenShot10.png)

9. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**.

   ![Uzyskiwanie sygnatury dostępu współdzielonego dla pliku platformy Azure](media/CRMScreenShot11.png)

10. Zmodyfikuj **czas wygaśnięcia** , aby uaktywnić sygnaturę dostępu współdzielonego przez miesiąc, a następnie kliknij przycisk **Utwórz**.

    ![Modyfikowanie daty wygaśnięcia sygnatury dostępu współdzielonego pliku platformy Azure](media/CRMScreenShot12.png)

11. Skopiuj pole adresu URL i Zapisz je w późniejszym czasie. Musisz wprowadzić ten adres URL podczas tworzenia skojarzonej oferty. 

    ![Skopiuj adres URL sygnatury dostępu współdzielonego pliku platformy Azure](media/CRMScreenShot13.png)

