---
title: 'Azure AD Connect: Uaktualnianie z poprzedniej wersji | Microsoft Docs'
description: Wyjaśnia różne metody uaktualniania do najnowszej wersji Azure Active Directory Connect, w tym uaktualnienie w miejscu i przemieszczenie na zasięg.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729994"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: uaktualnianie z wcześniejszej wersji do najnowszej
W tym temacie opisano różne metody, których można użyć w celu uaktualnienia instalacji programu Azure Active Directory (Azure AD) Connect do najnowszej wersji.  W przypadku zmiany konfiguracji należy również użyć kroków [z sekcji](#swing-migration) przedziały czasowe.

>[!NOTE]
> Ważne jest, aby zachować aktualność serwerów z najnowszymi wersjami Azure AD Connect. Ciągle wprowadzamy uaktualnienia do programu AADConnect, a te uaktualnienia obejmują poprawki dotyczące problemów z zabezpieczeniami i błędów, a także ich użyteczność, poprawę wydajności i skalowalności. Aby zobaczyć, co to jest Najnowsza wersja, i dowiedzieć się, jakie zmiany zostały wprowadzone między wersjami, zapoznaj się z [historią wersji](./reference-connect-version-history.md) .

>[!NOTE]
> Jest ona obecnie obsługiwana w celu uaktualnienia z dowolnej wersji Azure AD Connect do bieżącej wersji. Uaktualnienia w miejscu narzędzia DirSync lub ADSync nie są obsługiwane, a migracja wymaga przemieszczenia.  Jeśli chcesz przeprowadzić uaktualnienie z narzędzia DirSync, zobacz temat [Uaktualnianie z poziomu usługi Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md) lub sekcji [migracja z zasięgiem](#swing-migration) .  </br>W tym przypadku klienci korzystający z wyjątkowo starych wersji mogą napotkać problemy, które nie są bezpośrednio związane z Azure AD Connect. Serwery, które były w fazie produkcyjnej przez kilka lat, zazwyczaj mają do nich zastosowanie kilka poprawek, ale nie wszystkie z nich można uwzględnić.  Ogólnie rzecz biorąc Klienci, którzy nie dokonali uaktualnienia w 12-18 miesiącach, powinni rozważyć przeprowadzenie uaktualnienia, a nie jest to najbardziej umiarkowane i najmniej ryzykowne rozwiązanie.

Jeśli chcesz przeprowadzić uaktualnienie z narzędzia DirSync, zapoznaj się z tematem [Uaktualnij narzędzie Azure AD Sync Tool (DirSync)](how-to-dirsync-upgrade-get-started.md) .

Istnieje kilka różnych strategii, których można użyć do uaktualnienia Azure AD Connect.

| Metoda | Opis |
| --- | --- |
| [Automatycznie uaktualnianie](how-to-connect-install-automatic-upgrade.md) |Jest to najprostsza metoda dla klientów korzystających z instalacji ekspresowej. |
| [Uaktualnienie w miejscu](#in-place-upgrade) |Jeśli masz jeden serwer, możesz uaktualnić instalację w miejscu na tym samym serwerze. |
| [Migracja typu swing](#swing-migration) |W przypadku dwóch serwerów można przygotować jeden z serwerów z nową wersją lub konfiguracją i zmienić aktywny serwer, gdy wszystko będzie gotowe. |

Aby uzyskać informacje o uprawnieniach, zobacz [uprawnienia wymagane do uaktualnienia](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po włączeniu nowego serwera Azure AD Connect, aby rozpocząć synchronizowanie zmian z usługą Azure AD, nie należy przywracać z powrotem do korzystania z narzędzia DirSync ani Azure AD Sync. Obniżenie wersji z Azure AD Connect na starszych klientów, w tym narzędzia DirSync i Azure AD Sync, nie jest obsługiwane i może prowadzić do problemów, takich jak utrata danych w usłudze Azure AD.

## <a name="in-place-upgrade"></a>Uaktualnienie w miejscu
Uaktualnienie w miejscu działa w przypadku przechodzenia z Azure AD Sync lub Azure AD Connect. Nie działa on w przypadku przechodzenia z narzędzia DirSync lub rozwiązania za pomocą programu Forefront Identity Manager (FIM) + Azure AD Connector.

Ta metoda jest preferowana w przypadku posiadania jednego serwera i mniejszego niż około 100 000 obiektów. W przypadku wprowadzenia zmian w regułach synchronizacji poza Box następuje pełne Importowanie i pełna synchronizacja po uaktualnieniu. Ta metoda zapewnia, że nowa konfiguracja zostanie zastosowana do wszystkich istniejących obiektów w systemie. Ten przebieg może potrwać kilka godzin, w zależności od liczby obiektów, które znajdują się w zakresie aparatu synchronizacji. Harmonogram normalnego synchronizacji różnicowej (co domyślnie synchronizuje co 30 minut) jest wstrzymywany, ale synchronizacja haseł jest kontynuowana. Podczas weekendu warto rozważyć przeprowadzenie uaktualnienia w miejscu. Jeśli nie wprowadzono żadnych zmian w konfiguracji wstępnej w nowej wersji Azure AD Connect, zamiast tego zostanie uruchomione normalne Importowanie/synchronizacja przyrostowa.  
![Uaktualnienie w miejscu](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Jeśli wprowadzono zmiany w regułach synchronizacji out-of-Box, te reguły są przywracane do konfiguracji domyślnej podczas uaktualniania. Aby upewnić się, że konfiguracja jest utrzymywana między uaktualnieniami, upewnij się, że wprowadzasz zmiany zgodnie z opisem w [temacie najlepsze rozwiązania dotyczące zmiany konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md).

W trakcie uaktualniania w miejscu mogą zostać wprowadzone zmiany wymagające wykonania określonych czynności synchronizacji (w tym krok pełnego importu i krok pełnej synchronizacji), które mają zostać wykonane po zakończeniu uaktualniania. Aby odroczyć te działania, zapoznaj się z sekcją [jak odroczyć pełną synchronizację po uaktualnieniu](#how-to-defer-full-synchronization-after-upgrade).

Jeśli używasz Azure AD Connect z łącznikiem niestandardowym (na przykład ogólny łącznik LDAP i ogólny łącznik SQL), należy odświeżyć odpowiednią konfigurację łącznika w [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) po uaktualnieniu w miejscu. Aby uzyskać szczegółowe informacje na temat odświeżania konfiguracji łącznika, zapoznaj się z sekcją artykułu w temacie [historia wersji — Rozwiązywanie problemów](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Jeśli konfiguracja nie zostanie odświeżona, procedury importowania i eksportowania nie będą działać poprawnie dla łącznika. W dzienniku zdarzeń aplikacji zostanie wyświetlony następujący błąd z komunikatem *"wersja zestawu w konfiguracji łącznika usługi AAD (" x. x. xxx. x ") jest wcześniejsza niż wersja rzeczywista (" x. X. xxx. X ") elementu" C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ".*

## <a name="swing-migration"></a>Migracja typu swing
Jeśli masz złożone wdrożenie lub wiele obiektów, może być niepraktyczne, aby przeprowadzić uaktualnienie w miejscu na żywo systemu. W przypadku niektórych klientów ten proces może trwać wiele dni i w tym czasie nie są przetwarzane żadne zmiany różnicowe. Możesz również użyć tej metody, gdy planujesz wprowadzić znaczące zmiany w konfiguracji i chcesz wypróbować je przed wypchnięciem do chmury.

Zalecaną metodą tych scenariuszy jest użycie migracji wahadłowej. Potrzebujesz (co najmniej) dwóch serwerów — jednego aktywnego serwera i jednego serwera przejściowego. Aktywny serwer (wyświetlany z pełnymi niebieskimi liniami na poniższej ilustracji) jest odpowiedzialny za aktywne obciążenie produkcyjne. Serwer przemieszczania (wyświetlany z kreskami purpurowymi) jest przygotowana do nowej wersji lub konfiguracji. Gdy wszystko jest w pełni gotowe, ten serwer zostanie uaktywniony. Na poprzednim serwerze aktywnym, na którym jest zainstalowana starsza wersja lub konfiguracja, została wprowadzona do serwera przemieszczania i uaktualniona.

Te dwa serwery mogą korzystać z różnych wersji. Na przykład aktywny serwer, który ma zostać zlikwidowany, może używać Azure AD Sync, a nowy serwer przemieszczania może korzystać z Azure AD Connect. W przypadku korzystania z migracji z przeznaczeniem do tworzenia nowej konfiguracji dobrym pomysłem jest posiadanie tych samych wersji na obu serwerach.  
![Serwer przemieszczania](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Niektórzy klienci wolą mieć trzy lub cztery serwery w tym scenariuszu. Po uaktualnieniu serwera przemieszczania nie ma serwera kopii zapasowej na potrzeby [odzyskiwania po awarii](how-to-connect-sync-staging-server.md#disaster-recovery). Trzy lub cztery serwery umożliwiają przygotowanie jednego zestawu serwerów podstawowych/rezerwowych z nową wersją, co gwarantuje, że zawsze jest gotowy do przejęcia serwer przejściowy.

Te kroki umożliwiają również przechodzenie z Azure AD Sync lub rozwiązania przy użyciu programu FIM + Azure AD Connector. Te kroki nie działają w przypadku narzędzia DirSync, ale taka sama metoda migracji wahadła (nazywana również wdrożeniem równoległym) z krokami narzędzia DirSync jest [uaktualniana Azure Active Directory Sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Przeprowadź migrację z przeznaczeniem na zasięg
1. Jeśli używasz Azure AD Connect na obu serwerach i planujesz tylko zmianę konfiguracji, upewnij się, że na serwerze aktywnym i przejściowym są używane te same wersje. Ułatwia to porównywanie różnic w późniejszym czasie. Jeśli uaktualniasz program z Azure AD Sync, te serwery mają różne wersje. Jeśli uaktualniasz starszą wersję Azure AD Connect, dobrym pomysłem jest rozpoczęcie od dwóch serwerów, które korzystają z tej samej wersji, ale nie jest to wymagane.
2. Jeśli została utworzona Konfiguracja niestandardowa, a serwer tymczasowy nie ma tego serwera, wykonaj kroki opisane w sekcji [przenoszenie konfiguracji niestandardowej z aktywnego serwera na serwer przejściowy](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. W przypadku uaktualniania z wcześniejszej wersji Azure AD Connect należy uaktualnić serwer przejściowy do najnowszej wersji. Jeśli przenosisz z Azure AD Sync, Zainstaluj Azure AD Connect na serwerze tymczasowym.
4. Pozwól, aby aparat synchronizacji uruchomił pełny import i pełną synchronizację na serwerze tymczasowym.
5. Sprawdź, czy nowa konfiguracja nie powodowała żadnych nieoczekiwanych zmian, wykonując kroki opisane w sekcji "verify" (Sprawdź [konfigurację serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)). Jeśli coś nie jest zgodnie z oczekiwaniami, popraw je, uruchom import i zsynchronizuj i sprawdź dane, dopóki nie będzie wyglądało dobrze, wykonując czynności opisane w sekcji.
6. Przełącz serwer przemieszczania na aktywny serwer. Jest to ostatni krok "Przełącz aktywny serwer" w obszarze [Weryfikowanie konfiguracji serwera](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. W przypadku uaktualniania Azure AD Connect Uaktualnij serwer, który jest teraz w trybie przejściowym do najnowszej wersji. Wykonaj te same kroki co wcześniej, aby uzyskać uaktualnione dane i konfigurację. W przypadku uaktualnienia z Azure AD Sync można teraz wyłączyć i zlikwidować stary serwer.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Przenoszenie konfiguracji niestandardowej z aktywnego serwera na serwer tymczasowy
Jeśli wprowadzono zmiany w konfiguracji aktywnego serwera, należy się upewnić, że te same zmiany są stosowane do serwera przejściowego. Aby pomóc w tym przeniesieniu, można użyć [dokumentu konfiguracji Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Można przenieść niestandardowe reguły synchronizacji utworzone przy użyciu programu PowerShell. Należy zastosować inne zmiany w taki sam sposób w obu systemach i nie można migrować zmian. [Dokument configurationer](https://github.com/Microsoft/AADConnectConfigDocumenter) może pomóc w porównaniu obu systemów, aby upewnić się, że są one identyczne. Narzędzie może również pomóc w automatyzowaniu kroków znalezionych w tej sekcji.

Należy skonfigurować następujące czynności w taki sam sposób na obu serwerach:

* Połączenie z tymi samymi lasami
* Wszystkie filtrowanie domen i jednostek organizacyjnych
* Te same funkcje opcjonalne, takie jak synchronizacja haseł i zapisywanie zwrotne haseł

**Przenoszenie reguł synchronizacji niestandardowej**  
Aby przenieść niestandardowe reguły synchronizacji, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** na aktywnym serwerze.
2. Wybierz regułę niestandardową. Kliknij polecenie **Eksportuj**. Spowoduje to wyświetlenie okna Notatnika. Zapisz plik tymczasowy z rozszerzeniem PS1. Powoduje to skrypt programu PowerShell. Skopiuj plik PS1 na serwer przejściowy.  
   ![Eksport reguły synchronizacji](./media/how-to-upgrade-previous-version/exportrule.png)
3. Identyfikator GUID łącznika jest inny na serwerze tymczasowym i należy go zmienić. Aby uzyskać identyfikator GUID, uruchom **Edytor reguł synchronizacji**, wybierz jedną z reguł, które reprezentują ten sam system połączony, i kliknij przycisk **eksport**. Zastąp identyfikator GUID w pliku PS1 identyfikatorem GUID z serwera przemieszczania.
4. W wierszu polecenia programu PowerShell uruchom plik PS1. Spowoduje to utworzenie niestandardowej reguły synchronizacji na serwerze tymczasowym.
5. Powtórz tę czynność dla wszystkich reguł niestandardowych.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak odroczyć pełną synchronizację po uaktualnieniu
W trakcie uaktualniania w miejscu mogą zostać wprowadzone zmiany wymagające wykonania określonych czynności synchronizacji (w tym krok pełnego importu i krok pełnej synchronizacji). Na przykład zmiany schematu łącznika wymagają **pełnych** zmian reguł synchronizacji, a poza ramką wymagają wykonania **pełnego kroku synchronizacji** na łącznikach, których to dotyczy. Podczas uaktualniania Azure AD Connect określa, jakie działania synchronizacji są wymagane i zapisuje je w postaci *przesłonięć*. W następującym cyklu synchronizacji harmonogram synchronizacji wybiera te zastąpienia i wykonuje je. Przesłonięcie zostanie usunięte po pomyślnym wykonaniu.

Mogą wystąpić sytuacje, w których nie chcesz, aby te zastąpienia zostały wykonane natychmiast po uaktualnieniu. Na przykład masz wiele zsynchronizowanych obiektów i chcesz, aby te kroki synchronizacji miały miejsce po godzinach pracy. Aby usunąć następujące zastąpienia:

1. Podczas uaktualniania **Usuń zaznaczenie** opcji **Rozpocznij proces synchronizacji po zakończeniu konfiguracji**. Spowoduje to wyłączenie harmonogramu synchronizacji i uniemożliwia automatyczne przeprowadzenie cyklu synchronizacji przed usunięciem przesłonięć.

   ![Zrzut ekranu, który podświetla opcję Rozpocznij proces synchronizacji, gdy konfiguracja jest ukończona, należy wyczyścić.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Po zakończeniu uaktualniania Uruchom następujące polecenie cmdlet, aby dowiedzieć się, jakie zastąpienia zostały dodane: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Zastąpienia są specyficzne dla łącznika. W poniższym przykładzie dodano krok pełnego importu i krok pełnej synchronizacji do lokalnego łącznika usługi AD i łącznika usługi Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Zanotuj istniejące przesłonięcia, które zostały dodane.
   
4. Aby usunąć przesłonięcia zarówno pełnego importu, jak i pełnej synchronizacji na dowolnym łączniku, uruchom następujące polecenie cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Aby usunąć zastąpienia wszystkich łączników, wykonaj następujący skrypt programu PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Aby wznowić działanie usługi Scheduler, uruchom następujące polecenie cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Pamiętaj o wykonaniu wymaganych kroków synchronizacji w najkrótszej wygodie. Można wykonać te czynności ręcznie przy użyciu Synchronization Service Manager lub dodać przesłonięcia przy użyciu polecenia cmdlet Set-ADSyncSchedulerConnectorOverride.

Aby dodać zastąpienia dla pełnego importu i pełnej synchronizacji na dowolnym łączniku, uruchom następujące polecenie cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Poniższa sekcja zawiera informacje dotyczące rozwiązywania problemów i informacji, których można użyć, jeśli wystąpi problem z uaktualnianiem Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Brak błędu łącznika Azure Active Directory podczas uaktualniania Azure AD Connect

Podczas uaktualniania Azure AD Connect z poprzedniej wersji, można napotkać następujący błąd na początku uaktualnienia 

![Błąd](./media/how-to-upgrade-previous-version/error1.png)

Ten błąd występuje, ponieważ łącznik Azure Active Directory o identyfikatorze b891884f-051e-4a83-95af-2544101c9083 nie istnieje w bieżącej konfiguracji Azure AD Connect. Aby sprawdzić, czy jest to przypadek, Otwórz okno programu PowerShell, uruchom polecenie cmdlet. `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Polecenie cmdlet programu PowerShell zgłasza błąd **, którego nie można znaleźć**.

Dzieje się tak dlatego, że Bieżąca konfiguracja Azure AD Connect nie jest obsługiwana w przypadku uaktualniania. 

Jeśli chcesz zainstalować nowszą wersję Azure AD Connect: Zamknij kreatora Azure AD Connect, Odinstaluj istniejące Azure AD Connect i przeprowadź czystą instalację nowszej Azure AD Connect.



## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej [na temat integrowania tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).