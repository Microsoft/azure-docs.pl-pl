---
title: Błędy Azure AD Connect-LargeObject spowodowane przez atrybut userCertificate | Microsoft Docs
description: W tym temacie przedstawiono kroki korygowania błędów funkcji LargeObject spowodowanych przez atrybut userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861752"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect Sync: obsługa błędów Dużegoobject spowodowanych przez atrybut userCertificate

Usługa Azure AD wymusza maksymalny limit **15** wartości certyfikatów w atrybucie **userCertificate** . Jeśli Azure AD Connect eksportuje obiekt mający więcej niż 15 wartości do usługi Azure AD, usługa Azure AD zwróci błąd **dużegoobject** z komunikatem:

>*"Obiekt aprowizacji jest zbyt duży. Przytnij liczbę wartości atrybutów dla tego obiektu. Operacja zostanie ponowiona w następnym cyklu synchronizacji... "*

Błąd LargeObject może być spowodowany przez inne atrybuty usługi AD. Aby potwierdzić, że jest to spowodowane przez atrybut userCertificate, należy sprawdzić względem obiektu w lokalnej usłudze AD lub w [wyszukiwaniu metaverse Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Aby uzyskać listę obiektów w dzierżawie z błędami LargeObject, należy użyć jednej z następujących metod:

 * Jeśli dzierżawa jest włączona dla Azure AD Connect Health do synchronizacji, można odwołać się do podanego [raportu o błędzie synchronizacji](./how-to-connect-health-sync.md) .
 
 * W wiadomości e-mail z powiadomieniem o błędach synchronizacji katalogów wysyłanych na końcu każdego cyklu synchronizacji znajduje się lista obiektów z błędami typu LargeObject. 
 * [Karta operacje Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-operations.md) wyświetla listę obiektów z błędami LargeObject po kliknięciu najnowszej operacji Eksportuj do usługi Azure AD.
 
## <a name="mitigation-options"></a>Opcje łagodzenia
Do momentu rozpoznania błędu Dużegoobject nie można wyeksportować innych zmian atrybutów do tego samego obiektu do usługi Azure AD. Aby rozwiązać ten problem, można wziąć pod uwagę następujące opcje:

 * Uaktualnij Azure AD Connect do kompilowania 1.1.524.0 lub po nim. W Azure AD Connect build 1.1.524.0 reguły synchronizacji out-of-Box zostały zaktualizowane, aby nie eksportować atrybutów userCertificate i userSMIMECertificate, jeśli atrybuty mają więcej niż 15 wartości. Aby uzyskać szczegółowe informacje na temat uaktualniania Azure AD Connect, zapoznaj się z artykułem [Azure AD Connect: Uaktualnij poprzednią wersję do najnowszej wersji](./how-to-upgrade-previous-version.md).

 * Zaimplementuj **regułę synchronizacji danych wychodzących** w Azure AD Connect, która eksportuje **wartość null, a nie wartości rzeczywiste dla obiektów mających więcej niż 15 wartości certyfikatów**. Ta opcja jest odpowiednia, jeśli nie jest wymagane, aby żadne z wartości certyfikatów były eksportowane do usługi Azure AD dla obiektów mających więcej niż 15 wartości. Aby uzyskać szczegółowe informacje na temat implementowania tej reguły synchronizacji, zapoznaj się z sekcją [wykonywanie reguły synchronizacji, aby ograniczyć eksport atrybutu userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Zmniejsz liczbę wartości certyfikatów w lokalnym obiekcie usługi AD (15 lub mniej) przez usunięcie wartości, które nie są już używane przez organizację. Jest to przydatne, jeśli atrybut przeładowanie jest spowodowany przez wygasłe lub nieużywane certyfikaty. Możesz użyć [dostępnego tutaj skryptu programu PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) , aby pomóc w znalezieniu, utworzeniu kopii zapasowej i usunięciu wygasłych certyfikatów w lokalnej usłudze AD. Przed usunięciem certyfikatów zalecamy zweryfikowanie administratorów infrastruktury kluczy publicznych w organizacji.

 * Skonfiguruj Azure AD Connect, aby wykluczyć atrybut userCertificate z eksportu do usługi Azure AD. Ogólnie rzecz biorąc, nie zalecamy tej opcji, ponieważ atrybut może być używany przez usługi online firmy Microsoft do włączania określonych scenariuszy. W szczególności:
    * Atrybut userCertificate obiektu user jest używany przez klientów usługi Exchange Online i programu Outlook do podpisywania i szyfrowania wiadomości. Aby dowiedzieć się więcej na temat tej funkcji, zapoznaj się z artykułem [S/MIME na potrzeby podpisywania i szyfrowania wiadomości](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption).

    * Atrybut userCertificate w obiekcie Computer jest używany przez usługę Azure AD do zezwalania lokalnym urządzeniom przyłączonym do domeny systemu Windows 10 na łączenie się z usługą Azure AD. Aby dowiedzieć się więcej na temat tej funkcji, zapoznaj się z artykułem [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD dla systemu Windows 10](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementowanie reguły synchronizacji w celu ograniczenia eksportu atrybutu userCertificate
Aby rozwiązać błąd LargeObject spowodowany przez atrybut userCertificate, można zaimplementować wychodzącą regułę synchronizacji w Azure AD Connect, która eksportuje **wartość null, a nie wartości rzeczywiste dla obiektów mających więcej niż 15 wartości certyfikatów**. W tej sekcji opisano kroki wymagane do zaimplementowania reguły synchronizacji dla obiektów **użytkownika** . Kroki można dostosować dla obiektów **kontaktów** i **komputerów** .

> [!IMPORTANT]
> Eksportowanie wartości null powoduje usunięcie wartości certyfikatów, które zostały wcześniej wyeksportowane do usługi Azure AD.

Kroki mogą być podsumowywane jako:

1. Wyłącz harmonogram synchronizacji i sprawdź, czy synchronizacja nie jest w toku.
3. Znajdź istniejącą regułę synchronizacji ruchu wychodzącego dla atrybutu userCertificate.
4. Utwórz wymaganą regułę synchronizacji danych wychodzących.
5. Sprawdź nową regułę synchronizacji w istniejącym obiekcie z błędem LargeObject.
6. Zastosuj nową regułę synchronizacji do pozostałych obiektów z błędem LargeObject.
7. Sprawdź, czy nie ma żadnych nieoczekiwanych zmian oczekujących na wyeksportowanie do usługi Azure AD.
8. Wyeksportuj zmiany do usługi Azure AD.
9. Ponowne włączenie harmonogramu synchronizacji.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Wyłącz harmonogram synchronizacji i sprawdź, czy synchronizacja nie jest w toku
Upewnij się, że synchronizacja nie odbywa się w trakcie wdrażania nowej reguły synchronizacji, aby uniknąć niezamierzonych zmian eksportowanych do usługi Azure AD. Aby wyłączyć wbudowany harmonogram synchronizacji:
1. Uruchom sesję programu PowerShell na serwerze Azure AD Connect.

2. Wyłącz zaplanowaną synchronizację przez uruchomienie polecenia cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Powyższe kroki mają zastosowanie tylko do nowszych wersji (1.1. xxx. x) Azure AD Connect z wbudowanym harmonogramem. Jeśli używasz starszych wersji (1.0. xxx. x) Azure AD Connect, które korzystają z systemu Windows Harmonogram zadań, lub używasz własnego harmonogramu niestandardowego (nietypowego) do wyzwalania okresowej synchronizacji, należy odpowiednio je wyłączyć.

1. Uruchom **Synchronization Service Manager** , aby uruchomić usługę synchronizacji →.

1. Przejdź do karty **operacje** i sprawdź, czy nie ma żadnych operacji, których stan to *"w toku".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Znajdź istniejącą regułę synchronizacji ruchu wychodzącego dla atrybutu userCertificate
Powinna istnieć istniejąca reguła synchronizacji, która jest włączona i skonfigurowana do eksportowania atrybutu userCertificate dla obiektów użytkownika w usłudze Azure AD. Znajdź tę regułę synchronizacji, aby sprawdzić jej **priorytet** i konfigurację **filtru zakresu** :

1. Uruchom **Edytor reguł synchronizacji** , aby uruchomić Edytor reguł synchronizacji →.

2. Skonfiguruj filtry wyszukiwania przy użyciu następujących wartości:

    | Atrybut | Wartość |
    | --- | --- |
    | Kierunek |**Wychodzący** |
    | MV — typ obiektu |**Person (Osoba)** |
    | Łącznik |*nazwa łącznika usługi Azure AD* |
    | Typ obiektu łącznika |**Użytkownicy** |
    | MV — atrybut |**userCertificate** |

3. Jeśli używasz reguły synchronizacji OOB (out-of-Box) do programu Azure AD Connector do eksportowania atrybutu userCertficiate dla obiektów użytkownika, należy uzyskać regułę *"Out to AAD – User ExchangeOnline"* .
4. Zanotuj wartość **pierwszeństwa** tej reguły synchronizacji.
5. Wybierz regułę synchronizacji, a następnie kliknij przycisk **Edytuj**.
6. W oknie dialogowym *"Edytowanie potwierdzenia reguły zastrzeżonej"* kliknij przycisk **nie**. (Nie martw się, nie będziemy wprowadzać żadnych zmian w tej regule synchronizacji).
7. Na ekranie Edycja wybierz kartę **Filtr zakresu** .
8. Zanotuj konfigurację filtru określania zakresu. W przypadku korzystania z reguły synchronizacji OOB należy dokładnie mieć **jedną grupę filtrów zakresu zawierającą dwie klauzule**, w tym:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | WIĘKSZY | Użytkownik |
    | cloudMastered | NOTEQUAL | Prawda |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Utwórz wymaganą regułę synchronizacji danych wychodzących
Nowa reguła synchronizacji musi mieć ten sam **Filtr określania zakresu** i **wyższy priorytet** niż istniejąca reguła synchronizacji. Dzięki temu Nowa reguła synchronizacji ma zastosowanie do tego samego zestawu obiektów co istniejąca reguła synchronizacji i zastępuje istniejącą regułę synchronizacji atrybutu userCertificate. Aby utworzyć regułę synchronizacji:
1. W Edytorze reguł synchronizacji kliknij przycisk **Dodaj nową regułę** .
2. Na **karcie Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Na przykład: *"do usługi AAD — niestandardowe zastąpienie dla userCertificate"* |
    | Opis | *Podaj opis* | Na przykład, *"Jeśli atrybut userCertificate ma więcej niż 15 wartości, wyeksportuj null".* |
    | Połączony system | *Wybieranie łącznika usługi Azure AD* |
    | Typ połączonego obiektu systemu | **Użytkownicy** | |
    | Typ obiektu metaverse | **Sprzedawca** | |
    | Typ łącza | **Dołącz** | |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1-99* | Wybrana liczba nie może być używana przez żadną istniejącą regułę synchronizacji i ma niższą wartość (i dlatego wyższy priorytet) niż istniejąca reguła synchronizacji. |

3. Przejdź do karty **Filtr zakresu** i zaimplementuj ten sam filtr określania zakresu, w którym jest używana istniejąca reguła synchronizacji.
4. Pomiń kartę **reguły sprzężenia** .
5. Przejdź do karty **przekształcenia** , aby dodać nową transformację przy użyciu następującej konfiguracji:

    | Atrybut | Wartość |
    | --- | --- |
    | Typ przepływu |**Wyrażenie** |
    | Atrybut docelowy |**userCertificate** |
    | Atrybut źródłowy |*Użyj następującego wyrażenia*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknij przycisk **Dodaj** , aby utworzyć regułę synchronizacji.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Sprawdź nową regułę synchronizacji w istniejącym obiekcie z błędem LargeObject
Ma to na celu sprawdzenie, czy utworzona reguła synchronizacji działa prawidłowo w istniejącym obiekcie usługi AD z powodu błędu Dużegoobject przed zastosowaniem go do innych obiektów:
1. Przejdź do karty **operacje** w Synchronization Service Manager.
2. Wybierz najnowszą operację eksportowania do usługi Azure AD i kliknij jeden z obiektów z błędami LargeObject.
3.  Na ekranie podręcznym właściwości obiektu łącznika kliknij przycisk **Podgląd** .
4. Na ekranie podręcznym Podgląd Wybierz pozycję **pełna synchronizacja** , a następnie kliknij pozycję **Podgląd zatwierdzania**.
5. Zamknij ekran podglądu i ekran właściwości obiektu obszaru łącznika.
6. Przejdź do karty **Łączniki** w Synchronization Service Manager.
7. Kliknij prawym przyciskiem myszy łącznik usługi **Azure AD** , a następnie wybierz polecenie **Uruchom...**
8. W oknie podręcznym uruchamiania łącznika wybierz pozycję **Eksportuj** krok, a następnie kliknij przycisk **OK**.
9. Poczekaj na zakończenie eksportu do usługi Azure AD i upewnij się, że w tym konkretnym obiekcie nie ma więcej błędów o dużym rozmiarze.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Zastosuj nową regułę synchronizacji do pozostałych obiektów z błędem LargeObject
Po dodaniu reguły synchronizacji należy wykonać krok pełnej synchronizacji na łączniku usługi AD:
1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
2. Kliknij prawym przyciskiem myszy łącznik **usługi AD** i wybierz polecenie **Uruchom...**
3. W oknie podręcznym Uruchom łącznik wybierz pozycję **pełna synchronizacja** , a następnie kliknij przycisk **OK**.
4. Poczekaj na zakończenie kroku pełnej synchronizacji.
5. Powtórz powyższe kroki dla pozostałych łączników usługi AD, jeśli masz więcej niż jedną liczbę łączników usługi AD. Zwykle wiele łączników jest wymaganych, jeśli istnieje wiele katalogów lokalnych.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Sprawdź, czy nie ma żadnych nieoczekiwanych zmian oczekujących na wyeksportowanie do usługi Azure AD
1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
2. Kliknij prawym przyciskiem myszy łącznik usługi **Azure AD** , a następnie wybierz pozycję **obszar łącznika wyszukiwania**.
3. W oknie podręcznym łącznika wyszukiwania:
    1. Ustaw zakres na **eksport oczekujący**.
    2. Zaznacz wszystkie 3 pola wyboru, w tym **Dodawanie**, **Modyfikowanie** i **usuwanie**.
    3. Kliknij przycisk **Wyszukaj** , aby zwrócić wszystkie obiekty ze zmianami oczekującymi na wyeksportowanie do usługi Azure AD.
    4. Sprawdź, czy nie ma żadnych nieoczekiwanych zmian. Aby przejrzeć zmiany dla danego obiektu, kliknij dwukrotnie obiekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Eksportowanie zmian do usługi Azure AD
Aby wyeksportować zmiany do usługi Azure AD:
1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
2. Kliknij prawym przyciskiem myszy łącznik usługi **Azure AD** , a następnie wybierz polecenie **Uruchom...**
4. W oknie podręcznym uruchamiania łącznika wybierz pozycję **Eksportuj** krok, a następnie kliknij przycisk **OK**.
5. Poczekaj na zakończenie eksportu do usługi Azure AD i upewnij się, że nie ma więcej błędów usługi LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Włącz ponownie harmonogram synchronizacji
Po rozwiązaniu problemu należy ponownie włączyć wbudowany harmonogram synchronizacji:
1. Uruchom sesję programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację przez uruchomienie polecenia cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Powyższe kroki mają zastosowanie tylko do nowszych wersji (1.1. xxx. x) Azure AD Connect z wbudowanym harmonogramem. Jeśli używasz starszych wersji (1.0. xxx. x) Azure AD Connect, które korzystają z systemu Windows Harmonogram zadań, lub używasz własnego harmonogramu niestandardowego (nietypowego) do wyzwalania okresowej synchronizacji, należy odpowiednio je wyłączyć.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
