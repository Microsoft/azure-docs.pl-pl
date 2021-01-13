---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165913"
---
## <a name="create-a-new-cdn-profile"></a>Tworzenie nowego profilu CDN

Profil usługi CDN jest kontenerem dla punktów końcowych usługi CDN i określa warstwę cenową.

1. W Azure Portal wybierz pozycję **Utwórz zasób** (w lewym górnym rogu). Zostanie wyświetlone okienko **Nowy**.
   
1. Wyszukaj i wybierz pozycję **CDN**, a następnie wybierz pozycję **Utwórz**:
   
    ![Wybierz zasób usługi CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Zostanie wyświetlone okienko **profil usługi CDN** .

1. Wprowadź następujące wartości:
   
    | Ustawienie  | Wartość |
    | -------- | ----- |
    | **Nazwa** | W obszarze Nazwa profilu Wprowadź nazwę *CDN-profile-123* . Ta nazwa musi być globalnie unikatowa; Jeśli jest już używana, wprowadź inną nazwę. |
    | **Subskrypcja** | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nową** i wprowadź *CDNQuickstart-RG* dla nazwy grupy zasobów lub wybierz opcję **Użyj istniejącej** , a następnie wybierz pozycję *CDNQuickstart-RG* , jeśli istnieje już Grupa. | 
    | **Lokalizacja grupy zasobów** | Wybierz z listy rozwijanej lokalizację znajdującą się obok siebie. |
    | **Warstwa cenowa** | Wybierz **standardową opcję Akamai** z listy rozwijanej. (Czas wdrożenia dla warstwy Akamai jest około jednej minuty. Warstwa firmy Microsoft zajmie około 10 minut, a warstwy Verizon zajmują około 30 minut. |
    | **Utwórz teraz nowy punkt końcowy CDN** | Pozostaw niezaznaczone. |  
   
    ![Nowy profil usługi CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Wybierz pozycję **Utwórz**, aby utworzyć profil.

