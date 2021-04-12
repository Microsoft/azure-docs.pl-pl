---
title: Azure AD Connect synchronizacji punktu końcowego v2 | Microsoft Docs
description: W tym dokumencie opisano aktualizacje interfejsu API programu Azure AD Connect Sync w wersji 2 punktów końcowych.
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
ms.openlocfilehash: 0ecfd277f2cc86102d59b201e7b43fa8519bdd3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937609"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Interfejs API punktu końcowego synchronizacji programu Azure AD Connect 2 
Firma Microsoft wdrożyła nowy punkt końcowy (API) dla Azure AD Connect, który poprawia wydajność operacji usługi synchronizacji do Azure Active Directory. Korzystając z nowego punktu końcowego w wersji 2, zobaczysz zauważalny wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje następujące elementy:
    
 - Synchronizowanie grup z elementami członkowskimi do 250 000
 - wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD
 
> [!NOTE]
> Obecnie nowy punkt końcowy nie ma skonfigurowanego limitu rozmiaru grupy dla grup Microsoft 365, które są zapisywane z powrotem. Może to mieć wpływ na Active Directory i opóźnienia cyklu synchronizacji. Zaleca się przyrostowe zwiększenie rozmiarów grup.  

## <a name="prerequisites"></a>Wymagania wstępne  
Aby można było korzystać z nowego punktu końcowego v2, należy użyć Azure AD Connect w [wersji 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej i postępować zgodnie z poniższymi instrukcjami wdrażania, aby włączyć punkt końcowy V2 dla Azure AD Connect serwera.   

## <a name="deployment-guidance"></a>Wskazówki dotyczące wdrażania 
Musisz wdrożyć [Azure AD Connect w wersji 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej, aby użyć punktu końcowego v2. Użyj linku podanego do pobrania. 

Zaleca się, aby postępować zgodnie z metodą [migracji wahadłowej](./how-to-upgrade-previous-version.md#swing-migration) w celu wdrażania nowego punktu końcowego w środowisku. Dzięki temu będzie można jasno zaplanować sytuacje awaryjne w przypadku, gdy konieczne jest poważne wycofanie. Poniższy przykład ilustruje, jak w tym scenariuszu może być używana migracja wahadła. Aby uzyskać więcej informacji na temat metody wdrożenia migracji na zasięg, zapoznaj się z udostępnionym linkiem. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migracja na zasięg dla wdrożenia punktu końcowego v2
Poniższe kroki przeprowadzą Cię przez proces wdrażania punktu końcowego v2 przy użyciu metody wahadłowej.

1. Wdróż punkt końcowy V2 na bieżącym serwerze tymczasowym. Ten serwer będzie znany jako **serwer v2** w poniższych krokach. Bieżący aktywny serwer będzie kontynuował przetwarzanie obciążenia produkcyjnego przy użyciu punktu końcowego V1, który zostanie wywołany z **serwerem V1** poniżej.
1. Sprawdź, czy **serwer v2** nadal przetwarza Importy zgodnie z oczekiwaniami. Na tym etapie duże grupy nie będą obsługiwane w usłudze Azure AD ani w usłudze AD Premium, ale będzie można sprawdzić, czy uaktualnienie nie spowodowało żadnego innego nieoczekiwanego wpływu na istniejący proces synchronizacji. 
2. Po zakończeniu walidacji Przełącz serwer w **wersji 2** na serwer, który będzie serwerem aktywnym i **serwerem V1** . W tej chwili duże grupy, które znajdują się w zakresie synchronizacji, będą obsługiwane do usługi Azure AD, a także duże Microsoft 365 ujednolicone grupy będą obsługiwane w usłudze AD, jeśli włączono funkcję zapisywania zwrotnego grup.
3. Sprawdź, czy **serwer v2** działa i czy przetwarzanie dużych grup powiodło się. Możesz pozostawać w tym kroku i monitorować proces synchronizacji przez okres.
  >[!NOTE]
  > Jeśli musisz przejść z powrotem do poprzedniej konfiguracji, możesz przeprowadzić przechodzenie między **serwerami z wersji 2** do wersji **1**. Ponieważ punkt końcowy V1 nie obsługuje grup mających ponad 50 000 członków, każda duża grupa, która została zainicjowana przez Azure AD Connect w usłudze Azure AD lub w usłudze Premium AD, zostanie następnie usunięta. 
4. Jeśli masz pewność, że korzystasz z punktu końcowego v2, Uaktualnij **serwer V1** , aby rozpocząć korzystanie z punktu końcowego v2. 
 

## <a name="expectations-of-performance-impact"></a>Oczekiwania na wpływ na wydajność  
W przypadku korzystania z punktu końcowego v2 zyski wydajności są funkcją liczby zsynchronizowanych grup, rozmiaru tych grup i zmian grup (działanie wynikające z dodawania i usuwania użytkowników jako członków grupy). Korzystając z nowego punktu końcowego, bez zwiększania liczby, rozmiaru lub zmiany zsynchronizowanych grup, należy skrócić czas eksportowania i importowania do usługi Azure AD. 
 
Jednak wzrost wydajności może być spowodowany przez dodatkowe przetwarzanie wymagane podczas synchronizacji dużych grup. Można zwiększyć całkowity czas synchronizacji, dodając zbyt wiele dużych grup do procesu synchronizacji.  

Aby lepiej zrozumieć, jak dodanie nowych grup będzie miało wpływ na wydajność synchronizacji, zaleca się rozpoczęcie od synchronizacji tylko kilku dużych grup, których elementy są mniejsze niż 100 000. Następnie można zwiększyć liczbę i rozmiar grup, przenosząc im więcej z nich w zakresie, za pomocą jednostki organizacyjnej, atrybutu lub maksymalnego filtrowania rozmiaru grupy. Ulepszenia wydajności zostaną zrealizowane na zadaniach eksportu i importu dla łącznika usługi Azure AD, a nie do lokalnego łącznika usługi AD. 

## <a name="deployment-step-by-step"></a>Wdrożenie krok po kroku 
Poniższe trzy fazy są szczegółowym przykładem wdrażania nowego punktu końcowego w wersji 2.  Użyj faz jako wskazówki dla wdrożenia.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Faza 1 — Instalowanie i weryfikowanie Azure AD Connect 
Zalecane jest, aby najpierw wykonać kroki instalacji lub uaktualnienia do [Azure AD Connect w wersji 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej i zweryfikować proces synchronizacji przed przejściem do drugiej fazy, w której zostanie włączony punkt końcowy w wersji 2. Na serwerze Azure AD Connect: 


1. Obowiązkowe Utwórz kopię zapasową bazy danych 
2. Zainstaluj lub Uaktualnij program [Azure AD Connect do wersji 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) lub nowszej.
3. Sprawdzanie poprawności instalacji 

### <a name="phase-2--enable-the-v2-endpoint"></a>Faza 2 — Włączanie punktu końcowego v2 
Następnym krokiem jest włączenie punktu końcowego v2. 

> [!NOTE]
> Po włączeniu punktu końcowego v2 dla serwera będzie można zobaczyć pewne ulepszenia wydajności dla istniejącego obciążenia. Nie będzie jeszcze możliwe synchronizowanie grup o większej liczbie członków 50 000. 

Aby przełączyć się do punktu końcowego v2, wykonaj następujące czynności: 

1. Otwórz wiersz polecenia programu PowerShell jako administrator. 
2. Wyłącz harmonogram synchronizacji po sprawdzeniu, czy nie są uruchomione żadne operacje synchronizacji: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Zaimportuj nowy moduł: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Przejdź do punktu końcowego v2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Punkt końcowy w wersji 2 jest teraz włączony dla serwera. Poświęć trochę czasu, aby upewnić się, że nie ma żadnych nieoczekiwanych wyników po włączeniu punktu końcowego v2 przed przejściem do następnej fazy, w której zostanie zwiększony limit rozmiaru grupy. 
>[!NOTE]
>Ścieżki plików/modułów mogą używać innej litery dysku w zależności od ścieżki instalacji podanej podczas instalowania Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Faza 3 — zwiększenie limitu członkostwa w grupie 
Po sprawdzeniu, że usługa jest uruchomiona bez nieoczekiwanych wyników, można przystępować do wywoływania limitu członkostwa w grupie. Zalecane jest, aby najpierw podnieść limit członkostwa do nieco wyższej wartości, e g. 75K członków, aby zobaczyć większe grupy synchronizowane z usługą Azure AD. Po uzyskaniu zadowalającego rezultatu możesz podnieść limit elementów członkowskich.  

Maksymalny limit wynosi 250 000 członków na grupę. 

Aby zwiększyć limit członkostwa, można użyć następujących kroków:  

1. Otwórz Edytor reguł synchronizacji usługi Azure AD 
2. W edytorze wybierz pozycję **wychodzące** dla kierunku 
3. Kliknij regułę synchronizacji **dołączania do usługi AAD — Grupa** 
4. Kliknij zrzut ekranu przycisku **edycji** ![ , na którym jest zaznaczona wartość "Wyświetl i Zarządzaj regułami synchronizacji" z opcją "out do AAD-Group Join".](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Kliknij przycisk **tak** , aby wyłączyć regułę domyślną i utworzyć kopię, którą można edytować.
 ![Zrzut ekranu przedstawiający okno "Edytowanie potwierdzenia reguły zastrzeżonej" z wybranym przyciskiem "tak".](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. W oknie podręcznym na stronie **Opis** Ustaw dla pierwszeństwa wartość dostępną od 1 do 99 ![ zrzutu ekranu, który pokazuje okno "Edytowanie reguły synchronizacji danych wychodzących" z wyróżnioną opcją "pierwszeństwo".](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na stronie **przekształcenia** zaktualizuj wartość **źródłową** przekształcenia **elementu członkowskiego** , zastępując wartość "50000&quot; wartością z zakresu od 50001 do 250000. To zastąpienie spowoduje zwiększenie maksymalnego rozmiaru członkostwa grup, które będą synchronizowane z usługą Azure AD. Zalecamy rozpoczęcie od kilku 100 000ów, aby zrozumieć wpływ synchronizacji dużych grup na wydajność synchronizowania. 
 
 **Przykład** 
 
 `IIF((ValueCount(&quot;member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Edytuj regułę synchronizacji](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klikanie pozycji Zapisz. 
10. Otwórz wiersz administracyjny programu PowerShell 
11. Włącz ponownie harmonogram synchronizacji 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Jeśli Azure AD Connect Health nie jest włączona, Zmień ustawienia dziennika zdarzeń aplikacji systemu Windows w celu archiwizacji dzienników zamiast zastępowania ich. Dzienniki mogą służyć do ułatwienia rozwiązywania problemów w przyszłości. 

>[!NOTE]
> Po włączeniu nowego punktu końcowego mogą pojawić się dodatkowe błędy eksportu łącznika usługi AAD o nazwie "DN-Attributes-Failure". Istnieje odpowiedni wpis dziennika zdarzeń dla każdego błędu o identyfikatorze 6949. Błędy są informacyjne i nie wskazują problemu z instalacją, ale zamiast tego proces synchronizacji nie mógł dodać niektórych członków do grupy w usłudze Azure AD, ponieważ sam obiekt członkowski nie został zsynchronizowany z usługą Azure AD. 

Nowy kod punktu końcowego v2 obsługuje niektóre typy błędów eksportu nieco inaczej niż w kodzie v1.  W przypadku korzystania z punktu końcowego v2 mogą pojawić się więcej informacji o komunikatach o błędach informacyjnych. 

>[!NOTE]
> Podczas uaktualniania Azure AD Connect upewnij się, że kroki w fazie 2 są ponownie uruchamiane, ponieważ zmiany nie są zachowywane w procesie uaktualniania. 

Podczas kolejnej podwyżki do limitu elementu członkowskiego grupy w regule synchronizacji **poza grupą w usłudze AAD —** pełna synchronizacja nie jest konieczna, dlatego można wybrać opcję pomijania pełnej synchronizacji, uruchamiając następujące polecenie w programie PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Jeśli masz Microsoft 365 ujednoliconych grup, które mają więcej niż 50 000 członków, grupy zostaną odczytane do Azure AD Connect i jeśli włączono funkcję zapisywania zwrotnego grup, zostaną one zapisaną w lokalnej usłudze AD. 

## <a name="rollback"></a>Wycofywanie 
Jeśli włączono punkt końcowy w wersji 2 i konieczne jest wycofanie, wykonaj następujące kroki: 

1. Na serwerze Azure AD Connect: a. Obowiązkowe Utwórz kopię zapasową bazy danych 
2. Otwórz wiersz polecenia administratora programu PowerShell:
3. Wyłącz harmonogram synchronizacji po sprawdzeniu, czy nie są uruchomione żadne operacje synchronizacji
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Przejdź do punktu końcowego v1 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Otwórz Edytor reguł synchronizacji usługi Azure AD 
6. Usuń edytowalną kopię reguły synchronizacji **dołączania do usługi AAD — Grupa** 
7. Włącz domyślną kopię reguły synchronizacji **połączeń poza do usługi AAD — Grupa** 
8. Otwieranie monitu administratora programu PowerShell 
9. Włącz ponownie harmonogram synchronizacji 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Po przełączeniu z powrotem z punktów końcowych z przedziału od 2 do 1 grupy synchronizowane z więcej niż 50 000 członkami zostaną usunięte po uruchomieniu pełnej synchronizacji dla grup usługi AD, które są udostępnione w usłudze Azure AD, i Microsoft 365 ujednoliconych grup zainicjowanych do usługi AD. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania  
 
**Kiedy nowy punkt końcowy stanie się domyślny dla uaktualnień i nowych instalacji?**  
</br>Planujemy opublikowanie nowej wersji AADConnect do pobrania w lutym 2021. Ta wersja domyślnie użyje punktu końcowego v2 i włączy synchronizowanie grup większe niż 50 000 bez żadnej dodatkowej konfiguracji. Ta wersja zostanie później opublikowana w celu przeprowadzenia uaktualnienia do odpowiednich serwerów.
 
## <a name="next-steps"></a>Następne kroki

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
