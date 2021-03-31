---
title: Tworzenie konta usługi NetApp i uzyskiwanie dostępu do usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano, jak uzyskać dostęp do usługi Azure NetApp Files i utworzyć konto usługi NetApp, co umożliwia skonfigurowanie puli pojemności i utworzenie woluminu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006597"
---
# <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp
Utworzenie konta usługi NetApp umożliwia skonfigurowanie puli pojemności, w której następnie można utworzyć wolumin. Nowe konto usługi NetApp możesz utworzyć w bloku usługi Azure NetApp Files.

## <a name="before-you-begin"></a>Zanim rozpoczniesz
Użytkownik musi otrzymać wiadomość e-mail od zespołu Azure NetApp Files, upewniając się, że udzielono dostępu do usługi. [Aby uzyskać dostęp do usługi, zobacz Przesyłanie żądania waitlist](azure-netapp-files-register.md#waitlist).

Ponadto należy zarejestrować swoją subskrypcję do korzystania z dostawcy zasobów NetApp. Zobacz [Rejestrowanie dostawcy zasobów NetApp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Kroki 

1. Zaloguj się w witrynie Azure Portal. 
2. Przejdź do bloku usługi Azure NetApp Files za pomocą jednej z następujących metod:  
   * Wyszukaj usługę **Azure NetApp Files** w polu wyszukiwania witryny Azure Portal.  
   * Kliknij przycisk **Wszystkie usługi** w obszarze nawigacji, a następnie wprowadź nazwę usługi Azure NetApp Files w filtrze.  

   Możesz dodać blok usługi Azure NetApp Files do ulubionych, klikając ikonę gwiazdki obok tej usługi. 

3. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.  
   Zostanie wyświetlone okno Nowe konto usługi NetApp.  

4. Podaj następujące informacje dotyczące konta usługi NetApp: 
   * **Nazwa konta**  
     Określ unikatową nazwę subskrypcji.
   * **Subskrypcja**  
     Wybierz subskrypcję z listy istniejących subskrypcji.
   * **Grupa zasobów**   
     Użyj istniejącej grupy zasobów lub utwórz nową.
   * **Lokalizacja**  
     Wybierz region, w którym będzie znajdować się konto i jego zasoby podrzędne.  

     ![Nowe konto usługi NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kliknij pozycję **Utwórz**.     
   Utworzone konto usługi NetApp znajduje się teraz w bloku usługi Azure NetApp Files. 

> [!NOTE] 
> Jeśli nie udzielono dostępu do usługi Azure NetApp Files, podczas próby utworzenia pierwszego konta NetApp zostanie wyświetlony następujący komunikat o błędzie:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Następne kroki  

[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)

