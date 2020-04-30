---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67593990"
---
## <a name="create-a-new-cdn-profile"></a>Tworzenie nowego profilu CDN

Profil usługi CDN jest kontenerem dla punktów końcowych usługi CDN i określa warstwę cenową.

1. W witrynie Azure Portal w lewym górnym rogu wybierz pozycję **Utwórz zasób**. 
    
    Zostanie wyświetlone okienko **Nowy**.
   
2. Wybierz pozycję **Sieć Web + aplikacje mobilne**, a następnie wybierz pozycję **CDN**.
   
    ![Wybierz zasób usługi CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Zostanie wyświetlone okienko **profil usługi CDN** .

3. Dla ustawień profilu usługi CDN należy użyć wartości podanych w poniższej tabeli:
   
    | Ustawienie  | Wartość |
    | -------- | ----- |
    | **Nazwa** | Jako nazwę profilu wprowadź *my-cdn-profile-123*. Ta nazwa musi być globalnie unikatowa; jeśli jest już używana, można wprowadzić inną nazwę. |
    | **Subskrypcja** | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nowy**, a następnie jako nazwę grupy zasobów wprowadź *my-resource-group-123*. Jeśli jest już używana, możesz wprowadzić inną nazwę lub wybrać **Użyj istniejącej** i wybrać **my-resource-group-123** z listy rozwijanej. | 
    | **Lokalizacja grupy zasobów** | Z listy rozwijanej wybierz pozycję **Środkowe stany USA**. |
    | **Warstwa cenowa** | Z listy rozwijanej wybierz pozycję **Verizon — Standardowa**. |
    | **Utwórz teraz nowy punkt końcowy CDN** | Pozostaw niezaznaczone. |  
   
    ![Nowy profil usługi CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, aby zapisać profil na pulpicie nawigacyjnym po jego utworzeniu.
    
5. Wybierz pozycję **Utwórz**, aby utworzyć profil. 

    Tylko w przypadku profilów usługi **Azure CDN Standard firmy Microsoft** przygotowanie profilu trwa zwykle do dwóch godzin. 

