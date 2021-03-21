---
title: Selektywna synchronizacja skrótów haseł dla Azure AD Connect
description: W tym artykule opisano sposób konfigurowania i konfigurowania selektywnej synchronizacji skrótów haseł, która ma być używana z Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 774c78cbb09d2e5e60dfc0cafc0082b25e9b1b45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103603105"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Konfiguracja selektywnej synchronizacji skrótów haseł dla Azure AD Connect

[Synchronizacja skrótów haseł](whatis-phs.md) jest jedną z metod logowania używanych do realizacji tożsamości hybrydowej. Azure AD Connect synchronizuje skrót skrótu hasła użytkownika z wystąpienia lokalnego Active Directory do wystąpienia usługi Azure AD opartego na chmurze.  Domyślnie po zakończeniu instalacji synchronizacja skrótów haseł zostanie przeprowadzona na wszystkich synchronizowanych użytkowników.

Jeśli chcesz mieć podzbiór użytkowników, którzy zostali wykluczeni z synchronizowania skrótu haseł do usługi Azure AD, możesz skonfigurować selektywną synchronizację skrótów haseł, korzystając z kroków opisanych w tym artykule.

>[!Important]
> Firma Microsoft nie obsługuje modyfikowania ani działania Azure AD Connect synchronizacji poza konfiguracją lub akcjami, które są formalnie udokumentowane. Wszystkie te konfiguracje i akcje mogą spowodować niespójny lub nieobsługiwany stan Azure AD Connect synchronizacji. W związku z tym firma Microsoft nie gwarantuje, że będziemy mogli zapewnić wydajną pomoc techniczną dotyczącą takich wdrożeń. 


## <a name="consider-your-implementation"></a>Weź pod uwagę swoją implementację  
Aby zmniejszyć nakład pracy w konfiguracji, należy najpierw wziąć pod uwagę liczbę obiektów użytkowników, które mają zostać wykluczone z synchronizacji skrótów haseł. Sprawdź, które z poniższych scenariuszy, które wzajemnie się wykluczają, są zgodne z wymaganiami w celu wybrania odpowiedniej opcji konfiguracji.
- Jeśli liczba użytkowników do **wykluczenia** jest **mniejsza** niż liczba użytkowników do **dołączenia**, wykonaj kroki opisane w tej [sekcji](#excluded-users-is-smaller-than-included-users).
- Jeśli liczba użytkowników do **wykluczenia** jest **większa** niż liczba użytkowników do **uwzględnienia**, wykonaj kroki opisane w tej [sekcji](#excluded-users-is-larger-than-included-users).

> [!Important]
> W przypadku wybrania opcji konfiguracji wymagana synchronizacja początkowa (Pełna synchronizacja) w celu zastosowania zmian zostanie przeprowadzona automatycznie w następnym cyklu synchronizacji.

### <a name="the-admindescription-attribute"></a>Atrybut adminDescription
Oba scenariusze polegają na ustawieniu atrybutu adminDescription użytkowników dla określonej wartości.  Pozwala to na stosowanie reguł i to, co sprawia, że selektywne PHS pracy.

|Scenariusz|adminDescription wartość|
|-----|-----|
|Wykluczeni użytkownicy są mniej niż włączeni użytkownicy|PHSFiltered|
|Wykluczeni użytkownicy jest większy niż uwzględnioni użytkownicy|PHSIncluded|

Ten atrybut można ustawić jako:

- Korzystanie z interfejsu użytkownika Active Directory Użytkownicy i komputery
- przy użyciu `Set-ADUser` polecenia cmdlet programu PowerShell.  Aby uzyskać więcej informacji, zobacz [Set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Wyłącz harmonogram synchronizacji:
Przed rozpoczęciem jakiegokolwiek scenariusza należy wyłączyć harmonogram synchronizacji podczas wprowadzania zmian w regułach synchronizacji.
 1. Uruchom program Windows PowerShell ENTER.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Upewnij się, że harmonogram jest wyłączony, uruchamiając następujące polecenie cmdlet:
     
    ```Get-ADSyncScheduler```

Aby uzyskać więcej informacji na temat harmonogramu, zobacz [Azure AD Connect Scheduler Sync](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Wykluczeni użytkownicy są mniej niż włączeni użytkownicy
W poniższej sekcji opisano, jak włączyć selektywną synchronizację skrótów haseł, gdy liczba użytkowników do **wykluczenia** jest **mniejsza** niż liczba użytkowników do **uwzględnienia**.

>[!Important]
> Przed kontynuowaniem upewnij się, że harmonogram synchronizacji jest wyłączony, jak opisano powyżej.

- Utwórz edytowalną kopię programu **w programie z usługi AD — User AccountEnabled** z opcją **włączenia opcji włączania synchronizacji skrótów haseł** i definiowania jej filtru określania zakresu 
- Utwórz kolejną edytowalną kopię domyślnego elementu **z usługi AD — User AccountEnabled** z opcją **włączenia synchronizacji skrótów haseł wybrane** i zdefiniuj filtr zakresu 
- Włącz ponownie harmonogram synchronizacji 
- Ustaw wartość atrybutu w usłudze Active Directory, która została zdefiniowana jako atrybut zakresu dla użytkowników, którym chcesz zezwolić na synchronizację skrótów haseł. 

>[!Important]
>Kroki podane w celu skonfigurowania synchronizacji skrótów haseł selektywnych będą miały wpływ tylko na obiekty użytkownika, które mają Active Directory **adminDescriptioną** wartość **PHSFiltered**.
Jeśli ten atrybut nie jest wypełniony lub wartość jest inna niż **PHSFiltered** , te reguły nie zostaną zastosowane do obiektów użytkownika.


### <a name="configure-the-necessary-synchronization-rules"></a>Skonfiguruj niezbędne reguły synchronizacji:

 1. Uruchom Edytor reguł synchronizacji i ustaw dla opcji filtry **Synchronizacja hasła** na wartość **włączone** i **Typ reguły** na **standardowa**.
     ![Uruchom Edytor reguł synchronizacji](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Wybierz regułę **z usługi AD — User AccountEnabled** dla łącznika Active Directory lasu, dla którego chcesz skonfigurować hasło selektywne, a następnie kliknij pozycję **Edytuj**. Wybierz pozycję **tak** w następnym oknie dialogowym, aby utworzyć edytowalną kopię oryginalnej reguły.
     ![Wybierz regułę](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. Pierwsza reguła spowoduje wyłączenie synchronizacji skrótów haseł. Podaj następującą nazwę nowej reguły niestandardowej: **w programie AD-User AccountEnabled-Filter users from PHS**.
 Zmień wartość pierwszeństwa na liczbę mniejszą niż 100 (na przykład **90** lub w zależności od tego, która jest najmniejszą wartością dostępną w środowisku).
 Upewnij się, że pola wyboru **Włącz synchronizację haseł** i **wyłączone** są niezaznaczone i c.
 Kliknij przycisk **Dalej**.
  ![Edytuj ruch przychodzący](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj klauzulę**.
 Wybierz pozycję **adminDescription** w kolumnie atrybut, **równej** w kolumnie operator, a następnie wprowadź **PHSFiltered** jako wartość.
     ![Filtr zakresu](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Nie są wymagane żadne dalsze zmiany. **Reguły sprzężenia** i **przekształcenia** powinny pozostać z domyślnymi skopiowanymi ustawieniami, aby można było kliknąć pozycję **Zapisz** teraz.
 Kliknij przycisk **OK** w oknie dialogowym ostrzeżenia z informacją, że pełna synchronizacja zostanie uruchomiona w następnym cyklu synchronizacji łącznika.
     ![Zapisz regułę](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Następnie utwórz kolejną regułę niestandardową z włączoną synchronizacją skrótów haseł. Wybierz ponownie regułę domyślną **w programie z usługi AD — User AccountEnabled** dla lasu Active Directory, dla którego chcesz skonfigurować hasło selektywne z synchronizacją, i kliknij przycisk **Edytuj**. Wybierz pozycję **tak** w następnym oknie dialogowym, aby utworzyć edytowalną kopię oryginalnej reguły.
     ![Reguła niestandardowa](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Podaj następującą nazwę nowej reguły niestandardowej: **w programie AD-User AccountEnabled — użytkownicy włączeni do PHS**.
 Zmień wartość pierwszeństwa na liczbę mniejszą niż wcześniej utworzona reguła (w tym przykładzie będzie to **89**).
 Upewnij się, że pole wyboru **Włącz synchronizację haseł** jest zaznaczone i **wyłączone** pole wyboru nie jest zaznaczone.
 Kliknij przycisk **Dalej**.  
     ![Edytuj nową regułę](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj klauzulę**.
 Wybierz pozycję **adminDescription** w kolumnie atrybut, **NOTEQUAL** w kolumnie operator i wprowadź **PHSFiltered** jako wartość.
     ![Reguła zakresu](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Nie są wymagane żadne dalsze zmiany. **Reguły sprzężenia** i **przekształcenia** powinny pozostać z domyślnymi skopiowanymi ustawieniami, aby można było kliknąć pozycję **Zapisz** teraz.
 Kliknij przycisk **OK** w oknie dialogowym ostrzeżenia z informacją, że pełna synchronizacja zostanie uruchomiona w następnym cyklu synchronizacji łącznika.
     ![Reguły dołączania](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Potwierdź Tworzenie reguł. Usuwanie **filtrów filtry** **i** **Typ reguły** **Standard**. Powinny zostać wyświetlone dwie nowo utworzone reguły.
     ![Potwierdź reguły](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Włącz ponownie harmonogram synchronizacji:  
Po wykonaniu kroków konfigurowania niezbędnych reguł synchronizacji należy ponownie włączyć harmonogram synchronizacji, wykonując następujące czynności:
 1. W programie Windows PowerShell uruchom:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Następnie upewnij się, że został pomyślnie włączony, uruchamiając polecenie:

     ```Get-ADSyncScheduler```

Aby uzyskać więcej informacji na temat harmonogramu, zobacz [Azure AD Connect Scheduler Sync](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Edytuj **adminDescription** użytkowników:
Po zakończeniu wszystkich konfiguracji należy edytować atrybut **adminDescription** dla wszystkich użytkowników, którzy mają zostać **wykluczeni** z synchronizacji skrótów haseł w Active Directory i dodać ciąg używany w filtrze określania zakresu: **PHSFiltered**.
   
  ![Edytowanie atrybutu](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)


## <a name="excluded-users-is-larger-than-included-users"></a>Wykluczeni użytkownicy jest większy niż uwzględnioni użytkownicy
W poniższej sekcji opisano, jak włączyć selektywną synchronizację skrótów haseł, gdy liczba **wykluczonych** użytkowników jest **większa** niż liczba użytkowników do **uwzględnienia**.

>[!Important]
> Przed kontynuowaniem upewnij się, że harmonogram synchronizacji jest wyłączony, jak opisano powyżej.

Poniżej znajduje się podsumowanie akcji, które zostaną wykonane w poniższych krokach:

- Utwórz edytowalną kopię programu **w programie z usługi AD — User AccountEnabled** z opcją **włączenia opcji włączania synchronizacji skrótów haseł** i definiowania jej filtru określania zakresu 
- Utwórz kolejną edytowalną kopię domyślnego elementu **z usługi AD — User AccountEnabled** z opcją **włączenia synchronizacji skrótów haseł wybrane** i zdefiniuj filtr zakresu 
- Włącz ponownie harmonogram synchronizacji 
- Ustaw wartość atrybutu w usłudze Active Directory, która została zdefiniowana jako atrybut zakresu dla użytkowników, którym chcesz zezwolić na synchronizację skrótów haseł. 

>[!Important]
>Kroki podane w celu skonfigurowania synchronizacji skrótów haseł selektywnych będą miały wpływ tylko na obiekty użytkownika, które mają Active Directory **adminDescriptioną** wartość **PHSIncluded**.
Jeśli ten atrybut nie jest wypełniony lub wartość jest inna niż **PHSIncluded** , te reguły nie zostaną zastosowane do obiektów użytkownika.


### <a name="configure-the-necessary-synchronization-rules"></a>Skonfiguruj niezbędne reguły synchronizacji:

 1. Uruchom Edytor reguł synchronizacji i ustaw Filtr hasła filtry  **dla** warstwy **standardowa** i **Typ reguły** .
     ![Typ reguły](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Wybierz regułę **z usługi AD — User AccountEnabled** dla lasu Active Directory, dla którego chcesz skonfigurować hasło selektywne ma synchronizację, i kliknij przycisk **Edytuj**. Wybierz pozycję **tak** w następnym oknie dialogowym, aby utworzyć edytowalną kopię oryginalnej reguły.
     ![W programie z usługi AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. Pierwsza reguła spowoduje wyłączenie synchronizacji skrótów haseł. Podaj następującą nazwę nowej reguły niestandardowej: **w programie AD-User AccountEnabled-Filter users from PHS**.
 Zmień wartość pierwszeństwa na liczbę mniejszą niż 100 (na przykład **90** lub w zależności od tego, która jest najmniejszą wartością dostępną w środowisku).
 Upewnij się, że pola wyboru **Włącz synchronizację haseł** i **wyłączone** nie są zaznaczone.
 Kliknij przycisk **Dalej**.
  ![Ustaw pierwszeństwo](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj klauzulę**.
Wybierz pozycję **adminDescription** w kolumnie atrybut, **NOTEQUAL** w kolumnie operator i wprowadź **PHSIncluded** jako wartość.
     ![Dodaj klauzulę](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Nie są wymagane żadne dalsze zmiany. **Reguły sprzężenia** i **przekształcenia** powinny pozostać z domyślnymi skopiowanymi ustawieniami, aby można było kliknąć pozycję **Zapisz** teraz.
 Kliknij przycisk **OK** w oknie dialogowym ostrzeżenia z informacją, że pełna synchronizacja zostanie uruchomiona w następnym cyklu synchronizacji łącznika.
     ![Przekształcenia](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Następnie utwórz kolejną regułę niestandardową z włączoną synchronizacją skrótów haseł. Wybierz ponownie regułę domyślną **w programie z usługi AD — User AccountEnabled** dla lasu Active Directory, dla którego chcesz skonfigurować hasło selektywne z synchronizacją, i kliknij przycisk **Edytuj**. Wybierz pozycję **tak** w następnym oknie dialogowym, aby utworzyć edytowalną kopię oryginalnej reguły.
     ![AccountEnabled użytkownika](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Podaj następującą nazwę nowej reguły niestandardowej: **w programie AD-User AccountEnabled — użytkownicy włączeni do PHS**.
 Zmień wartość pierwszeństwa na liczbę mniejszą niż wcześniej utworzona reguła (w tym przykładzie będzie to **89**).
 Upewnij się, że pole wyboru **Włącz synchronizację haseł** jest zaznaczone i **wyłączone** pole wyboru nie jest zaznaczone.
 Kliknij przycisk **Dalej**.
     ![Włącz synchronizację haseł](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj klauzulę**.
 Wybierz pozycję **adminDescription** w kolumnie atrybut, **równej** w kolumnie operator, a następnie wprowadź **PHSIncluded** jako wartość.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Nie są wymagane żadne dalsze zmiany. **Reguły sprzężenia** i **przekształcenia** powinny pozostać z domyślnymi skopiowanymi ustawieniami, aby można było kliknąć pozycję **Zapisz** teraz.
 Kliknij przycisk **OK** w oknie dialogowym ostrzeżenia z informacją, że pełna synchronizacja zostanie uruchomiona w następnym cyklu synchronizacji łącznika.
     ![Zapisz teraz](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Potwierdź Tworzenie reguł. Usuwanie **filtrów filtry** **i** **Typ reguły** **Standard**. Powinny zostać wyświetlone dwie nowo utworzone reguły.
     ![Synchronizuj włączone](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Włącz ponownie harmonogram synchronizacji:  
Po wykonaniu kroków konfigurowania niezbędnych reguł synchronizacji należy ponownie włączyć harmonogram synchronizacji, wykonując następujące czynności:
 1. W programie Windows PowerShell uruchom:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Następnie upewnij się, że został pomyślnie włączony, uruchamiając polecenie:

     ```Get-ADSyncScheduler```

Aby uzyskać więcej informacji na temat harmonogramu, zobacz [Azure AD Connect Scheduler Sync](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Edytuj **adminDescription** użytkowników:
Po zakończeniu wszystkich konfiguracji należy edytować atrybut **adminDescription** dla wszystkich użytkowników, którzy mają zostać **dołączeni** do synchronizacji skrótów haseł w Active Directory i dodać ciąg używany w filtrze określania zakresu: **PHSIncluded**.

  ![Edytuj atrybuty](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 

## <a name="next-steps"></a>Następne kroki
- [Co to jest synchronizacja skrótów haseł?](whatis-phs.md)
- [Jak działa synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)
