---
title: Azure Active Directory Connect Health operacji
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
ms.openlocfilehash: 836c7bf9aefd4b2cb7d52c66bbd37e7ba38a467c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377314"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health operacji
W tym temacie opisano różne operacje, które można wykonać przy użyciu programu Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Włączanie powiadomień e-mail
Możesz skonfigurować usługę Azure AD Connect Health do wysyłania powiadomień e-mail, gdy alerty wskazują, że infrastruktura tożsamości nie jest w dobrej kondycji. Dzieje się tak po wygenerowaniu alertu i jego rozwiązania.

![Zrzut ekranu przedstawiający Azure AD Connect Health powiadomień e-mail](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Powiadomienia e-mail są domyślnie włączone.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aby włączyć Azure AD Connect Health e-mail
1. W witrynie Azure Portal wyszukaj Azure AD Connect Health
2. Wybierz pozycję **Błędy synchronizacji**
3. Wybierz **pozycję Ustawienia powiadomień.**
5. Na przełączniku powiadomień e-mail wybierz pozycję **WŁ.**
6. Zaznacz pole wyboru, jeśli chcesz, aby wszyscy administratorzy globalni otrzymywać powiadomienia e-mail.
7. Jeśli chcesz otrzymywać powiadomienia e-mail na inne adresy e-mail, określ je w polu **Dodatkowi adresaci poczty e-mail.** Aby usunąć adres e-mail z tej listy, kliknij prawym przyciskiem myszy wpis i wybierz polecenie **Usuń.**
8. Aby sfinalizować zmiany, kliknij przycisk **Zapisz.** Zmiany zostaną wprowadzone dopiero po zapisaniu.

>[!NOTE] 
> W przypadku problemów z przetwarzaniem żądań synchronizacji w naszej usłudze zaplecza ta usługa wysyła wiadomość e-mail z powiadomieniem ze szczegółami błędu na adresy e-mail kontaktu administracyjnego dzierżawy. Wysłuchaliśmy opinii klientów, że w niektórych przypadkach liczba tych komunikatów jest zbyt duża, więc zmieniamy sposób ich wysyłania. 
>
> Zamiast wysyłać komunikat dla każdego błędu synchronizacji za każdym razem, gdy wystąpi, wyślemy codzienne podsumowanie wszystkich błędów zwróconych przez usługę zaplecza. Dzięki temu klienci mogą przetwarzać te błędy w bardziej wydajny sposób i zmniejszyć liczbę zduplikowanych komunikatów o błędach.

## <a name="delete-a-server-or-service-instance"></a>Usuwanie serwera lub wystąpienia usługi

>[!NOTE] 
> Do kroków usuwania jest wymagana licencja usługi Azure AD Premium.

W niektórych przypadkach możesz chcieć usunąć serwer z monitorowania. Oto co należy wiedzieć, aby usunąć serwer z usługi Azure AD Connect Health service.

Podczas usuwania serwera należy pamiętać o następujących kwestie:

* Ta akcja zatrzymuje zbieranie dalszych danych z tego serwera. Ten serwer jest usuwany z usługi monitorowania. Po tej akcji nie będzie można wyświetlać nowych alertów, monitorowania ani danych analizy użycia dla tego serwera.
* Ta akcja nie powoduje odinstalowania agenta kondycji z serwera. Jeśli agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą pojawić się błędy związane z agentem kondycji na serwerze.
* Ta akcja nie powoduje usunięcia już zebranych danych z tego serwera. Te dane są usuwane zgodnie z zasadami przechowywania danych platformy Azure.
* Jeśli po wykonaniu tej akcji chcesz ponownie rozpocząć monitorowanie tego samego serwera, musisz odinstalować i ponownie zainstalować agenta kondycji na tym serwerze.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Usuwanie serwera z Azure AD Connect Health usługi

>[!NOTE] 
> Do kroków usuwania jest wymagana licencja usługi Azure AD w wersji Premium.

Azure AD Connect Health dla Active Directory Federation Services (AD FS) i Azure AD Connect (synchronizacja):

1. Otwórz blok **Serwer** z bloku **Lista serwerów,** wybierając nazwę serwera do usunięcia.
2. W bloku **Serwer** na pasku akcji kliknij pozycję **Usuń**.
![Zrzut ekranu Azure AD Connect Health usuwania serwera](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potwierdź, wpisując nazwę serwera w oknie potwierdzenia.
4. Kliknij polecenie **Usuń**.

Azure AD Connect Health dla Azure Active Directory Domain Services:

1. Otwórz pulpit **nawigacyjny Kontrolery** domeny.
2. Wybierz kontroler domeny do usunięcia.
3. Na pasku akcji kliknij pozycję **Usuń wybrane.**
4. Potwierdź akcję usunięcia serwera.
5. Kliknij polecenie **Usuń**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Usuwanie wystąpienia usługi z Azure AD Connect Health usługi
W niektórych przypadkach można usunąć wystąpienie usługi. Oto co należy wiedzieć, aby usunąć wystąpienie usługi z usługi Azure AD Connect Health service.

Podczas usuwania wystąpienia usługi należy pamiętać o następujących chmurze:

* Ta akcja powoduje usunięcie bieżącego wystąpienia usługi z usługi monitorowania.
* Ta akcja nie powoduje odinstalowania ani usunięcia agenta kondycji z żadnego z serwerów monitorowanych w ramach tego wystąpienia usługi. Jeśli agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą wystąpić błędy związane z agentem kondycji na serwerach.
* Wszystkie dane z tego wystąpienia usługi są usuwane zgodnie z zasadami przechowywania danych platformy Azure.
* Po wykonaniu tej akcji, jeśli chcesz rozpocząć monitorowanie usługi, odinstaluj i ponownie zainstaluj agenta kondycji na wszystkich serwerach. Jeśli po wykonaniu tej akcji chcesz ponownie rozpocząć monitorowanie tego samego serwera, odinstaluj, zainstaluj ponownie i zarejestruj agenta kondycji na tym serwerze.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Aby usunąć wystąpienie usługi z Azure AD Connect Health usługi
1. Otwórz blok **Usługa** w bloku **Lista usług,** wybierając identyfikator usługi (nazwę farmy), który chcesz usunąć. 
2. W bloku **Usługa** na pasku akcji kliknij pozycję **Usuń.** 
![Zrzut ekranu Azure AD Connect Health usuwania usługi](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potwierdź, wpisując nazwę usługi w polu potwierdzenia (na przykład: sts.contoso.com).
4. Kliknij polecenie **Usuń**.
   <br><br>

[//]: # (Sekcja uruchamiania kontroli RBAC)
## <a name="manage-access-with-azure-rbac"></a>Zarządzanie dostępem przy użyciu kontroli dostępu na kontach platformy Azure
[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) platformy Azure dla usługi Azure AD Connect Health zapewnia dostęp do użytkowników i grup innych niż administratorzy globalni. Rola RBAC platformy Azure przypisuje role do użytkowników i grup, a także udostępnia mechanizm ograniczania administratorów globalnych w katalogu.

### <a name="roles"></a>Role
Azure AD Connect Health obsługuje następujące wbudowane role:

| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Właściciele *mogą* zarządzać dostępem (na przykład przypisywać rolę do użytkownika lub *grupy),* wyświetlać wszystkie informacje (na przykład wyświetlać alerty) z portalu i zmieniać ustawienia *(na* przykład powiadomienia e-mail) w Azure AD Connect Health. <br>Domyślnie administratorzy globalni usługi Azure AD mają przypisaną tę rolę i nie można jej zmienić. |
| Współautor |Współautorzy *mogą wyświetlać wszystkie informacje* (na przykład wyświetlać alerty) z portalu i zmieniać ustawienia *(na* przykład powiadomienia e-mail) w Azure AD Connect Health. |
| Czytelnik |*Czytelnicy mogą wyświetlać wszystkie* informacje (na przykład alerty) z poziomu portalu w Azure AD Connect Health. |

Wszystkie inne role (takie jak Administratorzy dostępu użytkowników lub Użytkownicy usługi DevTest Labs) nie mają wpływu na dostęp w ramach usługi Azure AD Connect Health, nawet jeśli role są dostępne w portalu.

### <a name="access-scope"></a>Zakres dostępu
Azure AD Connect Health obsługuje zarządzanie dostępem na dwóch poziomach:

* **Wszystkie wystąpienia usługi:** jest to zalecana ścieżka w większości przypadków. Kontroluje ona dostęp do wszystkich wystąpień usługi (na przykład AD FS farmy) dla wszystkich typów ról, które są monitorowane przez Azure AD Connect Health.
* **Wystąpienie usługi:** W niektórych przypadkach może być konieczne oddzielenie dostępu na podstawie typów ról lub według wystąpienia usługi. W takim przypadku można zarządzać dostępem na poziomie wystąpienia usługi.  

Uprawnienie jest udzielane, jeśli użytkownik końcowy ma dostęp na poziomie katalogu lub wystąpienia usługi.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Zezwalaj użytkownikom lub grupom na dostęp do Azure AD Connect Health
Poniższe kroki pokazują, jak zezwolić na dostęp.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1. Wybieranie odpowiedniego zakresu dostępu
Aby zezwolić użytkownikowi na dostęp *na* poziomie wszystkich wystąpień usługi w Azure AD Connect Health, otwórz główny blok w Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2. Dodawanie użytkowników i grup oraz przypisywanie ról
1. W sekcji **Konfigurowanie** kliknij pozycję **Użytkownicy.**<br>
   ![Zrzut ekranu Azure AD Connect Health paska bocznego zasobu](./media/how-to-connect-health-operations/startRBAC.png)
2. Wybierz pozycję **Dodaj**.
3. W **okienku Wybierz rolę** wybierz rolę (na przykład **Właściciel**).<br>
   ![Zrzut ekranu przedstawiający Azure AD Connect Health konfiguracji kontroli RBAC platformy Azure](./media/how-to-connect-health-operations/RBAC_add.png)
4. Wpisz nazwę lub identyfikator docelowego użytkownika lub grupy. Możesz wybrać co najmniej jednego użytkownika lub większej liczby grup w tym samym czasie. Kliknij pozycję **Wybierz**.
   ![Zrzut ekranu Azure AD Connect Health listy ról platformy Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Wybierz przycisk **OK**.<br>
6. Po zakończeniu przypisywania roli użytkownicy i grupy zostaną wyświetloni na liście.<br>
   ![Zrzut ekranu przedstawiający wyróżnione Azure AD Connect Health RBAC platformy Azure i nowych użytkowników](./media/how-to-connect-health-operations/RBAC_user_list.png)

Teraz użytkownicy i grupy na liście mają dostęp zgodnie z przypisanymi im rolami.

> [!NOTE]
> * Administratorzy globalni zawsze mają pełny dostęp do wszystkich operacji, ale konta administratorów globalnych nie znajdują się na powyższej liście.
> * Funkcja Zaproś użytkowników nie jest obsługiwana w Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3. Udostępnianie lokalizacji bloku użytkownikom lub grupom
1. Po przypisaniu uprawnień użytkownik może uzyskać dostęp do Azure AD Connect Health, przechodząc [tutaj.](https://aka.ms/aadconnecthealth)
2. W bloku użytkownik może przypiąć blok lub inne jego części do pulpitu nawigacyjnego. Wystarczy kliknąć **ikonę Przypnij do pulpitu nawigacyjnego.**<br>
   ![Zrzut ekranu przedstawiający Azure AD Connect Health przypinania RBAC platformy Azure z wyróżniona ikoną pinezki](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Użytkownik z przypisaną rolą Czytelnik nie może pobrać rozszerzenia Azure AD Connect Health z Azure Marketplace. Użytkownik nie może wykonać niezbędnej operacji "create", aby to zrobić. Użytkownik może nadal przejść do bloku, przechodząc do poprzedniego linku. W przypadku kolejnego użycia użytkownik może przypiąć blok do pulpitu nawigacyjnego.
>
>

### <a name="remove-users-or-groups"></a>Usuwanie użytkowników lub grup
Możesz usunąć użytkownika lub grupę dodaną do usługi Azure AD Connect Health RBAC platformy Azure. Po prostu kliknij prawym przyciskiem myszy użytkownika lub grupę, a następnie wybierz polecenie **Usuń**.<br>
![Zrzut ekranu przedstawiający Azure AD Connect Health kontroli RBAC na platformie Azure z wyróżnionem kreśleniami Usuń](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Koniec sekcji kontroli RBAC)

## <a name="next-steps"></a>Następne kroki
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health instalacji agenta](how-to-connect-health-agent-install.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
