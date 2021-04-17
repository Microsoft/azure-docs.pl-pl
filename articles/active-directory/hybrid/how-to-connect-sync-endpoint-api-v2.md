---
title: Azure AD Connect punktu końcowego synchronizacji w wersji 2 | Microsoft Docs
description: Ten dokument zawiera informacje o aktualizacjach interfejsu API punktów końcowych synchronizacji programu Azure AD Connect w wersji 2.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575721"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Interfejs API punktu końcowego synchronizacji programu Azure AD Connect 2 
Firma Microsoft wdrożyła nowy punkt końcowy (API) dla usługi Azure AD Connect który zwiększa wydajność operacji usługi synchronizacji w celu Azure Active Directory. Korzystając z nowego punktu końcowego w wersji 2, można zauważyć zauważalne wzrosty wydajności podczas eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje następujące elementy:
    
 - synchronizowanie grup z maksymalnie 250 tys. elementów członkowskich
 - wzrost wydajności podczas eksportowania i importowania do usługi Azure AD
 
> [!NOTE]
> Obecnie nowy punkt końcowy nie ma skonfigurowanego limitu rozmiaru grupy dla Microsoft 365 zapisywanych wstecz. Może to mieć wpływ na opóźnienia cyklu synchronizacji i usługi Active Directory. Zaleca się przyrostowe zwiększanie rozmiarów grup.  

>[!NOTE]
> Interfejs API punktu końcowego Azure AD Connect synchronizacji w wersji 2 jest obecnie dostępny tylko w tych środowiskach platformy Azure:
> - Komercyjna platforma Azure
> - Chmura Azure w Chinach
> - Chmura platformy Azure dla instytucji rządowych USA Nie będzie dostępna w niemieckiej chmurze platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne  
Aby można było korzystać z nowego punktu końcowego w wersji 2, należy użyć programu Azure AD Connect w wersji [1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej i wykonać poniższe kroki wdrażania, aby włączyć punkt końcowy W2 dla serwera Azure AD Connect.   

## <a name="deployment-guidance"></a>Wskazówki dotyczące wdrażania 
Aby korzystać z punktu końcowego w wersji 2, Azure AD Connect należy wdrożyć program w wersji [1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej. Użyj podanego linku do pobrania. 

Zaleca się stosowanie metody migracji [swing](./how-to-upgrade-previous-version.md#swing-migration) w celu stopniowego stosowania nowego punktu końcowego w środowisku. Zapewni to jasny plan awaryjny w przypadku, gdy konieczne jest główne wycofywanie. W poniższym przykładzie pokazano, jak w tym scenariuszu można użyć migracji swing. Aby uzyskać więcej informacji na temat metody wdrażania migracji swing, skorzystaj z podanego linku. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migracja swing w celu wdrożenia punktu końcowego w wersji 2
Poniższe kroki poprowadzi Cię przez proces wdrażania punktu końcowego w wersji 2 przy użyciu metody swing.

1. Wd wdrażać punkt końcowy w wersji 2 na bieżącym serwerze przejściowym. W poniższych krokach ten serwer będzie **nazywany serwerem w wersji 2.** Bieżący aktywny serwer będzie nadal przetwarzać obciążenie produkcyjne przy użyciu punktu końcowego V1, który będzie nazywany serwerem **V1** poniżej.
1. Sprawdź, czy **serwer w wersji 2** nadal przetwarza importy zgodnie z oczekiwaniami. Na tym etapie duże grupy nie będą aprowizowane w usłudze Azure AD ani w usłudze AD w sieci wirtualnej, ale będzie można sprawdzić, czy uaktualnienie nie miało innego nieoczekiwanego wpływu na istniejący proces synchronizacji. 
2. Po zakończeniu walidacji przełącz serwer **V2** na serwer aktywny, a serwer **V1** na serwer przejściowy. W tej chwili duże grupy, które znajdują się w zakresie do zsynchronizowania, zostaną aprowowane w usłudze Azure AD, a duże grupy ujednolicone usługi Microsoft 365 będą aprowowane w usłudze AD, jeśli włączono funkcję zapisu zwrotnego grup.
3. Sprawdź, czy **serwer w wersji 2** działa i pomyślnie przetwarza duże grupy. Możesz pozostać na tym etapie i monitorować proces synchronizacji przez określony czas.
  >[!NOTE]
  > Jeśli musisz wrócić do poprzedniej konfiguracji, możesz przeprowadzić migrację z serwera **v2** z powrotem do wersji **1**. Ponieważ punkt końcowy w wersji 1 nie obsługuje grup z ponad 50 000 członków, wszystkie duże grupy, które zostały zaaprowizowane przez usługę Azure AD Connect, w usłudze Azure AD lub w usłudze AD w sieci wirtualnej, zostaną następnie usunięte. 
4. Gdy już wiesz, jak korzystać z punktu końcowego w wersji 2, uaktualnij serwer **V1,** aby rozpocząć korzystanie z punktu końcowego w wersji 2. 
 

## <a name="expectations-of-performance-impact"></a>Oczekiwania dotyczące wpływu na wydajność  
W przypadku korzystania z punktu końcowego w wersji 2 wzrost wydajności jest funkcją liczby zsynchronizowanych grup, rozmiaru tych grup i współczynnika zmian grup (działanie wynikające z dodawania i usuwania użytkowników jako członków grupy). Użycie nowego punktu końcowego bez zwiększania liczby, rozmiaru lub współczynnika zmian zsynchronizowanych grup powinno spowodować skrócenie czasu eksportowania i importowania do usługi Azure AD. 
 
Jednak wzrost wydajności można zanegować przez dodatkowe przetwarzanie wymagane podczas synchronizowania dużych grup. Możesz zwiększyć całkowity czas synchronizacji, dodając do procesu synchronizacji zbyt wiele dużych grup.  

Aby lepiej zrozumieć, jak dodanie nowych grup wpłynie na wydajność synchronizacji, zaleca się rozpoczęcie od zsynchronizowania tylko kilku dużych grup z mniej niż 100 000 elementów członkowskich. Następnie można zwiększyć liczbę i rozmiar grup, wprowadzając więcej z nich do zakresu, za pośrednictwem oU, atrybutu lub filtrowania maksymalnego rozmiaru grupy. Ulepszenia wydajności zostaną wprowadzone w zadaniach eksportu i importu dla łącznika usługi Azure AD, a nie dla lokalnego łącznika usługi AD. 

## <a name="deployment-step-by-step"></a>Wdrażanie krok po kroku 
Poniższe trzy fazy to szczegółowe przykłady wdrażania nowego punktu końcowego w wersji 2.  Użyj faz jako wytycznych dla wdrożenia.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Faza 1 — instalowanie i weryfikowanie Azure AD Connect 
Zaleca się, aby najpierw wykonać kroki instalacji lub uaktualnienia do wersji [1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej programu Azure AD Connect i zweryfikowania procesu synchronizacji przed drugą fazą, w której zostanie włączyć punkt końcowy V2. Na Azure AD Connect serwera: 


1. [Opcjonalnie] Wykonywanie kopii zapasowej bazy danych 
2. Zainstaluj program lub [uaktualnij Azure AD Connect wersji 1.5.30.0 lub](https://www.microsoft.com/download/details.aspx?id=47594) nowszej.
3. Sprawdzanie poprawności instalacji 

### <a name="phase-2--enable-the-v2-endpoint"></a>Faza 2 — włączanie punktu końcowego w wersji 2 
Następnym krokiem jest włączenie punktu końcowego w wersji 2. 

> [!NOTE]
> Po włączeniu punktu końcowego w wersji 2 dla serwera będzie można zobaczyć ulepszenia wydajności istniejącego obciążenia. Nie będzie można jeszcze synchronizować grup z więcej niż 50 000 elementów członkowskich. 

Aby przełączyć się do punktu końcowego w wersji 2, należy wykonać następujące czynności: 

1. Otwórz wiersz polecenia programu PowerShell jako administrator. 
2. Wyłącz harmonogram synchronizacji po sprawdzeniu, czy nie są uruchomione żadne operacje synchronizacji: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Zaimportuj nowy moduł: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Przejdź do punktu końcowego w wersji 2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Punkt końcowy w wersji 2 został włączony dla serwera. Poświęć trochę czasu, aby sprawdzić, czy nie ma nieoczekiwanych wyników po włączeniu punktu końcowego w wersji 2, przed przejściem do następnej fazy, w której zwiększysz limit rozmiaru grupy. 
>[!NOTE]
>Ścieżki plików/modułów mogą używać innej litery dysku, w zależności od ścieżki instalacji podanej podczas instalowania Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Faza 3 — zwiększenie limitu członkostwa w grupie 
Po sprawdzeniu, czy usługa działa bez nieoczekiwanych wyników, możesz przejść do podniesienia limitu członkostwa w grupie. Zaleca się, aby najpierw zwiększyć limit członkostwa do nieco wyższej wartości, np. 75 000 członków, aby zobaczyć, jak większe grupy synchronizują się z usługą Azure AD. Gdy wyniki są satysfakcjonują, można dodatkowo zwiększyć limit liczby członków.  

Maksymalny limit wynosi 250 000 członków na grupę. 

Aby zwiększyć limit członkostwa, można wykonać następujące kroki:  

1. Otwieranie edytora reguł synchronizacji usługi Azure AD 
2. W edytorze wybierz pozycję **Ruch wychodzący** dla opcji Kierunek 
3. Kliknij regułę synchronizacji **Out to AAD – Group Join (Wyjść do usługi AAD — dołączenie do** grupy) 
4. Kliknij przycisk **Edytuj Zrzut** ekranu przedstawiający pozycję "Wyświetl reguły synchronizacji i zarządzaj nimi" z wybraną pozycję "Out to AAD - Group Join" (Wyświetl reguły synchronizacji i zarządzaj ![ nimi).](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Kliknij przycisk **Tak,** aby wyłączyć regułę domyślną i utworzyć kopię edytowacyjną.
 ![Zrzut ekranu przedstawiający okno "Edytuj potwierdzenie reguły zarezerwowanej" z wybranym przyciskiem "Tak".](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. W oknie podręcznym  na stronie Opis ustaw pierwszeństwo na dostępną wartość od 1 do 99 Zrzut ekranu przedstawiający okno "Edytowanie reguły synchronizacji ruchu wychodzącego" z wyróżnionem ![ ustawieniem "Pierwszeństwo".](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na **stronie Przekształcenia** zaktualizuj  wartość źródłową przekształcenia członka, zastępując wartość "50000&quot; wartością z między 50001 a 250000.  To zastąpienie zwiększy maksymalny rozmiar członkostwa grup, które będą synchronizowane z usługą Azure AD. Zalecamy rozpoczęcie od liczby 100 000, aby zrozumieć wpływ synchronizacji dużych grup na wydajność synchronizacji. 
 
 **Przykład** 
 
 `IIF((ValueCount(&quot;member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Edytowanie reguły synchronizu](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klikanie pozycji Zapisz. 
10. Otwórz wiersz polecenia programu PowerShell dla administratora 
11. Ponowne włączanie harmonogramu synchronizacji 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Jeśli Azure AD Connect Health nie jest włączona, zmień ustawienia dziennika zdarzeń aplikacji systemu Windows, aby zarchiwizować dzienniki, zamiast ich nadpisać. Dzienniki mogą służyć jako pomoc w przyszłych działaniach związanych z rozwiązywaniem problemów. 

>[!NOTE]
> Po włączeniu nowego punktu końcowego w łączniku usługi AAD mogą pojawić się dodatkowe błędy eksportu o nazwie "dn-attributes-failure". Dla każdego błędu będzie odpowiedni wpis dziennika zdarzeń o identyfikatorze 6949. Błędy są informacyjne i nie wskazują problemu z instalacją, ale raczej że proces synchronizacji nie może dodać niektórych członków do grupy w usłudze Azure AD, ponieważ sam obiekt członkowski nie został zsynchronizowany z usługą Azure AD. 

Nowy kod punktu końcowego w wersji 2 obsługuje niektóre typy błędów eksportu nieco różniące się od kodu w wersji 1.  Podczas korzystania z punktu końcowego w wersji 2 możesz zobaczyć więcej komunikatów o błędach informacyjnych. 

>[!NOTE]
> Podczas uaktualniania Azure AD Connect, upewnij się, że kroki w fazie 2 są ponownie uruchomić, ponieważ zmiany nie są zachowywane w procesie uaktualniania. 

Podczas kolejnych wzrostów limitu członków grupy w regułę synchronizacji Out **to AAD — Group Join** (Wychodzące do usługi AAD — dołączenie do grupy) pełna synchronizacja nie jest konieczna, dlatego można pominąć pełną synchronizację, uruchamiając następujące polecenie w programie PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Jeśli masz Microsoft 365 grupy, które mają więcej niż 50 000 członków, grupy zostaną odczytane do usługi Azure AD Connect, a jeśli włączono funkcję zapisu zwrotnego grup, zostaną one zapisane w lokalnej u usługi AD. 

## <a name="rollback"></a>Wycofywanie 
Jeśli włączono punkt końcowy w wersji 2 i musisz wycofać go, wykonaj następujące kroki: 

1. Na Azure AD Connect serwera: a. [Opcjonalnie] Wykonywanie kopii zapasowej bazy danych 
2. Otwórz administracyjny wiersz polecenia programu PowerShell:
3. Wyłącz harmonogram synchronizacji po sprawdzeniu, czy nie są uruchomione żadne operacje synchronizacji
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Przejdź do punktu końcowego V1 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Otwieranie edytora reguł synchronizacji usługi Azure AD 
6. Usuń edytowacyjną kopię reguły synchronizacji **Out to AAD – Group Join** 
7. Włączanie domyślnej kopii reguły synchronizacji **Zejdą do usługi AAD — dołączenie do** grupy 
8. Otwieranie administracyjnego monitu programu PowerShell 
9. Ponowne włączanie harmonogramu synchronizacji 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Podczas przełączania z powrotem z punktu końcowego w wersji 2 do wersji 1 grupy zsynchronizowane z ponad 50 000 członków zostaną usunięte po uruchomieniu pełnej synchronizacji dla obu grup usługi AD aprowowanych w usłudze Azure AD i Microsoft 365 ujednoliconych grup aprowowanych w usłudze AD. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania  
 
**Kiedy nowy punkt końcowy stanie się domyślnym punktem dla uaktualnień i nowych instalacji?**  
</br>Planujemy opublikowanie nowej wersji usługi AADConnect do pobrania w lutym 2021 r. W tej wersji punkt końcowy w wersji 2 będzie domyślnie używać i umożliwi synchronizowanie grup większych niż 50 000 bez dodatkowej konfiguracji. Ta wersja zostanie następnie opublikowana w celu automatycznego uaktualnienia do kwalifikujących się serwerów.
 
## <a name="next-steps"></a>Następne kroki

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
