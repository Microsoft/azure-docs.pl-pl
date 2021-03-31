---
title: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z Azure Active Directory | Microsoft Docs "
description: Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413466"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z Azure Active Directory

Jeśli obiekt nie jest synchronizowany zgodnie z oczekiwaniami Microsoft Azure Active Directory (Azure AD), może to być z kilku powodów. Jeśli otrzymasz komunikat o błędzie z usługi Azure AD lub wystąpił błąd w Azure AD Connect Health, podczas synchronizacji zapoznaj się [z błędami rozwiązywania problemów](tshoot-connect-sync-errors.md) . Ale w przypadku rozwiązywania problemów z tym, że obiekt nie znajduje się w usłudze Azure AD, ten artykuł jest dla Ciebie. Opisano w nim, jak znaleźć błędy w składniku lokalnym Azure AD Connect synchronizacji.

>[!IMPORTANT]
>W przypadku wdrażania Azure AD Connect w wersji 1.1.749.0 lub nowszej Użyj [zadania rozwiązywania problemów](tshoot-connect-objectsync.md) w kreatorze, aby rozwiązać problemy z synchronizacją obiektów. 

## <a name="synchronization-process"></a>Proces synchronizacji

Przed zbadaniem problemów z synchronizacją zapoznaj się z procesem synchronizacji Azure AD Connect:

  ![Diagram procesu synchronizacji Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Przestrzeń łącznika, tabela w bazie danych
* **MV:** Metaverse, tabela w bazie danych

### <a name="synchronization-steps"></a>**Kroki synchronizacji**
Proces synchronizowania obejmuje następujące kroki:

1. **Importuj z usługi AD:** Active Directory obiektów do Active Directory CS.

2. **Importuj z usługi Azure AD:** Obiekty usługi Azure AD są umieszczane w usłudze Azure AD CS.

3. **Synchronizacja:** Reguły synchronizacji ruchu przychodzącego i reguły synchronizacji danych wychodzących są uruchamiane w kolejności według numeru pierwszeństwa, od niższego do wyższego. Aby wyświetlić reguły synchronizacji, przejdź do edytora reguł synchronizacji z aplikacji klasycznych. Reguły synchronizacji ruchu przychodzącego przyjmują dane z CS do MV. Reguły synchronizacji wychodzącej przenosiją dane z MV do CS.

4. **Eksportuj do usługi AD:** Po zsynchronizowaniu obiekty są eksportowane z Active Directory CS do Active Directory.

5. **Eksportuj do usługi Azure AD:** Po zsynchronizowaniu obiekty są eksportowane z usługi Azure AD CS do usługi Azure AD.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby znaleźć błędy, spójrz na kilka różnych miejsc w następującej kolejności:

1. [Dzienniki operacji](#operations) , aby znaleźć błędy zidentyfikowane przez aparat synchronizacji podczas importowania i synchronizowania.
2. [Przestrzeń łącznika](#connector-space-object-properties) , w której mają zostać znalezione brakujące obiekty i błędy synchronizacji.
3. Funkcja [Metaverse](#metaverse-object-properties) do znajdowania problemów związanych z danymi.

Rozpocznij [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) przed rozpoczęciem wykonywania tych kroków.

## <a name="operations"></a>Operacje
Karta **operacje** w Synchronization Service Manager jest miejscem, w którym należy rozpocząć rozwiązywanie problemów. Na tej karcie są wyświetlane wyniki najnowszych operacji. 

![Zrzut ekranu przedstawiający Synchronization Service Manager, z wybraną kartą operacje](./media/tshoot-connect-object-not-syncing/operations.png)  

W górnej połowie karty **operacje** są wyświetlane wszystkie uruchomienia w kolejności chronologicznej. Domyślnie dziennik operacji przechowuje informacje o ciągu ostatnich siedmiu dni, ale to ustawienie można zmienić przy użyciu [harmonogramu](how-to-connect-sync-feature-scheduler.md). Poszukaj dowolnego przebiegu, który nie pokazuje stanu **sukcesu** . Sortowanie można zmienić, klikając nagłówki.

Kolumna **stan** zawiera najważniejsze informacje i pokazuje najbardziej poważny problem z uruchomieniem. Poniżej przedstawiono krótkie podsumowanie najbardziej typowych stanów w kolejności sprawdzania priorytetu (gdzie * wskazuje kilka możliwych ciągów błędów).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-* |Nie można zakończyć przebiegu. Może się tak zdarzyć, jeśli na przykład system zdalny nie działa i nie można się z nim skontaktować. |
| Zatrzymano — limit błędów |Występuje więcej niż 5 000 błędów. Przebieg został automatycznie zatrzymany z powodu dużej liczby błędów. |
| ukończono — \* Błędy |Przebieg został ukończony, ale wystąpiły błędy (mniej niż 5 000), które powinny zostać zbadane. |
| ukończone — \* ostrzeżenia |Przebieg został ukończony, ale niektóre dane nie są w oczekiwanym stanie. Jeśli występują błędy, ten komunikat jest zazwyczaj tylko objawem. Nie Badaj ostrzeżeń, dopóki nie zostaną rozkierowane błędy. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza Dolna część karty **operacje** zostanie zaktualizowana, aby wyświetlić szczegóły tego uruchomienia. Po lewej stronie tego obszaru może istnieć lista o tytule **krok nr**. Ta lista jest wyświetlana tylko w przypadku, gdy w lesie znajduje się wiele domen, a każda z nich jest reprezentowana przez krok. Nazwę domeny można znaleźć pod **partycją** nagłówka. W nagłówku **statystyki synchronizacji** można znaleźć więcej informacji na temat liczby przetworzonych zmian. Wybierz linki, aby uzyskać listę zmienionych obiektów. Jeśli masz obiekty z błędami, te błędy są wyświetlane pod nagłówkiem **Błędy synchronizacji** .

### <a name="errors-on-the-operations-tab"></a>Błędy na karcie operacje
Gdy występują błędy, Synchronization Service Manager pokazuje zarówno obiekt z błędami, jak i sam błąd jako linki, które zawierają więcej informacji.

![Zrzut ekranu przedstawiający błędy w Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Zacznij od wybrania ciągu błędu. (Na powyższym rysunku ciąg błędu to **Sync-Rule-Error-Function-wyzwolone**). Najpierw przedstawiono przegląd obiektu. Aby wyświetlić faktyczny błąd, wybierz pozycję **ślad stosu**. Ten ślad zawiera informacje na poziomie debugowania dotyczące błędu.

Kliknij prawym przyciskiem myszy pole **Informacje o stosie wywołań** , kliknij pozycję **Zaznacz wszystko**, a następnie wybierz polecenie **Kopiuj**. Następnie skopiuj stos i sprawdź błąd w ulubionym edytorze, np. w Notatniku.

Jeśli błąd jest z **SyncRulesEngine**, informacje stosu wywołań najpierw wyświetlają wszystkie atrybuty obiektu. Przewiń w dół do momentu, gdy zobaczysz nagłówek **InnerException =>**.  

  ![Zrzut ekranu przedstawiający Synchronization Service Manager, w którym są wyświetlane informacje o błędzie w obszarze nagłówek InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Wiersz po nagłówku pokazuje błąd. Na powyższym rysunku Wystąpił błąd pochodzący z niestandardowej reguły synchronizacji utworzonej przez firmę fabrikam.

Jeśli błąd nie daje wystarczającej ilości informacji, czas na wyświetlenie danych. Wybierz łącze z identyfikatorem obiektu i kontynuuj rozwiązywanie problemów z [zaimportowanym obiektem łącznika](#cs-import).

## <a name="connector-space-object-properties"></a>Właściwości obiektu obszaru łącznika
Jeśli na karcie [**operacje**](#operations) nie są wyświetlane żadne błędy, postępuj zgodnie z obiektem przestrzeń łącznika z Active Directory do magazynu Metaverse do usługi Azure AD. W tej ścieżce należy znaleźć miejsce problemu.

### <a name="searching-for-an-object-in-the-cs"></a>Wyszukiwanie obiektu w CS

W Synchronization Service Manager wybierz pozycję **Łączniki**, wybierz łącznik Active Directory, a następnie wybierz pozycję **obszar łącznika wyszukiwania**.

W polu **zakres** wybierz pozycję **RDN** , jeśli chcesz wyszukać atrybut CN, lub wybierz **nazwę wyróżniającą lub zakotwiczenie** , aby wyszukać atrybut **odróżnionyname** . Wprowadź wartość i wybierz pozycję **Wyszukaj**. 
 
![Zrzut ekranu przedstawiający wyszukiwanie w miejscu łącznika](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Jeśli nie znajdziesz szukanego obiektu, być może został on przefiltrowany przy użyciu [filtrowania opartego na domenie](how-to-connect-sync-configure-filtering.md#domain-based-filtering) lub [filtrowania opartego na jednostce organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Aby sprawdzić, czy filtrowanie jest skonfigurowane zgodnie z oczekiwaniami, Odczytaj [Azure AD Connect synchronizacji: Skonfiguruj filtrowanie](how-to-connect-sync-configure-filtering.md).

Możesz wykonać inne przydatne wyszukiwanie, wybierając łącznik usługi Azure AD. W polu **zakres** wybierz pozycję **oczekujące na import**, a następnie zaznacz pole wyboru **Dodaj** . To wyszukiwanie umożliwia wyszukanie wszystkich zsynchronizowanych obiektów w usłudze Azure AD, które nie mogą być skojarzone z obiektem lokalnym.  

![Zrzut ekranu oddzielony w przeszukiwaniu miejsca łącznika](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Te obiekty zostały utworzone przez inny aparat synchronizacji lub aparat synchronizacji z inną konfiguracją filtrowania. Te oddzielone obiekty nie są już zarządzane. Przejrzyj tę listę i Rozważ usunięcie tych obiektów przy użyciu poleceń cmdlet [programu PowerShell usługi Azure AD](/previous-versions/azure/jj151815(v=azure.100)) .

### <a name="cs-import"></a>Import CS
Po otwarciu obiektu CS istnieje kilka kart u góry. Karta **Importowanie** przedstawia dane, które są przemieszczane po zaimportowaniu.  

![Zrzut ekranu przedstawiający obiekt przestrzeni łącznika okno Właściwości z wybraną kartą importowania](./media/tshoot-connect-object-not-syncing/csobject.png)    

W kolumnie **stara wartość** są wyświetlane dane, które są obecnie przechowywane w programie Connect, a w kolumnie **Nowa wartość** są wyświetlane dane odebrane z systemu źródłowego i nie zostały jeszcze zastosowane. Jeśli wystąpi błąd w obiekcie, zmiany nie zostaną przetworzone.

Karta **błąd synchronizacji** jest widoczna w oknie **właściwości obiektu obszaru łącznika** tylko wtedy, gdy występuje problem z obiektem. Aby uzyskać więcej informacji, zapoznaj się z tematem [Rozwiązywanie problemów z błędami synchronizacji na karcie **operacje**](#errors-on-the-operations-tab).

![Zrzut ekranu przedstawiający kartę błąd synchronizacji w obiekcie przestrzeni łącznika okno Właściwości](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Elementy powiązane z CS
Na **karcie elementy** w oknie **właściwości obiektu obszaru łącznika** pokazuje, jak obiekt przestrzeni łącznika jest powiązany z obiektem Metaverse. Możesz zobaczyć, kiedy łącznik ostatnio zaimportował zmianę z podłączonego systemu i które reguły zastosowały się do wypełniania danych w magazynie Metaverse.  

![Zrzut ekranu przedstawiający kartę elementy w obiekcie przestrzeni łącznika okno Właściwości](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na powyższym rysunku kolumna **Akcja** zawiera regułę synchronizacji ruchu przychodzącego z **zainicjowaną** akcją. Oznacza to, że o ile ten obiekt przestrzeni łącznika jest obecny, obiekt Metaverse pozostaje. Jeśli zamiast tego lista reguł synchronizacji zawiera regułę synchronizacji wychodzącej z akcją **udostępniania** , ten obiekt jest usuwany po usunięciu obiektu Metaverse.  

![Zrzut ekranu przedstawiający okno elementu zależnego na karcie elementy w obiekcie przestrzeni łącznika okno Właściwości](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na powyższym rysunku można także zobaczyć w kolumnie **PasswordSync** , że przestrzeń łącznika przychodzącego może współtworzyć zmiany hasła, ponieważ jedna reguła synchronizacji ma wartość **true**. To hasło jest wysyłane do usługi Azure AD za pomocą reguły ruchu wychodzącego.

**Z poziomu karty elementy** powiązane można przejść do obiektu metaverse, wybierając pozycję [**właściwości obiektów Metaverse**](#mv-attributes).

### <a name="preview"></a>Wersja zapoznawcza
W lewym dolnym rogu okna **właściwości obiektu obszaru łącznika** jest przycisk **Podgląd** . Wybierz ten przycisk, aby otworzyć stronę **podglądu** , na której można zsynchronizować pojedynczy obiekt. Ta strona jest przydatna, jeśli rozwiązywasz problemy z niestandardowymi regułami synchronizacji i chcesz zobaczyć wpływ zmiany na pojedynczy obiekt. Można wybrać **pełną synchronizację** lub **synchronizację Delta**. Możesz również wybrać opcję **Generuj Podgląd**, która zachowuje tylko zmiany w pamięci. Lub wybierz **Podgląd zatwierdzania**, która aktualizuje funkcję Metaverse i etapy wszystkie zmiany do miejsc łączników docelowych.  

![Zrzut ekranu strony podglądu z wybraną pozycją Rozpocznij podgląd](./media/tshoot-connect-object-not-syncing/preview.png)  

W wersji zapoznawczej można sprawdzić obiekt i sprawdzić, która reguła została zastosowana do określonego przepływu atrybutu.  

![Zrzut ekranu strony podglądu pokazujący przepływ atrybutu importu](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Dziennik
Obok przycisku **Podgląd** wybierz przycisk **Dziennik** , aby otworzyć stronę **Dziennik** . W tym miejscu można zobaczyć stan i historię synchronizacji haseł. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Właściwości obiektu metaverse
Zwykle lepiej jest rozpocząć wyszukiwanie ze źródłowej przestrzeni łącznika Active Directory. Możesz również rozpocząć wyszukiwanie z poziomu Metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Wyszukiwanie obiektu w MV
W Synchronization Service Manager wybierz pozycję **Wyszukiwanie** w trybie Metaverse, jak na poniższej ilustracji. Utwórz zapytanie, które wie, że znajduje użytkownika. Wyszukaj typowe atrybuty, takie jak **AccountName** (**sAMAccountName**) i **userPrincipalName**. Aby uzyskać więcej informacji, zobacz [synchronizacja Service Manager funkcji wyszukiwania Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Zrzut ekranu przedstawiający Synchronization Service Manager z wybraną kartą wyszukiwania Metaverse](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

W oknie **wyniki wyszukiwania** kliknij obiekt.

Jeśli obiekt nie został odnaleziony, nie osiągnął jeszcze Metaverse. Kontynuuj wyszukiwanie obiektu w [obszarze łącznika](#connector-space-object-properties)Active Directory. Jeśli odnajdziesz obiekt w obszarze łącznika Active Directory, może wystąpić błąd synchronizacji, który blokuje obiekt z przychodzącymi do Metaverse lub filtr określania zakresu reguł synchronizacji.

### <a name="object-not-found-in-the-mv"></a>Nie znaleziono obiektu w MV
Jeśli obiekt znajduje się w Active Directory CS, ale nie występuje w MV, zostanie zastosowany filtr zakresu. Aby sprawdzić filtr określania zakresu, przejdź do menu aplikacji klasycznej i wybierz pozycję **Edytor reguł synchronizacji**. Odfiltruj reguły odpowiednie dla obiektu, dostosowując Poniższy filtr.

  ![Zrzut ekranu edytora reguł synchronizacji, pokazujący wyszukiwanie reguł synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Przejrzyj wszystkie reguły na liście powyżej i sprawdź **Filtr określania zakresu**. W poniższym filtrze określania zakresu, jeśli wartość **isCriticalSystemObject** jest równa null lub FAŁSZ lub pusta, znajduje się w zakresie.

  ![Zrzut ekranu przedstawiający filtr określania zakresu w wyszukiwaniu reguły synchronizacji ruchu przychodzącego](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Przejdź do listy atrybutów [importu CS](#cs-import) i sprawdź, który filtr blokuje obiekt od przejścia do MV. Lista atrybutów **obszaru łącznika** będzie zawierać tylko atrybuty inne niż null i niepuste. Na przykład jeśli **isCriticalSystemObject** nie pojawia się na liście, wartość tego atrybutu jest zerowa lub pusta.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Nie znaleziono obiektu w usłudze Azure AD CS
Jeśli obiekt nie występuje w miejscu łącznika usługi Azure AD, ale znajduje się w MV, spójrz na filtr określania zakresu reguł ruchu wychodzącego odpowiadającego mu miejsca łącznika i sprawdź, czy obiekt jest odfiltrowany, ponieważ [atrybuty MV](#mv-attributes) nie spełniają kryteriów.

Aby przyjrzeć się filtrowi zakresu wychodzącego, wybierz odpowiednie reguły dla obiektu, dostosowując Poniższy filtr. Wyświetl każdą regułę i sprawdź odpowiadającą jej wartość [atrybutu MV](#mv-attributes) .

  ![Zrzut ekranu przedstawiający reguły synchronizacji danych wychodzących wyszukiwanie w Edytorze reguł synchronizacji](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atrybuty MV
Na karcie **atrybuty** są widoczne wartości i łączniki, które zostały przez niego utworzone.  

![Zrzut ekranu przedstawiający obiekt Metaverse okno Właściwości, z wybraną kartą atrybuty](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Jeśli obiekt nie jest synchronizowany, zapoznaj się z poniższymi pytaniami dotyczącymi Stanów atrybutów w obiekcie Metaverse:
- Czy atrybut **cloudFiltered** jest obecny i ma ustawioną **wartość PRAWDA**? Jeśli tak, zostało ono przefiltrowane zgodnie z krokami [filtrowania opartego na atrybutach](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Czy atrybut **sourceAnchor** jest obecny? Jeśli nie, czy masz topologię lasu zasobów konta? Jeśli obiekt jest identyfikowany jako połączona Skrzynka pocztowa (atrybut **msExchRecipientTypeDetails** ma wartość **2**), **sourceAnchor** jest tworzony przez las z włączonym kontem Active Directory. Upewnij się, że konto główne zostało zaimportowane i zsynchronizowane prawidłowo. Główne konto musi znajdować się na liście [łączników](#mv-connectors) dla obiektu.

### <a name="mv-connectors"></a>MV — łączniki
Na karcie **Łączniki** są wyświetlane wszystkie miejsca łączników, które mają reprezentację obiektu. 
 
![Zrzut ekranu przedstawiający obiekt Metaverse okno Właściwości, z wybraną kartą łączniki](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Należy mieć łącznik, aby:

- Każdy Las Active Directory jest reprezentowany przez użytkownika. Ta reprezentacja może obejmować obiekty **foreignSecurityPrincipals** i **Contact** .
- Łącznik w usłudze Azure AD.

Jeśli nie masz łącznika z usługą Azure AD, zapoznaj się z sekcją [atrybuty MV](#mv-attributes) , aby sprawdzić kryteria udostępniania w usłudze Azure AD.

Na karcie **Łączniki** możesz również przejść do [obiektu spacja łącznika](#connector-space-object-properties). Zaznacz wiersz i kliknij pozycję **Właściwości**.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Azure AD Connect synchronizacji](how-to-connect-sync-whatis.md).
- Dowiedz się więcej o [tożsamości hybrydowej](whatis-hybrid-identity.md).