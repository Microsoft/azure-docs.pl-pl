---
title: Azure AD Connect nową konfigurację agenta aprowizacji w chmurze
description: W tym artykule opisano sposób instalowania aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628903"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Utwórz nową konfigurację dla Azure AD Connect aprowizacji opartej na chmurze

Po zainstalowaniu agenta należy zalogować się do Azure Portal i skonfigurować usługę Azure Active Directory (Azure AD) łączenie się z obsługą chmury. Wykonaj następujące kroki, aby włączyć agenta.

## <a name="configure-provisioning"></a>Konfigurowanie aprowizacji
Aby skonfigurować Inicjowanie obsługi, wykonaj następujące kroki.

 1. W Azure Portal wybierz pozycję **Azure Active Directory**
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj obsługą**.

 ![Zarządzanie obsługą administracyjną](media/how-to-configure/manage1.png)
 
 4. Wybierz pozycję **Nowa konfiguracja**.
 5. Na ekranie Konfiguracja wybierz domenę i zdecyduj, czy ma zostać włączona synchronizacja skrótów haseł.  Kliknij przycisk **Utwórz**.  
 
 ![Utwórz nową konfigurację](media/how-to-configure/configure1.png)


 6.  Zostanie otwarty ekran edytuj konfigurację aprowizacji.

   ![Edytuj konfigurację](media/how-to-configure/configure2.png)

 7. Wprowadź **wiadomość e-mail z powiadomieniem**. Ta wiadomość e-mail zostanie powiadomiona, gdy obsługa administracyjna nie jest w dobrej kondycji.
 8. Przenieś selektor, aby go włączyć, a następnie wybierz pozycję Zapisz.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Inicjowanie obsługi zakresu dla określonych użytkowników i grup
Można ograniczyć agenta do synchronizowania określonych użytkowników i grup za pomocą lokalnych grup Active Directory lub jednostek organizacyjnych. W ramach konfiguracji nie można konfigurować grup i jednostek organizacyjnych. 

 1.  W Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.
 4. W obszarze **Konfiguracja**wybierz konfigurację.

 ![Sekcja konfiguracji](media/how-to-configure/scope1.png)
 
 5. W obszarze **Konfiguracja**wybierz opcję **Edytuj filtry zakresu** , aby zmienić zakres reguły konfiguracji.
 ![Edytowanie zakresu](media/how-to-configure/scope3.png)
 7. Po prawej stronie można zmienić zakres.  Po zakończeniu kliknij pozycję **gotowe**  i **Zapisz** .
 8. Po zmianie zakresu należy [ponownie uruchomić Inicjowanie obsługi](#restart-provisioning) , aby zainicjować natychmiastową synchronizację zmian.

## <a name="attribute-mapping"></a>Mapowanie atrybutów
Azure AD Connect aprowizacji w chmurze umożliwia łatwe Mapowanie atrybutów między obiektami lokalnymi użytkownika/grupy i obiektami w usłudze Azure AD.  Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.  Aby uzyskać więcej informacji, zobacz [Mapowanie atrybutów](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Aprowizacja na żądanie
Azure AD Connect aprowizacji w chmurze umożliwia testowanie zmian konfiguracji przez zastosowanie tych zmian do pojedynczego użytkownika lub grupy.  Można go użyć do sprawdzenia poprawności i sprawdzenia, czy zmiany wprowadzone do konfiguracji zostały zastosowane prawidłowo i są prawidłowo zsynchronizowane z usługą Azure AD.  Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi na żądanie](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Ponowne uruchamianie aprowizacji 
Jeśli nie chcesz czekać na następne zaplanowane uruchomienie, wyzwól uruchomienie aprowizacji za pomocą przycisku **ponowne Inicjowanie obsługi administracyjnej** . 
 1.  W Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.
 4. W obszarze **Konfiguracja**wybierz konfigurację.

   ![Wybór konfiguracji w celu ponownego uruchomienia aprowizacji](media/how-to-configure/scope1.png)

 5. W górnej części wybierz pozycję **Uruchom ponownie Inicjowanie obsługi**.

## <a name="remove-a-configuration"></a>Usuń konfigurację
Aby usunąć konfigurację, wykonaj następujące kroki.

 1.  W Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.
 4. W obszarze **Konfiguracja**wybierz konfigurację.
   
   ![Wybór konfiguracji w celu usunięcia konfiguracji](media/how-to-configure/scope1.png)

 5. W górnej części ekranu konfiguracja wybierz pozycję **Usuń**.

>[!IMPORTANT]
>Nie ma potwierdzenia przed usunięciem konfiguracji. Upewnij się, że jest to akcja, którą chcesz wykonać przed wybraniem opcji **Usuń**.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
