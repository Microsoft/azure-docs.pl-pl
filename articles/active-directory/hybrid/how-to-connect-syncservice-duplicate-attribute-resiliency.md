---
title: Synchronizacja tożsamości i odporność na zduplikowane atrybuty | Microsoft Docs
description: Nowe zachowanie sposobu obsługi obiektów z nazwami UPN lub ProxyAddress konflikty podczas synchronizacji katalogów przy użyciu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413432"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizacja tożsamości i odporność względem zduplikowanych atrybutów
Odporność na zduplikowane atrybuty to funkcja w Azure Active Directory, która eliminuje liczbę problemów spowodowanych **przez** wartości **ProxyAddress** i protokołu SMTP podczas uruchamiania jednego z narzędzi do synchronizacji firmy Microsoft.

Te dwa atrybuty zwykle muszą być unikatowe dla wszystkich obiektów **użytkowników**, **grup** lub **kontaktów** w danej dzierżawie Azure Active Directory.

> [!NOTE]
> Tylko użytkownicy mogą mieć nazwy UPN.
> 
> 

Nowe zachowanie, które ta funkcja włącza, znajduje się w części w chmurze potoku synchronizacji, w związku z czym jest niezależny od klienta i ma zastosowanie do dowolnego produktu synchronizacji firmy Microsoft, w tym Azure AD Connect, narzędzia DirSync i programu MIM + łącznik. Ogólny termin "klient synchronizacji" jest używany w tym dokumencie do reprezentowania dowolnego z tych produktów.

## <a name="current-behavior"></a>Bieżące zachowanie
Jeśli podjęto próbę zainicjowania nowego obiektu z wartością UPN lub ProxyAddress, która narusza to ograniczenie unikatowości, Azure Active Directory blokuje tworzenie tego obiektu. Podobnie, jeśli obiekt jest aktualizowany z nieunikatową nazwą UPN lub ProxyAddress, aktualizacja kończy się niepowodzeniem. Próba aprowizacji lub aktualizacja zostanie ponowiona przez klienta synchronizacji w każdym cyklu eksportu i w dalszym ciągu kończy się niepowodzeniem do momentu rozwiązania konfliktu. Wiadomość e-mail z raportem o błędzie jest generowana przy każdej próbie, a klient synchronizacji rejestruje błąd.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Zachowanie z odpornością na zduplikowane atrybuty
Zamiast całkowicie niepowodzeniem zainicjować lub zaktualizować obiekt ze zduplikowanym atrybutem, Azure Active Directory "Kwarantanna", zduplikowany atrybut, który mógłby naruszać ograniczenia unikatowości. Jeśli ten atrybut jest wymagany do aprowizacji, na przykład UserPrincipalName, usługa przypisuje wartość symbolu zastępczego. Format tych wartości tymczasowych to  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> . onmicrosoft.com**_.

Proces odporności atrybutów obsługuje tylko wartości **ProxyAddress** UPN i SMTP.

Jeśli atrybut nie jest wymagany, taki jak  **ProxyAddress**, Azure Active Directory po prostu przekwarantanna atrybutu konfliktu i kontynuuje proces tworzenia lub aktualizowania obiektu.

Po poddawania kwarantannie atrybutu informacje o konflikcie są wysyłane w tym samym wiadomości e-mail z raportem o błędzie użytym w starym zachowaniu. Jednak te informacje są wyświetlane tylko w raporcie o błędzie po raz, gdy Kwarantanna przejdzie, nie jest nadal rejestrowane w przyszłych wiadomościach e-mail. Ponadto, ponieważ eksport dla tego obiektu zakończył się pomyślnie, klient synchronizacji nie rejestruje błędu i nie ponawia próby wykonania operacji tworzenia/aktualizacji przy kolejnych cyklach synchronizacji.

Aby obsłużyć to zachowanie, dodano nowy atrybut do klasy użytkownika, grupy i obiektu kontaktu:  
**DirSyncProvisioningErrors**

Jest to wielowartościowy atrybut, który jest używany do przechowywania atrybutów powodujących konflikt, które mogłyby naruszać ograniczenia unikatowości, jeśli są one dodawane normalnie. Zadanie czasomierza w tle zostało włączone w Azure Active Directory, które jest uruchamiane co godzinę, aby wyszukać zduplikowane konflikty atrybutów, które zostały rozwiązane, i automatycznie usunie te atrybuty z kwarantanny.

### <a name="enabling-duplicate-attribute-resiliency"></a>Włączanie odporności na zduplikowane atrybuty
Odporność na zduplikowane atrybuty będzie nowym zachowaniem domyślnym dla wszystkich dzierżawców Azure Active Directory. Będzie ona domyślnie włączona dla wszystkich dzierżawców, którzy włączyli synchronizację po raz pierwszy od 22 sierpnia, 2016 lub nowszych. Dzierżawy, które włączyły synchronizację przed tą datą, będą miały włączoną funkcję w partiach. To wdrożenie rozpocznie się we wrześniu 2016, a powiadomienie e-mail zostanie wysłane do kontaktu z powiadomieniem technicznym każdej dzierżawy z określoną datą, gdy funkcja zostanie włączona.

> [!NOTE]
> Po włączeniu odporności na zduplikowane atrybuty nie można jej wyłączyć.

Aby sprawdzić, czy funkcja jest włączona dla Twojej dzierżawy, możesz to zrobić, pobierając najnowszą wersję modułu Azure Active Directory PowerShell i uruchamiając następujące polecenie:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Nie można już używać Set-MsolDirSyncFeature polecenia cmdlet do aktywnego włączania funkcji odporności na zduplikowane atrybuty przed włączeniem jej dla dzierżawy. Aby można było przetestować tę funkcję, należy utworzyć nową dzierżawę Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identyfikowanie obiektów z błędami DirSyncProvisioningErrors
Obecnie istnieją dwie metody identyfikacji obiektów, które mają te błędy ze względu na konflikty zduplikowanych właściwości, Azure Active Directory program PowerShell i [Microsoft 365 centrum administracyjne](https://admin.microsoft.com). W przyszłości istnieją plany umożliwiające przejęcie dodatkowych raportów opartych na portalu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
W przypadku poleceń cmdlet programu PowerShell w tym temacie są spełnione następujące kwestie:

* W przypadku wszystkich następujących poleceń cmdlet jest rozróżniana wielkość liter.
* Wartość **– ErrorCategory PropertyConflict** musi zawsze być uwzględniona. Obecnie nie ma żadnych innych typów **ErrorCategory**, ale może to zostać przedłużone w przyszłości.

Najpierw Rozpocznij pracę, uruchamiając polecenie **Connect-MsolService** i wprowadzając poświadczenia dla administratora dzierżawy.

Następnie użyj następujących poleceń cmdlet i operatorów, aby wyświetlić błędy na różne sposoby:

1. [Zobacz wszystko](#see-all)
2. [Według typu właściwości](#by-property-type)
3. [Przez wartość powodującą konflikt](#by-conflicting-value)
4. [Używanie wyszukiwania ciągów](#using-a-string-search)
5. Sorted
6. [W ograniczonej ilości lub wszystkich](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobacz wszystko
Po nawiązaniu połączenia, aby wyświetlić ogólną listę błędów aprowizacji atrybutów w przebiegu dzierżawy:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Daje to wynik podobny do poniższego:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Według typu właściwości
Aby wyświetlić błędy według typu właściwości, Dodaj flagę **-PropertyName** z argumentem **userPrincipalName** lub **proxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Lub

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Przez wartość powodującą konflikt
Aby wyświetlić błędy dotyczące konkretnej właściwości, Dodaj flagę **-PropertyValue** (**-PropertyName** należy również użyć podczas dodawania tej flagi):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Używanie wyszukiwania ciągów
Aby przeszukać wiele ciągów, Użyj flagi **-ciągwyszukiwania** . Można go użyć niezależnie od wszystkich powyższych flag, z wyjątkiem **-ErrorCategory PropertyConflict**, który jest zawsze wymagany:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>W ograniczonej ilości lub wszystkich
1. **MaxResults \<Int>** może służyć do ograniczania zapytania do określonej liczby wartości.
2. **Wszystkie** te mogą służyć do upewnienia się, że wszystkie wyniki są pobierane w przypadku, gdy istnieje duża liczba błędów.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum administracyjne platformy Microsoft 365
Błędy synchronizacji katalogów można wyświetlić w centrum administracyjnym Microsoft 365. Raport w centrum administracyjnym Microsoft 365 wyświetla tylko obiekty **użytkownika** , które mają te błędy. Nie pokazuje informacji o konfliktach między **grupami** i **kontaktami**.

![Zrzut ekranu pokazujący błędy synchronizacji katalogów w centrum administracyjnym Microsoft 365.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktywni użytkownicy")

Aby uzyskać instrukcje dotyczące wyświetlania błędów synchronizacji katalogów w centrum administracyjnym Microsoft 365, zobacz [identyfikowanie błędów synchronizacji katalogów w Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Synchronizacja tożsamości raport o błędach
Gdy w nowym zachowaniu jest obsługiwany obiekt ze zduplikowanym konfliktem atrybutów, w wiadomości e-mail dotyczącej standardowego błędu synchronizacji tożsamości zostanie uwzględniona powiadomienie wysyłanego do kontaktu z powiadomieniem technicznym dla dzierżawy. Istnieje jednak ważna zmiana tego zachowania. W przeszłości informacje o zduplikowanym konflikcie atrybutu zostałyby uwzględnione w każdym kolejnym raporcie o błędach do momentu rozwiązania konfliktu. W przypadku tego nowego zachowania powiadomienia o błędzie dla danego konfliktu są wyświetlane tylko raz — w momencie, gdy atrybut powodujący konflikt zostanie poddany kwarantannie.

Oto przykład powiadomienia e-mail dla konfliktu ProxyAddress:  
    ![Zrzut ekranu przedstawiający przykład powiadomienia e-mail o konflikcie ProxyAddress.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktywni użytkownicy")  

## <a name="resolving-conflicts"></a>Rozwiązywanie konfliktów
Rozwiązywanie problemów z strategią i rozdzielczością taktykę dla tych błędów nie powinno różnić się od sposobu, w jaki zduplikowane błędy atrybutów zostały obsłużone w przeszłości. Jedyną różnicą jest to, że zadanie czasomierza jest wyczyszczone przez dzierżawcę po stronie usługi, aby automatycznie dodać ten atrybut do odpowiedniego obiektu po rozwiązaniu konfliktu.

W poniższym artykule opisano różne strategie rozwiązywania problemów i rozwiązań: [zduplikowane lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w pakiecie Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Znane problemy
Żadna z tych znanych problemów powoduje utratę danych lub spadek wydajności usługi. Niektóre z nich są estetyczne, inne powodują, że standardowe błędy "*wstępnej odporności*" są generowane zamiast poddawania kwarantannie atrybutu konfliktu, a inne powodują pewne błędy, aby wymagać dodatkowej ręcznej poprawki.

**Podstawowe zachowanie:**

1. Obiekty z określonymi konfiguracjami atrybutów nadal otrzymują błędy eksportu zamiast zduplikowanych atrybutów, które są poddane kwarantannie.  
   Na przykład:
   
    a. Nowy użytkownik jest tworzony w usłudze AD przy użyciu nazwy UPN Jan **\@ contoso.com** i ProxyAddress **SMTP: Jan \@ contoso.com**
   
    b. Właściwości tego obiektu powodują konflikt z istniejącą grupą, gdzie ProxyAddress to **SMTP: jan \@ contoso.com**.
   
    c. Podczas eksportowania jest zgłaszany błąd **konfliktu ProxyAddress** , zamiast mieć atrybuty konfliktów poddane kwarantannie. Operacja jest ponawiana podczas każdego kolejnego cyklu synchronizacji, tak jak przed włączeniem funkcji odporności.
2. Jeśli dwie grupy są tworzone lokalnie przy użyciu tego samego adresu SMTP, jeden z nich nie zostanie udostępniony przy pierwszej próbie przy użyciu standardowego duplikatu błędu **ProxyAddress** . Jednak duplikat wartości jest prawidłowo poddany kwarantannie w następnym cyklu synchronizacji.

**Raport portalu pakietu Office**:

1. Szczegółowy komunikat o błędzie dla dwóch obiektów w zestawie konfliktów nazw UPN jest taki sam. Oznacza to, że mają obie nazwy UPN zmienione/poddane kwarantannie, gdy tylko jeden z nich zmienił dane.
2. Szczegółowy komunikat o błędzie dotyczący konfliktu nazw UPN przedstawia niewłaściwy element displayName dla użytkownika, który miał nazwę UPN zmieniono/poddane kwarantannie. Na przykład:
   
    a. **Użytkownik A** najpierw synchronizuje się z nazwą **UPN = User \@ contoso.com**.
   
    b. **Użytkownik B** próbuje zsynchronizować się dalej z nazwą **UPN = User \@ contoso.com**.
   
    c. **Użytkownik B** Nazwa UPN jest zmieniana na **User1234 \@ contoso.onmicrosoft.com** i **\@ contoso.com użytkownika** zostanie dodana do **DirSyncProvisioningErrors**.
   
    d. Komunikat o błędzie dla **użytkownika B** powinien wskazywać, że **użytkownik** ma już **użytkownika \@ contoso.com** jako nazwę UPN, ale wyświetla własną wartość DisplayName **użytkownika B** .

**Synchronizacja tożsamości raport o błędach**:

Link do *procedury rozwiązywania tego problemu* jest niepoprawny:  
    ![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktywni użytkownicy")  

Powinno to wskazywać na [https://aka.ms/duplicateattributeresiliency]() .

## <a name="see-also"></a>Zobacz też
* [Synchronizacja Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Zidentyfikuj błędy synchronizacji katalogów w Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)