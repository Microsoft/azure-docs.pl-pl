---
title: 'Azure AD Connect: Skonfiguruj preferowaną lokalizację danych dla zasobów Microsoft 365'
description: Opisuje sposób umieszczania Microsoft 365 zasobów użytkowników blisko użytkownika z Azure Active Directory Connect synchronizacji.
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
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411137"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect Sync: Konfigurowanie preferowanej lokalizacji danych dla zasobów Microsoft 365
W tym temacie opisano sposób konfigurowania atrybutu dla preferowanej lokalizacji danych w programie Azure Active Directory (Azure AD) Connect Sync. Gdy ktoś korzysta z możliwości wieloznacznych w Microsoft 365, użyj tego atrybutu, aby określić lokalizację geograficzną Microsoft 365 danych użytkownika. ( *Region* terminów i *geograficznie* są używane zamiennie).

## <a name="enable-synchronization-of-preferred-data-location"></a>Włącz synchronizację preferowanej lokalizacji danych
Domyślnie zasoby Microsoft 365 dla użytkowników znajdują się w tej samej lokalizacji geograficznej co dzierżawy usługi Azure AD. Na przykład jeśli dzierżawa znajduje się w Ameryka Północna, skrzynki pocztowe użytkowników programu Exchange są również zlokalizowane w Ameryka Północna. W przypadku organizacji wielonarodowej może to nie być optymalne.

Ustawiając atrybut **preferredDataLocation** , można zdefiniować geograficzną użytkownika. Użytkownik może Microsoft 365 zasoby, takie jak Skrzynka pocztowa i usługa OneDrive, w tym samym georegionie, w którym znajduje się użytkownik, i nadal mieć jedną dzierżawę dla całej organizacji.

> [!IMPORTANT]
> Wiele lokalizacji geograficznych jest obecnie dostępnych dla klientów z aktywną Umowa Enterprise i co najmniej 250 subskrypcji usług Microsoft 365 Services. Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje.
>
>

Listę wszystkich Georegiony dla Microsoft 365 można znaleźć w lokalizacji, w [której znajdują się Twoje dane?](/microsoft-365/enterprise/o365-data-locations).

Georegiony Microsoft 365 dostępne dla wielościeżkowego:

| Obszar geograficzny | preferredDataLocation wartość |
| --- | --- |
| Azja i Pacyfik | ASYNCHRONICZN |
| Australia | JEDNOSTEK analizy |
| Kanada | MOŻE |
| Unia Europejska | EUR |
| Francja | FRA |
| Indie | IND |
| Japonia | JPN |
| Korea | KOR |
| Republika Południowej Afryki | ZAF |
| Szwajcaria | MIEŚĆ |
| Zjednoczone Emiraty Arabskie | LEŻĄ |
| Zjednoczone Królestwo | GBR |
| Stany Zjednoczone | Wietnam |

* Jeśli lokalizacja geograficzna nie jest wymieniona w tej tabeli (na przykład Ameryka Południowa), nie można jej używać w przypadku używania wiele lokalizacji geograficznych.

* Nie wszystkie obciążenia Microsoft 365 obsługują użycie ustawienia geograficznego użytkownika.

### <a name="azure-ad-connect-support-for-synchronization"></a>Obsługa Azure AD Connect synchronizacji

Azure AD Connect obsługuje synchronizację atrybutu **preferredDataLocation** dla obiektów **użytkownika** w wersji 1.1.524.0 i nowszych. W szczególności:

* Schemat typu obiektu **użytkownika** w łączniku usługi Azure AD został rozszerzony w celu uwzględnienia atrybutu **preferredDataLocation** . Ten atrybut jest typu String o pojedynczej wartości.
* Schemat **typu obiektu** w obiekcie Metaverse został rozszerzony tak, aby obejmował atrybut **preferredDataLocation** . Ten atrybut jest typu String o pojedynczej wartości.

Domyślnie **preferredDataLocation** nie jest włączona na potrzeby synchronizacji. Ta funkcja jest przeznaczona dla dużych organizacji. Schemat Active Directory w systemie Windows Server 2019 ma atrybut **msDS-preferredDataLocation** , którego należy użyć w tym celu. Jeśli schemat Active Directory nie został zaktualizowany i nie można tego zrobić, należy zidentyfikować atrybut do przechowywania Microsoft 365 geograficznych dla użytkowników. Jest to różne dla każdej organizacji.

> [!IMPORTANT]
> Usługa Azure AD zezwala na bezpośrednią konfigurację atrybutu **preferredDataLocation** w **obiektach użytkowników w chmurze** przy użyciu programu Azure AD PowerShell. Aby skonfigurować ten atrybut dla **synchronizowanych obiektów użytkownika** , należy użyć Azure AD Connect.

Przed włączeniem synchronizacji:

* Jeśli schemat Active Directory nie został uaktualniony do wersji 2019, zdecyduj, który lokalny atrybut Active Directory ma być używany jako atrybut źródłowy. Powinien być typu **ciąg o pojedynczej wartości**.
* Jeśli wcześniej skonfigurowano atrybut **preferredDataLocation** dla istniejących **synchronizowanych obiektów użytkownika** w usłudze Azure AD przy użyciu programu Azure AD PowerShell, należy backport wartości atrybutów do odpowiednich obiektów **użytkownika** w Active Directory lokalnym.

    > [!IMPORTANT]
    > Jeśli nie backport te wartości, Azure AD Connect usuwa istniejące wartości atrybutów w usłudze Azure AD po włączeniu synchronizacji dla atrybutu **preferredDataLocation** .

* Skonfiguruj atrybut Source dla co najmniej kilku lokalnych obiektów użytkownika Active Directory. Można go użyć do weryfikacji później.

Poniższe sekcje zawierają instrukcje dotyczące włączania synchronizacji atrybutu **preferredDataLocation** .

> [!NOTE]
> Kroki są opisane w kontekście wdrożenia usługi Azure AD z topologią jednego lasu i bez niestandardowych reguł synchronizacji. Jeśli istnieje topologia z obsługą kilku lasów, skonfigurowano niestandardowe reguły synchronizacji lub serwer przejściowy, należy odpowiednio dostosować kroki.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. wyłączenie harmonogramu synchronizacji i sprawdzenie, czy synchronizacja nie jest w toku
Aby uniknąć niezamierzonych zmian, które są eksportowane do usługi Azure AD, należy się upewnić, że synchronizacja nie odbywa się w trakcie aktualizacji reguł synchronizacji. Aby wyłączyć wbudowany harmonogram synchronizacji:

1. Uruchom sesję programu PowerShell na serwerze Azure AD Connect.
2. Wyłącz zaplanowaną synchronizację przez uruchomienie tego polecenia cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Uruchom **Synchronization Service Manager** , aby **uruchomić**  >  **usługę synchronizacji**.
4. Wybierz kartę **operacje** i upewnij się, że nie ma operacji o stanie *w toku*.

![Zrzut ekranu przedstawiający Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Krok 2. Odśwież schemat dla Active Directory
Jeśli schemat Active Directory został zaktualizowany do 2019 i połączenie zostało zainstalowane przed rozszerzeniem schematu, pamięć podręczna schematu łączenia nie ma zaktualizowanego schematu. Następnie należy odświeżyć schemat z poziomu kreatora, aby był wyświetlany w interfejsie użytkownika.

1. Uruchom Kreatora Azure AD Connect z poziomu pulpitu.
2. Wybierz opcję **Odśwież schemat katalogu** i kliknij przycisk **dalej**.
3. Wprowadź swoje poświadczenia usługi Azure AD, a następnie kliknij przycisk **dalej**.
4. Na stronie **Odśwież schemat katalogu** upewnij się, że wszystkie lasy są zaznaczone, a następnie kliknij przycisk **dalej**.
5. Po zakończeniu zamknij kreatora.

![Zrzut ekranu przedstawiający schemat odświeżania katalogu w Kreatorze połączenia](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 3. Dodawanie atrybutu source do schematu łącznika on-premises Active Directory
**Ten krok jest wymagany tylko w przypadku uruchamiania połączenia w wersji 1.3.21 lub starszej. Jeśli używasz programu 1.4.18 lub nowszego, przejdź do kroku 5.**  
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnego obszaru łącznika Active Directory. Jeśli wybrano użycie atrybutu, który domyślnie nie jest zsynchronizowany, należy go zaimportować. Aby dodać atrybut source do listy importowanych atrybutów:

1. Wybierz kartę **Łączniki** w Synchronization Service Manager.
2. Kliknij prawym przyciskiem myszy Active Directory łącznika on-premises, a następnie wybierz pozycję **Właściwości**.
3. W podręcznym oknie dialogowym przejdź do karty **Wybierz atrybuty** .
4. Upewnij się, że atrybut źródłowy wybrany do użycia jest zaznaczony na liście atrybutów. Jeśli nie widzisz atrybutu, zaznacz pole wyboru **Pokaż wszystkie** .
5. Aby zapisać, wybierz **przycisk OK**.

![Zrzut ekranu przedstawiający okno dialogowe Synchronization Service Manager i właściwości z wyróżnioną listą "atrybuty".](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 4. Dodawanie **preferredDataLocation** do schematu łącznika usługi Azure AD
**Ten krok jest wymagany tylko w przypadku uruchamiania połączenia w wersji 1.3.21 lub starszej. Jeśli używasz programu 1.4.18 lub nowszego, przejdź do kroku 5.**  
Domyślnie atrybut **preferredDataLocation** nie jest zaimportowany do obszaru łącznika usługi Azure AD. Aby dodać go do listy importowanych atrybutów:

1. Wybierz kartę **Łączniki** w Synchronization Service Manager.
2. Kliknij prawym przyciskiem myszy łącznik usługi Azure AD, a następnie wybierz pozycję **Właściwości**.
3. W podręcznym oknie dialogowym przejdź do karty **Wybierz atrybuty** .
4. Na liście Wybierz atrybut **preferredDataLocation** .
5. Aby zapisać, wybierz **przycisk OK**.

![Zrzut ekranu przedstawiający okno dialogowe Synchronization Service Manager i właściwości](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Krok 5. Tworzenie reguły synchronizacji ruchu przychodzącego
Reguła synchronizacji ruchu przychodzącego zezwala na przepływ wartości atrybutu z atrybutu Source w Active Directory lokalnym do magazynu Metaverse.

1. Uruchom **Edytor reguł synchronizacji** , przechodząc do **START**  >  **edytora reguł synchronizacji**.
2. Ustaw **kierunek** filtrowania wyszukiwania na **ruch przychodzący**.
3. Aby utworzyć nową regułę ruchu przychodzącego, wybierz pozycję **Dodaj nową regułę**.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Na przykład "w usłudze AD — User preferredDataLocation" |
    | Opis | *Podaj niestandardowy opis* |  |
    | Połączony system | *Wybieranie łącznika Active Directory lokalnego* |  |
    | Typ połączonego obiektu systemu | **Użytkownik** |  |
    | Typ obiektu metaverse | **Osoba** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | 1 – 99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie wybieraj wartości, która jest używana przez inną regułę synchronizacji. |

5. Pozostaw pusty **Filtr zakresu** , aby uwzględnić wszystkie obiekty. Może być konieczne dostosowanie filtru określania zakresu zgodnie ze wdrożeniem Azure AD Connect.
6. Przejdź do **karty transformacja** i zaimplementuj następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Wybierz atrybut źródłowy | Unchecked | Aktualizacja |

7. Aby utworzyć regułę ruchu przychodzącego, wybierz pozycję **Dodaj**.

![Zrzut ekranu przedstawiający regułę synchronizacji ruchu przychodzącego](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Krok 6. Tworzenie reguły synchronizacji danych wychodzących
Reguła synchronizacji danych wychodzących zezwala na przepływ wartości atrybutu z Metaverse do atrybutu **preferredDataLocation** w usłudze Azure AD:

1. Przejdź do **edytora reguł synchronizacji**.
2. Ustaw **kierunek** filtrowania wyszukiwania na **wychodzący**.
3. Wybierz pozycję **Dodaj nową regułę**.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Nazwa | *Podaj nazwę* | Na przykład "do usługi Azure AD — User preferredDataLocation" |
    | Opis | *Podaj opis* ||
    | Połączony system | *Wybieranie łącznika usługi Azure AD* ||
    | Typ połączonego obiektu systemu | **Użytkownik** ||
    | Typ obiektu metaverse | **Osoba** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | 1 – 99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie wybieraj wartości, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i Dodaj pojedynczą grupę filtrów określania zakresu przy użyciu dwóch klauzul:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | WIĘKSZY | Użytkownik |
    | cloudMastered | NOTEQUAL | Prawda |

    Filtr określania zakresu określa, do których obiektów usługi Azure AD jest stosowana ta reguła synchronizacji danych wychodzących. W tym przykładzie używamy tego samego filtru określania zakresu od "out do Azure AD — tożsamość użytkownika" OOB (out-of-Box) reguły synchronizacji. Uniemożliwia stosowanie reguły synchronizacji do obiektów **użytkownika** , które nie są zsynchronizowane z Active Directory lokalnego. Może być konieczne dostosowanie filtru określania zakresu zgodnie ze wdrożeniem Azure AD Connect.

6. Przejdź do karty **transformacja** i zaimplementuj następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Unchecked | Aktualizacja |

7. Zamknij **Dodaj** , aby utworzyć regułę wychodzącą.

![Zrzut ekranu przedstawiający regułę synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Krok 7. Uruchamianie pełnego cyklu synchronizacji
Ogólnie rzecz biorąc, wymagany jest pełny cykl synchronizacji. Wynika to z faktu, że dodano nowe atrybuty do schematu łącznika Active Directory i usługi Azure AD i wprowadzono niestandardowe reguły synchronizacji. Sprawdź zmiany przed ich eksportowaniem do usługi Azure AD. Poniższe kroki służą do weryfikacji zmian podczas ręcznego wykonywania kroków, które składają się na pełny cykl synchronizacji.

1. Uruchom **pełny import** na lokalnym łączniku Active Directory:

   1. Przejdź do karty **operacje** w Synchronization Service Manager.
   2. Kliknij prawym przyciskiem myszy **Active Directory łącznika on-premises** , a następnie wybierz polecenie **Uruchom**.
   3. W oknie dialogowym wybierz opcję **pełny import** , a następnie wybierz **przycisk OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Możesz pominąć pełny import na lokalnym łączniku Active Directory, jeśli atrybut Source już znajduje się na liście importowanych atrybutów. Innymi słowy, nie musisz wprowadzać żadnych zmian w kroku 2 wcześniej w tym artykule.

2. Uruchom **pełny import** z łącznika usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz polecenie **Uruchom**.
   2. W oknie dialogowym wybierz opcję **pełny import** , a następnie wybierz **przycisk OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmiany reguły synchronizacji w istniejącym obiekcie **użytkownika** .

   Atrybut source z lokalnych Active Directory i **preferredDataLocation** z usługi Azure AD został zaimportowany do każdego odpowiedniego obszaru łącznika. Przed przejściem do kroku pełnej synchronizacji wykonaj Podgląd na istniejącym obiekcie **użytkownika** w obszarze łącznika on-premises Active Directory. Wybrany obiekt powinien mieć atrybut Source wypełniony. Udany podgląd z **preferredDataLocationem** wypełnionym w magazynie Metaverse jest dobrym wskaźnikiem, że reguły synchronizacji zostały prawidłowo skonfigurowane. Aby uzyskać informacje na temat sposobu wykonania wersji zapoznawczej, zobacz temat [Weryfikowanie zmiany](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Uruchom **pełną synchronizację** na lokalnym łączniku Active Directory:

   1. Kliknij prawym przyciskiem myszy **Active Directory łącznika on-premises** , a następnie wybierz polecenie **Uruchom**.
   2. W oknie dialogowym wybierz pozycję **pełna synchronizacja** , a następnie wybierz **przycisk OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące eksporty** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz pozycję **obszar łącznika wyszukiwania**.
   2. W oknie dialogowym **przestrzeń łącznika wyszukiwania** :

        a. Ustaw **zakres** na **eksport oczekujący**.<br>
        b. Zaznacz wszystkie trzy pola wyboru, w tym **Dodawanie, modyfikowanie i usuwanie**.<br>
        c. Aby wyświetlić listę obiektów ze zmianami, które mają zostać wyeksportowane, wybierz pozycję **Wyszukaj**. Aby przejrzeć zmiany dla danego obiektu, kliknij dwukrotnie obiekt.<br>
        d. Sprawdź, czy zmiany są oczekiwane.

6. Uruchom **Eksportowanie** z **łącznika usługi Azure AD**

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz polecenie **Uruchom**.
   2. W oknie dialogowym **Uruchamianie łącznika** wybierz opcję **Eksportuj** , a następnie wybierz **przycisk OK**.
   3. Poczekaj na zakończenie operacji.

> [!NOTE]
> Można zauważyć, że kroki nie obejmują pełnego kroku synchronizacji łącznika usługi Azure AD lub kroku eksportu na łączniku Active Directory. Kroki nie są wymagane, ponieważ wartości atrybutów są przepływane tylko z Active Directory lokalnego do usługi Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. ponowne włączenie harmonogramu synchronizacji
Włącz ponownie wbudowany harmonogram synchronizacji:

1. Rozpocznij sesję programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację, uruchamiając to polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Krok 9. Sprawdzenie wyniku
Teraz można sprawdzić konfigurację i włączyć ją dla użytkowników.

1. Dodaj lokalizację geograficzną do wybranego atrybutu na użytkowniku. Listę dostępnych Georegiony można znaleźć w tej tabeli.  
![Zrzut ekranu dotyczący atrybutu AD dodanego do użytkownika](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Poczekaj na zsynchronizowanie atrybutu z usługą Azure AD.
3. Za pomocą programu Exchange Online PowerShell Sprawdź, czy region skrzynki pocztowej został poprawnie ustawiony.  
![Zrzut ekranu programu PowerShell usługi Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Przy założeniu, że dzierżawca został oznaczony do korzystania z tej funkcji, Skrzynka pocztowa jest przenoszona do poprawnej lokalizacji geograficznej. Można to zweryfikować, przeglądając nazwę serwera, na którym znajduje się Skrzynka pocztowa.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wielu lokalizacjach geograficznych w Microsoft 365:

* [Sesje wielogeograficzne podczas zapłonu](https://aka.ms/MultiGeoIgnite)
* [Wiele lokalizacji geograficznych w usłudze OneDrive](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Wiele lokalizacji geograficznych w usłudze SharePoint Online](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Dowiedz się więcej o modelu konfiguracji w aparacie synchronizacji:

* Więcej informacji na temat modelu konfiguracji można znaleźć w temacie [Inicjowanie obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Przeczytaj więcej na temat języka wyrażeń w temacie [Opis wyrażeń aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Tematy dotyczące omówienia:

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)