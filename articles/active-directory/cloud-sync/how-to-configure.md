---
title: Azure AD Connect nową konfigurację agenta usługi Cloud Sync
description: W tym artykule opisano sposób instalowania usługi Cloud Sync.
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
ms.openlocfilehash: 0eacf3cad0a610ae75f38dbb6f1bd7bdab816d54
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613786"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Utwórz nową konfigurację dla Azure AD Connect synchronizacji w chmurze

Po zainstalowaniu agenta aprowizacji Azure AD Connect należy zalogować się do Azure Portal i skonfigurować go. Wykonaj następujące kroki, aby włączyć agenta.

## <a name="configure-provisioning"></a>Konfigurowanie aprowizacji
Aby skonfigurować Inicjowanie obsługi, wykonaj następujące kroki.

 1. W Azure Portal wybierz pozycję **Azure Active Directory**
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.

 ![Zarządzanie obsługą administracyjną](media/how-to-install/install-6.png)
 
 4. Wybierz pozycję **Nowa konfiguracja**.
 5. Na ekranie Konfiguracja wybierz domenę i zdecyduj, czy ma zostać włączona synchronizacja skrótów haseł.  Kliknij przycisk **Utwórz**.  
 
 ![Utwórz nową konfigurację](media/how-to-configure/configure-1.png)


 6.  Zostanie otwarty ekran edytuj konfigurację aprowizacji.

   ![Edytuj konfigurację](media/how-to-configure/con-1.png)

 7. Wprowadź **wiadomość e-mail z powiadomieniem**. Ta wiadomość e-mail zostanie powiadomiona, gdy obsługa administracyjna nie jest w dobrej kondycji.  Zaleca się pozostawienie **zapobiegania przypadkowemu usuwaniu** i ustawienie **progu przypadkowego usunięcia** na liczbę, która ma zostać powiadomiona.  Aby uzyskać więcej informacji, zobacz sekcję [przypadkowe usuwanie](#accidental-deletions) poniżej.
 8. Przenieś selektor, aby go włączyć, a następnie wybierz pozycję Zapisz.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Inicjowanie obsługi zakresu dla określonych użytkowników i grup
Można ograniczyć agenta do synchronizowania określonych użytkowników i grup za pomocą lokalnych grup Active Directory lub jednostek organizacyjnych. W ramach konfiguracji nie można konfigurować grup i jednostek organizacyjnych. 

 1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.
 4. W obszarze **Konfiguracja** wybierz konfigurację.

 ![Sekcja konfiguracji](media/how-to-configure/scope-1.png)
 
 5. W obszarze **Konfiguracja** wybierz opcję **Edytuj filtry zakresu** , aby zmienić zakres reguły konfiguracji.
 ![Edytowanie zakresu](media/how-to-configure/scope-3.png)
 7. Po prawej stronie można zmienić zakres.  Po zakończeniu kliknij pozycję **gotowe**  i **Zapisz** .
 8. Po zmianie zakresu należy [ponownie uruchomić Inicjowanie obsługi](#restart-provisioning) , aby zainicjować natychmiastową synchronizację zmian.

## <a name="attribute-mapping"></a>Mapowanie atrybutów
Azure AD Connect synchronizacja w chmurze umożliwia łatwe Mapowanie atrybutów między obiektami lokalnymi użytkownika/grupy i obiektami w usłudze Azure AD.  Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.  Aby uzyskać więcej informacji, zobacz [Mapowanie atrybutów](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Aprowizacja na żądanie
Azure AD Connect synchronizacja w chmurze umożliwia testowanie zmian konfiguracji przez zastosowanie tych zmian do pojedynczego użytkownika lub grupy.  Można go użyć do sprawdzenia poprawności i sprawdzenia, czy zmiany wprowadzone do konfiguracji zostały zastosowane prawidłowo i są prawidłowo zsynchronizowane z usługą Azure AD.  Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi na żądanie](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Przypadkowe usunięcia
Funkcja przypadkowego usuwania jest przeznaczona do ochrony przed przypadkowymi zmianami konfiguracji i zmianami w katalogu lokalnym, które wpłyną na wielu użytkowników i grupy.  Ta funkcja umożliwia:

- Skonfiguruj możliwość automatycznego usuwania przypadkowych usunięć. 
- Ustaw liczbę obiektów (próg), po których ta konfiguracja zacznie obowiązywać 
- Skonfiguruj adres e-mail powiadomienia, aby móc otrzymywać powiadomienia e-mail, gdy w danym zadaniu synchronizacji zostanie umieszczona Kwarantanna w tym scenariuszu 

Aby uzyskać więcej informacji, zobacz [przypadkowe usuwanie](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Poddaje
Usługa Cloud Sync monitoruje kondycję konfiguracji i umieszcza w stanie kwarantanny obiekty w złej kondycji. Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie synchronizacji zostanie oznaczone jako objęte kwarantanną.  Aby uzyskać więcej informacji, zobacz sekcję Rozwiązywanie problemów w obszarze [kwarantanny](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Ponowne uruchamianie aprowizacji 
Jeśli nie chcesz czekać na następne zaplanowane uruchomienie, wyzwól uruchomienie aprowizacji za pomocą przycisku **ponowne Inicjowanie obsługi administracyjnej** . 
 1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.
 4. W obszarze **Konfiguracja** wybierz konfigurację.

   ![Wybór konfiguracji w celu ponownego uruchomienia aprowizacji](media/how-to-configure/scope-1.png)

 5. W górnej części wybierz pozycję **Uruchom ponownie Inicjowanie obsługi**.

## <a name="remove-a-configuration"></a>Usuń konfigurację
Aby usunąć konfigurację, wykonaj następujące kroki.

 1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
 2. Wybierz **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.
 4. W obszarze **Konfiguracja** wybierz konfigurację.
   
   ![Wybór konfiguracji w celu usunięcia konfiguracji](media/how-to-configure/scope-1.png)

 5. W górnej części ekranu konfiguracja wybierz pozycję **Usuń**.

>[!IMPORTANT]
>Nie ma potwierdzenia przed usunięciem konfiguracji. Upewnij się, że jest to akcja, którą chcesz wykonać przed wybraniem opcji **Usuń**.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
