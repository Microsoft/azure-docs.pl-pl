---
title: Operacje Azure Active Directory Connect Health
description: W tym artykule opisano dodatkowe operacje, które można wykonać po wdrożeniu Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e41a00c1489d5b7b4822fec8bb4da35e917826
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686465"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operacje Azure Active Directory Connect Health
W tym temacie opisano różne operacje, które można wykonać za pomocą programu Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Włącz powiadomienia e-mail
Usługę Azure AD Connect Health można skonfigurować do wysyłania powiadomień e-mail, gdy alerty wskazują, że infrastruktura tożsamości nie jest w dobrej kondycji. Dzieje się tak po wygenerowaniu alertu i po jego rozwiązaniu.

![Zrzut ekranu przedstawiający ustawienia powiadomień e-mail Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Powiadomienia e-mail są domyślnie włączone.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aby włączyć Azure AD Connect Health powiadomienia e-mail
1. Otwórz blok **alerty** dla usługi, dla której chcesz otrzymywać powiadomienia pocztą e-mail.
2. Na pasku akcji kliknij pozycję **Ustawienia powiadomień**.
3. W obszarze powiadomień e-mail wybierz pozycję **włączone**.
4. Zaznacz to pole wyboru, jeśli chcesz, aby wszyscy administratorzy globalni otrzymywali powiadomienia e-mail.
5. Jeśli chcesz otrzymywać powiadomienia e-mail przy użyciu innych adresów e-mail, określ je w polu **dodatkowi adresaci wiadomości e-mail** . Aby usunąć adres e-mail z tej listy, kliknij prawym przyciskiem myszy wpis i wybierz polecenie **Usuń**.
6. Aby sfinalizować zmiany, kliknij przycisk **Zapisz**. Zmiany zaczną obowiązywać dopiero po zapisaniu.

>[!NOTE] 
> Gdy wystąpią problemy z przetwarzaniem żądań synchronizacji w naszej usłudze wewnętrznej bazy danych, ta usługa wysyła wiadomość e-mail z powiadomieniem ze szczegółowymi informacjami o błędzie do adresów e-mail (y) osoby kontaktowej dzierżawy. Otrzymaliśmy opinie od klientów, że w niektórych przypadkach ilość tych komunikatów jest niezwykle duża, więc zmieniamy sposób wysyłania tych komunikatów. 
>
> Zamiast wysyłać komunikat dla każdego błędu synchronizacji za każdym razem, gdy wystąpi, wyślemy codzienne podsumowanie wszystkich błędów zwróconych przez usługę zaplecza. Dzięki temu klienci mogą przetwarzać te błędy w bardziej wydajny sposób i zmniejszać liczbę zduplikowanych komunikatów o błędach.

## <a name="delete-a-server-or-service-instance"></a>Usuwanie serwera lub wystąpienia usługi

>[!NOTE] 
> Dla kroków usuwania wymagana jest licencja usługi Azure AD Premium.

W niektórych przypadkach może być konieczne usunięcie serwera z monitorowania. Oto, co musisz wiedzieć, aby usunąć serwer z usługi Azure AD Connect Health.

Podczas usuwania serwera należy pamiętać o następujących kwestiach:

* Ta akcja powoduje zatrzymanie zbierania wszelkich dalszych danych z tego serwera. Ten serwer jest usuwany z usługi monitorowania. Po wykonaniu tej akcji nie można wyświetlać nowych alertów, monitorowania ani danych analizy użycia dla tego serwera.
* Ta akcja nie powoduje odinstalowania agenta kondycji z serwera. Jeśli Agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą pojawić się błędy związane z agentem kondycji na serwerze.
* Ta akcja nie powoduje usunięcia danych już zebranych z tego serwera. Te dane są usuwane zgodnie z zasadami przechowywania danych platformy Azure.
* Jeśli po wykonaniu tej akcji chcesz ponownie rozpocząć monitorowanie tego samego serwera, należy odinstalować i ponownie zainstalować agenta kondycji na tym serwerze.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Usuwanie serwera z usługi Azure AD Connect Health

>[!NOTE] 
> Dla kroków usuwania wymagana jest licencja usługi Azure AD Premium.

Azure AD Connect Health dla Active Directory Federation Services (AD FS) i Azure AD Connect (synchronizacja):

1. Otwórz blok **serwer** z bloku **Lista serwerów** , wybierając nazwę serwera, który ma zostać usunięty.
2. W bloku **serwer** na pasku akcji kliknij pozycję **Usuń**.
![Zrzut ekranu przedstawiający Azure AD Connect Health usuwania serwera](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potwierdź, wpisując nazwę serwera w polu potwierdzenia.
4. Kliknij polecenie **Usuń**.

Azure AD Connect Health Azure Active Directory Domain Services:

1. Otwórz pulpit nawigacyjny **kontrolery domeny** .
2. Wybierz kontroler domeny, który ma zostać usunięty.
3. Na pasku akcji kliknij pozycję **Usuń zaznaczone**.
4. Potwierdź akcję, aby usunąć serwer.
5. Kliknij polecenie **Usuń**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Usuwanie wystąpienia usługi z usługi Azure AD Connect Health
W niektórych przypadkach może być konieczne usunięcie wystąpienia usługi. Oto, co musisz wiedzieć, aby usunąć wystąpienie usługi z usługi Azure AD Connect Health.

Podczas usuwania wystąpienia usługi należy pamiętać o następujących kwestiach:

* Ta akcja spowoduje usunięcie bieżącego wystąpienia usługi z usługi monitorowania.
* Ta akcja nie powoduje odinstalowania ani usunięcia agenta kondycji z żadnego z serwerów monitorowanych w ramach tego wystąpienia usługi. Jeśli Agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą pojawić się błędy związane z agentem kondycji na serwerach.
* Wszystkie dane z tego wystąpienia usługi zostaną usunięte zgodnie z zasadami przechowywania danych platformy Azure.
* Jeśli po wykonaniu tej akcji chcesz rozpocząć monitorowanie usługi, Odinstaluj i ponownie zainstaluj agenta kondycji na wszystkich serwerach. Jeśli po wykonaniu tej akcji chcesz ponownie rozpocząć monitorowanie tego samego serwera, Odinstaluj, ponownie zainstaluj i zarejestruj agenta kondycji na tym serwerze.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Aby usunąć wystąpienie usługi z usługi Azure AD Connect Health
1. Otwórz blok **usługi** z bloku **Lista usług** , wybierając identyfikator usługi (nazwę farmy), którą chcesz usunąć. 
2. W bloku **Usługa** na pasku akcji kliknij pozycję **Usuń**. 
![Zrzut ekranu przedstawiający Azure AD Connect Health usuwania usługi](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potwierdź, wpisując nazwę usługi w polu potwierdzenia (na przykład: sts.contoso.com).
4. Kliknij polecenie **Usuń**.
   <br><br>

[//]: # (Sekcja początkowa kontroli RBAC)
## <a name="manage-access-with-azure-rbac"></a>Zarządzanie dostępem przy użyciu usługi Azure RBAC
[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) dla Azure AD Connect Health zapewnia dostęp do użytkowników i grup innych niż administratorzy globalni. Usługa Azure RBAC przypisuje role do zamierzonych użytkowników i grup i udostępnia mechanizm ograniczania administratorów globalnych w katalogu.

### <a name="roles"></a>Role
Azure AD Connect Health obsługuje następujące wbudowane role:

| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Właściciele mogą *zarządzać dostępem* (na przykład przypisywać rolę do użytkownika lub grupy), *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu i *zmieniać ustawienia* (na przykład powiadomienia e-mail) w Azure AD Connect Health. <br>Domyślnie Administratorzy globalni usługi Azure AD są przypisani do tej roli i nie można jej zmienić. |
| Współautor |Współautorzy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu i *zmieniać ustawienia* (na przykład powiadomienia e-mail) w Azure AD Connect Health. |
| Czytelnik |Czytelnicy mogą *wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu w Azure AD Connect Health. |

Wszystkie inne role (takie jak Administratorzy dostępu użytkowników lub Użytkownicy DevTest Labs) nie mają wpływu na dostęp w ramach Azure AD Connect Health, nawet jeśli role są dostępne w portalu.

### <a name="access-scope"></a>Zakres dostępu
Azure AD Connect Health obsługuje zarządzanie dostępem na dwóch poziomach:

* **Wszystkie wystąpienia usługi**: jest to zalecana ścieżka w większości przypadków. Kontroluje dostęp do wszystkich wystąpień usługi (na przykład farmy AD FS) we wszystkich typach ról monitorowanych przez Azure AD Connect Health.
* **Wystąpienie usługi**: w niektórych przypadkach może być konieczne oddzielenie dostępu na podstawie typów ról lub wystąpienia usługi. W takim przypadku można zarządzać dostępem na poziomie wystąpienia usługi.  

Przyznano uprawnienia, jeśli użytkownik końcowy ma dostęp na poziomie katalogu lub wystąpienia usługi.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zezwalaj użytkownikom lub grupom na dostęp do Azure AD Connect Health
Poniższe kroki pokazują, jak zezwolić na dostęp.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1. Wybieranie odpowiedniego zakresu dostępu
Aby zezwolić użytkownikowi na dostęp na poziomie *wszystkich wystąpień usługi* w Azure AD Connect Health, Otwórz główny blok w Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2. Dodawanie użytkowników i grup oraz przypisywanie ról
1. W sekcji **Konfiguracja** kliknij pozycję **Użytkownicy**.<br>
   ![Zrzut ekranu paska bocznego zasobów Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Wybierz pozycję **Dodaj**.
3. W okienku **Wybierz rolę** wybierz rolę (na przykład **właściciel**).<br>
   ![Zrzut ekranu przedstawiający Azure AD Connect Health i menu konfiguracji kontroli RBAC platformy Azure](./media/how-to-connect-health-operations/RBAC_add.png)
4. Wpisz nazwę lub identyfikator dostosowanego użytkownika lub grupy. Można wybrać co najmniej jednego użytkownika lub grupę w tym samym czasie. Kliknij pozycję **Wybierz**.
   ![Zrzut ekranu przedstawiający Azure AD Connect Health i listę ról platformy Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Wybierz przycisk **OK**.<br>
6. Po zakończeniu przypisywania roli Użytkownicy i grupy zostaną wyświetlone na liście.<br>
   ![Zrzut ekranu przedstawiający Azure AD Connect Health i RBAC platformy Azure oraz nowych użytkowników](./media/how-to-connect-health-operations/RBAC_user_list.png)

Teraz na liście Użytkownicy i grupy mają dostęp, zgodnie z przypisanymi rolami.

> [!NOTE]
> * Administratorzy globalni zawsze mają pełny dostęp do wszystkich operacji, ale konta administratorów globalnych nie znajdują się na poprzedniej liście.
> * Funkcja zapraszania użytkowników nie jest obsługiwana w ramach Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3. Udostępnianie lokalizacji bloku użytkownikom lub grupom
1. Po przypisaniu uprawnień użytkownik może uzyskać dostęp do Azure AD Connect Health, przechodząc do tego [miejsca](https://aka.ms/aadconnecthealth).
2. W bloku użytkownik może przypiąć blok lub jego różne części do pulpitu nawigacyjnego. Po prostu kliknij ikonę **Przypnij do pulpitu nawigacyjnego** .<br>
   ![Zrzut ekranu przedstawiający blok numeru PIN Azure AD Connect Health i Azure RBAC z wyróżnioną ikoną pinezki](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Użytkownik z przypisaną rolą czytelnik nie może uzyskać Azure AD Connect Health rozszerzenia z portalu Azure Marketplace. Użytkownik nie może wykonać operacji "Create", aby to zrobić. Użytkownik nadal może przejść do bloku, przechodząc do poprzedniego linku. Aby kolejne użycie, użytkownik może przypiąć blok do pulpitu nawigacyjnego.
>
>

### <a name="remove-users-or-groups"></a>Usuwanie użytkowników lub grup
Można usunąć użytkownika lub grupę dodaną do Azure AD Connect Health i kontroli RBAC platformy Azure. Po prostu kliknij prawym przyciskiem myszy użytkownika lub grupę, a następnie wybierz pozycję **Usuń**.<br>
![Zrzut ekranu przedstawiający Azure AD Connect Health i RBAC platformy Azure z wyróżnioną pozycją Usuń](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Koniec sekcji RBAC)

## <a name="next-steps"></a>Następne kroki
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalacja agenta Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
