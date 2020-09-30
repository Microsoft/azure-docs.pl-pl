---
title: Dodatkowe scenariusze licencjonowania opartego na grupach — Azure AD | Microsoft Docs
description: Więcej scenariuszy Azure Active Directory licencjonowania opartego na grupach
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7cc4065297c35164f1e37b187c6280b3d3546ee
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538844"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w programie Azure Active Directory

Skorzystaj z poniższych informacji i przykładów, aby uzyskać bardziej zaawansowane Omówienie licencjonowania opartego na grupach Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Lokalizacja użycia

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić właściwość **Lokalizacja użycia** dla użytkownika. W [Azure Portal](https://portal.azure.com)można określić lokalizację użycia w **User** &gt; **Profile** &gt; **ustawieniach**profilu użytkownika.

W przypadku przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, upewnij się, że zostały one odzwierciedlone prawidłowo w zasobach użytkownika przed dodaniem użytkowników do grup z licencjami.

> [!NOTE]
> Przypisanie licencji grupy nigdy nie spowoduje modyfikacji istniejącej wartości lokalizacji użycia na komputerze użytkownika. Zalecamy, aby zawsze ustawiać lokalizację użycia w ramach przepływu tworzenia użytkownika w usłudze Azure AD (np. przy użyciu konfiguracji programu AAD Connect), która zapewni, że wynik przypisywania licencji jest zawsze poprawny, a użytkownicy nie otrzymują usług w lokalizacjach, które są niedozwolone.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Używanie licencjonowania opartego na grupach z grupami dynamicznymi

Można użyć licencjonowania opartego na grupach z dowolną grupą zabezpieczeń, co oznacza, że można ją połączyć z grupami dynamicznymi usługi Azure AD. Grupy dynamiczne uruchamiają reguły dotyczące atrybutów zasobów użytkownika, aby automatycznie dodawać i usuwać użytkowników z grup.

Na przykład można utworzyć grupę dynamiczną dla pewnego zestawu produktów, które mają być przypisane do użytkowników. Każda grupa jest wypełniana przez regułę, dodając użytkowników według ich atrybutów, a każda grupa ma przypisane licencje, które mają zostać odebrane. Można przypisać atrybut lokalnie i zsynchronizować go z usługą Azure AD lub można zarządzać atrybutem bezpośrednio w chmurze.

Licencje są przypisywane do użytkownika wkrótce po dodaniu ich do grupy. Po zmianie atrybutu użytkownik opuszcza grupy, a licencje są usuwane.

### <a name="example"></a>Przykład

Rozważmy przykład lokalnego rozwiązania do zarządzania tożsamościami, które decyduje o tym, którzy użytkownicy powinni mieć dostęp do usług sieci Web firmy Microsoft. Używa **extensionAttribute1** do przechowywania wartości ciągu reprezentującej licencje, które użytkownik powinien mieć. Azure AD Connect synchronizuje ją z usługą Azure AD.

Użytkownicy mogą potrzebować jednej licencji, ale nie innej, lub mogą być potrzebne. Oto przykład, w którym są dystrybuowane Licencje pakietu Office 365 Enterprise E5 i Enterprise Mobility + Security (EMS) do użytkowników w grupach:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: usługi podstawowe

![Zrzut ekranu usług podstawowych dla pakietu Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: Licencjonowani użytkownicy

![Zrzut ekranu przedstawiający Enterprise Mobility + Security licencjonowanych użytkowników](./media/licensing-group-advanced/o365-e5-licensed-users.png)

W tym przykładzie zmodyfikuj jednego użytkownika i ustaw ich extensionAttribute1 na wartość, `EMS;E5_baseservices;` Jeśli chcesz, aby użytkownik miał obie licencje. Tę modyfikację można wprowadzić lokalnie. Po zsynchronizowaniu zmiany z chmurą użytkownik zostanie automatycznie dodany do obu grup, a licencje są przypisane.

![Zrzut ekranu przedstawiający sposób ustawiania extensionAttribute1 użytkownika](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Podczas modyfikowania reguły członkostwa istniejącej grupy należy zachować ostrożność. Gdy reguła zostanie zmieniona, członkostwo w grupie zostanie ponownie ocenione i użytkownicy, którzy nie są już zgodni z nową regułą, zostaną usunięci (Użytkownicy, którzy nadal pasują do nowej reguły, nie będą mieć żadnych zmian w trakcie tego procesu). Ci użytkownicy zostaną usunięci z licencji podczas procesu, co może spowodować utratę usługi lub w niektórych przypadkach utratę danych.
> 
> Jeśli masz dużą grupę dynamiczną, która jest zależna od przypisywania licencji, rozważ zweryfikowanie wszelkich istotnych zmian w mniejszej grupie testowej przed zastosowaniem ich do grupy głównej.

## <a name="multiple-groups-and-multiple-licenses"></a>Wiele grup i wiele licencji

Użytkownik może być członkiem wielu grup z licencjami. Oto kilka kwestii, które należy wziąć pod uwagę:

- Wiele licencji na ten sam produkt może się pokrywać i powodują, że wszystkie włączone usługi są stosowane do użytkownika. W poniższym przykładzie przedstawiono dwie grupy licencjonowania: *usługi podstawowe* w programie E3 zawierają usługi Foundation do wdrożenia najpierw dla wszystkich użytkowników. I *E3 Extended Services* zawiera dodatkowe usługi (Sway i planista) do wdrożenia tylko dla niektórych użytkowników. W tym przykładzie użytkownik został dodany do obu grup:

  ![Zrzut ekranu z włączonymi usługami](./media/licensing-group-advanced/view-enabled-services.png)

  W związku z tym użytkownik ma 7 usług 12 z włączonym produktem, korzystając tylko z jednej licencji dla tego produktu.

- Wybranie licencji *E3* zawiera więcej szczegółów, w tym informacje o usługach włączonych dla użytkownika przez przypisanie licencji grupy.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Bezpośrednie licencje współistnieją z licencjami grupy

Gdy użytkownik dziedziczy licencję z grupy, nie można bezpośrednio usunąć ani zmodyfikować tego przypisania licencji we właściwościach użytkownika. Zmiany muszą zostać wprowadzone w grupie, a następnie przekazane do wszystkich użytkowników.

Istnieje jednak możliwość przypisania tej samej licencji produktu bezpośrednio do użytkownika, oprócz licencji dziedziczonej. Możesz włączyć dodatkowe usługi z produktu tylko dla jednego użytkownika, bez wpływu na innych użytkowników.

Bezpośrednio przypisane licencje można usunąć i nie mieć wpływu na dziedziczone licencje. Rozważ użycie przez użytkownika, który odziedziczy licencję pakietu Office 365 Enterprise E3 od grupy.

Początkowo użytkownik dziedziczy licencję tylko z *podstawowej grupy usług E3* , która umożliwia korzystanie z czterech planów usług.

1. Wybierz pozycję **Przypisz** , aby bezpośrednio przypisać licencję E3 do użytkownika. W takim przypadku należy wyłączyć wszystkie plany usług z wyjątkiem usługi Yammer Enterprise.

    W związku z tym użytkownik nadal używa tylko jednej licencji produktu E3. Ale bezpośrednie przypisanie umożliwia usłudze Yammer Enterprise tylko dla tego użytkownika. Możesz zobaczyć, które usługi są włączane przez członkostwo w grupie w porównaniu do bezpośredniego przypisywania.

1. W przypadku korzystania z bezpośredniego przypisania są dozwolone następujące operacje:

   - Usługę Yammer Enterprise można wyłączyć bezpośrednio dla zasobu użytkownika. Przełącznik **Włącz/Wyłącz** na ilustracji został włączony dla tej usługi, w przeciwieństwie do innych przełączników usługi. Ponieważ usługa jest włączona bezpośrednio dla użytkownika, może być modyfikowana.
   - Dodatkowe usługi można również włączyć w ramach bezpośrednio przypisanej licencji.
   - Przycisk **Usuń** może służyć do usuwania licencji bezpośredniej od użytkownika. Zobaczysz, że użytkownik ma teraz tylko Odziedziczone licencje grupy i tylko oryginalne usługi pozostają włączone:

## <a name="managing-new-services-added-to-products"></a>Zarządzanie nowymi usługami dodanymi do produktów

Gdy firma Microsoft dodaje nową usługę do planu licencji produktu, jest domyślnie włączona we wszystkich grupach, do których przypisano licencję na produkt. Użytkownicy w organizacji, którzy subskrybują powiadomienia o zmianach produktu, będą otrzymywać wiadomości e-mail z wyprzedzeniem o nadchodzącym czasie dodawania usług.

Jako administrator możesz przejrzeć wszystkie grupy, których dotyczy zmiana, i podjąć działania, takie jak wyłączenie nowej usługi w każdej grupie. Jeśli na przykład utworzono grupy przeznaczone tylko dla określonych usług do wdrożenia, możesz ponownie odwiedzić te grupy i upewnić się, że wszystkie nowo dodane usługi są wyłączone.

Oto przykład tego, jak ten proces może wyglądać następująco:

1. Pierwotnie przypisano produkt *Office 365 Enterprise E5* do kilku grup. Jedna z tych grup o nazwie *O365 E5 — Exchange* została zaprojektowana tak, aby umożliwić jej członkom tylko usługę *Exchange Online (plan 2)* .

2. Otrzymasz powiadomienie od firmy Microsoft, że produkt E5 zostanie rozszerzony przy użyciu nowej usługi — *Microsoft Stream*. Gdy usługa stanie się dostępna w organizacji, możesz wykonać następujące czynności:

3. Przejdź do okna [**Azure Active Directory licencje > > wszystkie produkty**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) i wybierz pozycję *Office 365 Enterprise E5*, a następnie wybierz pozycję **licencjonowane grupy** , aby wyświetlić listę wszystkich grup z tym produktem.

4. Kliknij grupę, którą chcesz przejrzeć (w tym przypadku jest to usługa *O365 E5-Exchange*). Spowoduje to otwarcie karty **licencje** . Kliknięcie licencji E5 spowoduje otwarcie bloku zawierającego listę wszystkich włączonych usług.
   > [!NOTE]
   > Usługa *Microsoft Stream* została automatycznie dodana i włączona w tej grupie, oprócz usługi *Exchange Online* :

   ![Zrzut ekranu nowej usługi dodanej do licencji grupy](./media/licensing-group-advanced/manage-new-services.png)

5. Jeśli chcesz wyłączyć nową usługę w tej grupie, kliknij przełącznik **wł. off** obok usługi, a następnie kliknij przycisk **Zapisz** , aby potwierdzić zmianę. Usługa Azure AD będzie teraz przetwarzać wszystkich użytkowników w grupie w celu zastosowania zmiany. Wszyscy nowi użytkownicy dodani do grupy nie będą mieć włączonej usługi *Microsoft Stream* .

   > [!NOTE]
   > Użytkownicy mogą nadal korzystać z usługi przy użyciu innych przypisań licencji (innych grup, do których należą, lub do bezpośredniego przypisania licencji).

6. W razie potrzeby wykonaj te same kroki dla innych grup, do których ten produkt jest przypisany.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Użyj programu PowerShell, aby zobaczyć, kto ma Odziedziczone i bezpośrednie licencje
Możesz użyć skryptu programu PowerShell, aby sprawdzić, czy użytkownicy mają licencję przypisaną bezpośrednio lub dziedziczoną przez grupę.

1. Uruchom `connect-msolservice` polecenie cmdlet w celu uwierzytelnienia i nawiązania połączenia z organizacją.

2. `Get-MsolAccountSku` można go użyć do odnalezienia wszystkich licencji na produkty inicjowane w organizacji usługi Azure AD.

   ![Zrzut ekranu przedstawiający polecenie cmdlet Get-Msolaccountsku i sprawdź](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Użyj wartości *AccountSkuId* dla licencji, którą interesujesz za pomocą [tego skryptu programu PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Spowoduje to utworzenie listy użytkowników, którzy mają tę licencję z informacjami o sposobie przypisywania licencji.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Korzystanie z dzienników inspekcji do monitorowania działań licencjonowania opartych na grupach

Za pomocą [dzienników inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) można zobaczyć wszystkie działania związane z licencjonowaniem opartym na grupach, w tym:
- kto zmienił licencje dla grup
- gdy system rozpoczął przetwarzanie zmiany licencji grupy, a po jej zakończeniu
- Jakie zmiany licencji zostały dokonane dla użytkownika w wyniku przypisania licencji grupy.

>[!NOTE]
> Dzienniki inspekcji są dostępne na większości bloków w sekcji Azure Active Directory portalu. W zależności od tego, gdzie znajdują się dostęp do nich, filtry mogą być wstępnie zastosowane do wyświetlania tylko działania związane z kontekstem bloku. Jeśli nie widzisz oczekiwanych wyników, przejrzyj [Opcje filtrowania](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) lub uzyskaj dostęp do niefiltrowanych dzienników inspekcji w obszarze [**Azure Active Directory > działania > inspekcje dzienników**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Dowiedz się, kto zmodyfikował licencję grupy

1. Ustaw filtr **działania** , aby *ustawić licencję grupy* , a następnie kliknij przycisk **Zastosuj**.
2. Wyniki obejmują wszystkie przypadki, w których licencje są ustawiane lub modyfikowane w grupach.
   >[!TIP]
   > Możesz również wpisać nazwę grupy w filtrze *docelowym* , aby określić zakres wyników.

3. Wybierz element na liście, aby wyświetlić szczegółowe informacje o tym, co zostało zmienione. W obszarze *zmodyfikowane właściwości* są wyświetlane stare i nowe wartości dla przypisania licencji.

Oto przykład ostatnich zmian licencji grupowych, z uwzględnieniem szczegółów:

![Zrzut ekranu pokazujący stronę "dzienniki inspekcji" z wybranym elementem listy i otwartym okienkiem "dziennik inspekcji szczegółów działania".](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Dowiedz się, kiedy zmiany grupy zostały rozpoczęte i zakończone przetwarzaniem

Po zmianie licencji na grupę usługa Azure AD zacznie stosować zmiany do wszystkich użytkowników.

1. Aby sprawdzić, kiedy grupy rozpoczęły przetwarzanie, Ustaw filtr **działania** , aby *rozpocząć stosowanie licencji opartej na grupie dla użytkowników*. Należy pamiętać, że aktor dla operacji jest *Microsoft Azure AD licencjonowania opartego na grupach* — konta systemowego, które jest używane do wykonywania wszystkich zmian licencji grupowych.
   >[!TIP]
   > Kliknij element na liście, aby wyświetlić pole *zmodyfikowano właściwości* — pokazuje zmiany licencji, które zostały pobrane do przetwarzania. Jest to przydatne, jeśli wprowadzono wiele zmian w grupie i nie masz pewności, która z nich została przetworzona.

2. Podobnie aby zobaczyć, kiedy grupy zostały zakończone przetwarzanie, użyj wartości filtru *zakończenie stosowania licencji opartej na grupie dla użytkowników*.
   > [!TIP]
   > W takim przypadku pole *zmodyfikowano Właściwość* zawiera podsumowanie wyników — jest to przydatne do szybkiego sprawdzenia, czy przetwarzanie spowodowało błędy. Przykładowe dane wyjściowe:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Aby wyświetlić pełny dziennik, w jaki sposób przetworzył grupę, w tym wszystkie zmiany użytkownika, ustaw następujące filtry:
   - **Zainicjowane przez (aktor)**: "Microsoft Azure AD licencjonowania opartego na grupach"
   - **Zakres dat** (opcjonalnie): niestandardowy zakres dla gdy wiadomo, że określona grupa została rozpoczęta i zakończyła przetwarzanie

To przykładowe dane wyjściowe przedstawiają początek przetwarzania, wszystkie wynikowe zmiany użytkownika i zakończenie przetwarzania.

![Zrzut ekranu — zmiany licencji grupy](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknięcie pozycji elementy powiązane ze *zmianą licencji użytkownika* spowoduje wyświetlenie szczegółowych informacji dotyczących zmian licencji zastosowanych do poszczególnych użytkowników.

## <a name="deleting-a-group-with-an-assigned-license"></a>Usuwanie grupy z przypisaną licencją

Nie można usunąć grupy z przypisaną aktywną licencją. Administrator może usunąć grupę niewykonującą, że spowoduje to usunięcie licencji z użytkowników. z tego powodu przed usunięciem tej grupy wymagane są wszystkie licencje.

Podczas próby usunięcia grupy w Azure Portal może zostać wyświetlony komunikat o błędzie: ![ Usuwanie grupy zrzutu ekranu nie powiodło się](./media/licensing-group-advanced/groupdeletionfailed.png)

Przejdź do karty **licencje** w grupie i sprawdź, czy istnieją przypisane licencje. Jeśli tak, usuń te licencje i spróbuj ponownie usunąć grupę.

Podczas próby usunięcia grupy przy użyciu programu PowerShell lub interfejs API programu Graph mogą pojawić się podobne błędy. W przypadku korzystania z grupy zsynchronizowanej z poziomu lokalnego Azure AD Connect mogą także zgłaszać błędy, Jeśli usunięcie grupy w usłudze Azure AD kończy się niepowodzeniem. We wszystkich takich przypadkach upewnij się, że masz przypisane licencje do grupy, a następnie usuń je jako pierwsze.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

W przypadku korzystania z licencjonowania opartego na grupach warto zapoznać się z poniższą listą ograniczeń i znanych problemów.

- Licencjonowanie na podstawie grupy obecnie nie jest obsługiwane dla grup, które zawierają inne grupy (grupy zagnieżdżone). Jeśli zastosujesz licencję do grupy zagnieżdżonej, zostanie ona przypisana tylko do użytkowników będących bezpośrednimi członkami pierwszego poziomu grupy.

- Funkcja może być używana tylko z grupami zabezpieczeń, a grupy Microsoft 365, które mają securityEnabled = TRUE.

- [Centrum administracyjne Microsoft 365](https://admin.microsoft.com) nie obsługuje obecnie licencjonowania opartego na grupach. Jeśli użytkownik odziedziczy licencję z grupy, ta licencja jest wyświetlana w portalu administracyjnym pakietu Office jako zwykła Licencja użytkownika. Jeśli spróbujesz zmodyfikować tę licencję lub spróbujesz usunąć licencję, Portal zwróci komunikat o błędzie. Dziedziczone licencje grupy nie mogą być modyfikowane bezpośrednio dla użytkownika.

- Gdy licencje są przypisywane lub modyfikowane dla dużej grupy (na przykład 100 000 użytkowników), może to mieć wpływ na wydajność. W odniesieniu do wielkości zmian wygenerowanych przez usługę Azure AD Automation może niekorzystnie wpłynąć na wydajność synchronizacji katalogów między systemami Azure AD i lokalnymi.

- Jeśli używasz grup dynamicznych do zarządzania członkostwem użytkowników, sprawdź, czy dany użytkownik należy do grupy. Jest to wymagane do przypisania licencji. Jeśli tak nie jest, [sprawdź stan przetwarzania reguły członkostwa](./groups-create-rule.md) grupy dynamicznej.

- W pewnych sytuacjach wymagających dużego obciążenia może upłynąć dużo czasu, aby przetwarzać zmiany w grupach lub zmianach członkostwa w grupach z istniejącymi licencjami. Jeśli zobaczysz, że zmiany będą trwać dłużej niż 24 godziny w celu przetworzenia rozmiaru grupy 60K użytkowników lub mniej, [Otwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) , aby umożliwić nam zbadanie. 

- Automatyzacja zarządzania licencjami nie reaguje automatycznie na wszystkie typy zmian w środowisku. Na przykład można wypróbować Brak licencji, co może spowodować, że niektórzy użytkownicy będą w stanie błąd. Aby zwolnić dostępną liczbę miejsc, możesz usunąć niektóre bezpośrednio przypisane licencje od innych użytkowników. Jednak system nie reaguje automatycznie na tę zmianę i naprawia użytkowników w tym stanie błędu.

  Aby obejść te ograniczenia, możesz przejść do bloku **grupy** w usłudze Azure AD, a następnie kliknąć pozycję **przetwórz**ponownie. To polecenie przetwarza wszystkich użytkowników w tej grupie i rozwiązuje Stany błędów, jeśli jest to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrować użytkowników między licencjami produktu przy użyciu licencjonowania opartego na grupach w programie Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupach w Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)