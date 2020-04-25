---
title: Karta Ustawienia oferty maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę Ustawienia oferty używane podczas tworzenia oferty maszyny wirtualnej portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: abeadf5f3c0a19212a12256e06602e840c9d1fa9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146829"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Ustawienia oferty maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

**Nowa strona oferty** dla maszyn wirtualnych otwiera się na pierwszej karcie o nazwie **Ustawienia oferty**.  

![Nowa strona oferty dla maszyn wirtualnych](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Pola ustawień oferty

Na karcie **Ustawienia oferty** musisz podać następujące pola.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane. 

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***   | Unikatowy identyfikator (w ramach profilu wydawcy) oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach Azure Resource Manager i raportach dotyczących rozliczeń. Ma maksymalną długość 50 znaków, może składać się tylko z małych znaków alfanumerycznych i kresek (-), ale nie może kończyć się znakiem kreski. Nie można zmienić tego pola po zakończeniu oferty. <br> Na przykład jeśli firma Contoso publikuje ofertę z IDENTYFIKATORem oferty **przykładem-VM**, przypiszesz adres URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` witryny Azure Marketplace. |
| **Dawc\***  | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Wszystkie oferty powinny być skojarzone z IDENTYFIKATORem wydawcy. Nie można zmodyfikować tej wartości po zapisaniu oferty. |
| **Nazwa\***       | Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w witrynie Azure Marketplace i w portal Cloud Partner. Może zawierać maksymalnie 50 znaków. Wskazówki tutaj zawierają rozpoznawalną nazwę marki dla danego produktu. Nie dodawaj tutaj nazwy organizacji, chyba że jest to marketingowe. W przypadku wprowadzania tej oferty w innych witrynach sieci Web i publikacjach upewnij się, że nazwa jest dokładnie taka sama dla wszystkich publikacji. |
|   |   |
 
Po podaniu wszystkich pól kliknij przycisk **Zapisz** . 


## <a name="next-steps"></a>Następne kroki

Na następnej karcie dodasz [jednostki SKU](./cpp-skus-tab.md) do oferty.
