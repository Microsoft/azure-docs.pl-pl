---
title: 'Azure AD Connect Sync: zadania operacyjne i zagadnienia | Microsoft Docs'
description: W tym temacie opisano zadania operacyjne dla Azure AD Connect synchronizacji i przygotowania do obsługi tego składnika.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090129"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: przemieszczanie serwera i odzyskiwania po awarii
Gdy serwer jest w trybie przejściowym, można wprowadzić zmiany w konfiguracji i wyświetlić podgląd zmian przed rozpoczęciem aktywności serwera. Umożliwia również uruchomienie pełnego importu i pełnej synchronizacji w celu sprawdzenia, czy wszystkie zmiany są oczekiwane przed wprowadzeniem zmian w środowisku produkcyjnym.

## <a name="staging-mode"></a>Tryb przejściowy
Tryb przejściowy może być używany dla kilku scenariuszy, w tym:

* Wysoka dostępność.
* Testowanie i wdrażanie nowych zmian konfiguracji.
* Wprowadź nowy serwer i likwidowanie Starego.

Podczas instalacji można wybrać serwer, który ma być w **trybie przejściowym**. Ta akcja powoduje, że serwer jest aktywny na potrzeby importowania i synchronizowania, ale nie uruchamia żadnych eksportów. Serwer w trybie przejściowym nie korzysta z synchronizacji haseł lub zapisywania zwrotnego haseł, nawet jeśli podczas instalacji zostały wybrane te funkcje. Po wyłączeniu trybu przejściowego serwer rozpocznie eksportowanie, włącza synchronizację haseł i włącza funkcję zapisywania zwrotnego haseł.

> [!NOTE]
> Załóżmy, że masz Azure AD Connect z włączoną funkcją synchronizacji skrótów haseł. Po włączeniu trybu przejściowego serwer zatrzyma synchronizowanie zmian haseł z lokalnej usługi AD. Po wyłączeniu trybu przejściowego serwer wznawia synchronizowanie zmian haseł z miejsca, w którym zostało ono ostatnio pozostawione. Jeśli serwer pozostanie w trybie przejściowym przez dłuższy czas, może upłynąć trochę czasu, zanim serwer zsynchronizuje wszystkie zmiany haseł, które wystąpiły w danym okresie czasu.
>
>

Nadal można wymusić eksport za pomocą Menedżera usługi synchronizacji.

Serwer w trybie przejściowym nadal otrzymuje zmiany z Active Directory i usługi Azure AD i może szybko przejąć obowiązki innego serwera w przypadku awarii. Jeśli wprowadzisz zmiany w konfiguracji na serwerze podstawowym, możesz wprowadzić te same zmiany na serwerze w trybie przejściowym.

W przypadku osób z wiedzą na temat starszych technologii synchronizacji tryb przejściowy jest inny, ponieważ serwer ma własną bazę danych SQL. Ta architektura umożliwia zlokalizowanie serwera trybu przejściowego w innym centrum danych.

### <a name="verify-the-configuration-of-a-server"></a>Weryfikowanie konfiguracji serwera
Aby zastosować tę metodę, wykonaj następujące kroki:

1. [Przygotowywanie](#prepare)
2. [Konfiguracja](#configuration)
3. [Importowanie i synchronizacja](#import-and-synchronize)
4. [Sprawdź](#verify)
5. [Przełącz aktywny serwer](#switch-active-server)

#### <a name="prepare"></a>Przygotowywanie
1. Zainstaluj Azure AD Connect, wybierz **tryb przejściowy** i usuń zaznaczenie opcji **Rozpocznij synchronizację** na ostatniej stronie Kreatora instalacji. Ten tryb umożliwia ręczne uruchomienie aparatu synchronizacji.
   ![Zrzut ekranu przedstawia stronę gotowy do konfiguracji w oknie dialogowym Azure AD Connect.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Wyloguj się/Zaloguj się i z menu Start wybierz pozycję **usługa synchronizacji**.

#### <a name="configuration"></a>Konfigurowanie
Jeśli wprowadzono niestandardowe zmiany na serwerze podstawowym i chcesz porównać konfigurację z serwerem przemieszczania, użyj [Azure AD Connect documention Configuration](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importowanie i synchronizacja
1. Wybierz pozycję **Łączniki** i wybierz pierwszy łącznik z typem **Active Directory Domain Services**. Kliknij przycisk **Uruchom**, wybierz pozycję **pełny import** i przycisk **OK**. Wykonaj te kroki dla wszystkich łączników tego typu.
2. Wybierz łącznik z typem **Azure Active Directory (Microsoft)**. Kliknij przycisk **Uruchom**, wybierz pozycję **pełny import** i przycisk **OK**.
3. Upewnij się, że łączniki kart są nadal zaznaczone. Dla każdego łącznika z typem **Active Directory Domain Services**, kliknij przycisk **Uruchom**, wybierz pozycję **Synchronizacja różnicowa** i **przycisk OK**.
4. Wybierz łącznik z typem **Azure Active Directory (Microsoft)**. Kliknij przycisk **Uruchom**, wybierz pozycję **Synchronizacja różnicowa** i przycisk **OK**.

Teraz przygotowano zmiany w usłudze Azure AD i lokalnej usłudze AD (Jeśli korzystasz z wdrażania hybrydowego programu Exchange). Następne kroki umożliwiają sprawdzenie, co się zmieniło przed faktycznym rozpoczęciem eksportowania do katalogów.

#### <a name="verify"></a>Weryfikacja
1. Uruchom wiersz polecenia cmd i przejdź do `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Uruchom: `csexport "Name of Connector" %temp%\export.xml /f:x` nazwę łącznika można znaleźć w usłudze synchronizacji. Ma nazwę podobną do "contoso.com – Azure AD" dla usługi Azure AD.
3. Uruchom: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` masz plik w katalogu% Temp% o nazwie export.csv, który można sprawdzić w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają zostać wyeksportowane.
4. Wprowadź niezbędne zmiany w danych lub konfiguracji, a następnie ponownie wykonaj te kroki (Zaimportuj i zsynchronizuj), dopóki nie zostaną oczekiwane zmiany, które mają zostać wyeksportowane.

**Informacje o pliku export.csv** Większość plików nie wymaga wyjaśnień. Niektóre skróty do zrozumienia zawartości:
* OMODT — typ modyfikacji obiektu. Wskazuje, czy operacja na poziomie obiektu jest dodawana, aktualizowana lub usuwana.
* AMODT — typ modyfikacji atrybutu. Wskazuje, czy operacja na poziomie atrybutu jest dodawana, aktualizowana lub usuwana.

**Pobierz typowe identyfikatory** Plik export.csv zawiera wszystkie zmiany, które mają zostać wyeksportowane. Każdy wiersz odpowiada zmianie obiektu w miejscu łącznika, a obiekt jest identyfikowany przez atrybut nazwy wyróżniającej. Atrybut nazwy wyróżniającej jest unikatowym identyfikatorem przypisanym do obiektu w obszarze łącznika. Jeśli masz wiele wierszy/zmian w export.csv do przeanalizowania, może być trudne, aby ustalić, które obiekty są zmieniane na podstawie samego atrybutu nazwy wyróżniającej. Aby uprościć proces analizowania zmian, Użyj skryptu programu csanalyzer.ps1 PowerShell. Skrypt pobiera typowe identyfikatory (na przykład displayName, userPrincipalName) obiektów. Aby użyć skryptu:
1. Skopiuj skrypt programu PowerShell z sekcji [CSAnalyzer](#appendix-csanalyzer) do pliku o nazwie `csanalyzer.ps1` .
2. Otwórz okno programu PowerShell i przejdź do folderu, w którym został utworzony skrypt programu PowerShell.
3. Uruchom polecenie `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Masz teraz plik o nazwie **processedusers1.csv** , który można sprawdzić w programie Microsoft Excel. Należy zauważyć, że plik udostępnia mapowanie z atrybutu DN do wspólnych identyfikatorów (na przykład displayName i userPrincipalName). Obecnie nie obejmuje to faktycznych zmian atrybutów, które mają zostać wyeksportowane.

#### <a name="switch-active-server"></a>Przełącz aktywny serwer
1. Na aktualnie aktywnym serwerze Wyłącz serwer (DirSync/FIM/Azure AD Sync), aby nie eksportować go do usługi Azure AD lub ustawić go w trybie przejściowym (Azure AD Connect).
2. Uruchom Kreatora instalacji na serwerze w **trybie przejściowym** i Wyłącz **tryb przejściowy**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Częścią projektu implementacji jest zaplanowanie, co należy zrobić w przypadku awarii, w której utracisz serwer synchronizacji. Istnieją różne modele, z których jeden ma być używany, zależy od kilku czynników, takich jak:

* Jaka jest tolerancja nie można wprowadzać zmian w obiektach w usłudze Azure AD podczas przestoju?
* Jeśli używasz synchronizacji haseł, czy użytkownicy akceptują, że muszą używać starego hasła w usłudze Azure AD na wypadek, gdyby zmieniły się on lokalnie?
* Czy masz zależność od operacji wykonywanych w czasie rzeczywistym, takich jak zapisywanie zwrotne haseł?

W zależności od odpowiedzi na te pytania i zasady organizacji można zaimplementować jedną z następujących strategii:

* Kompiluj ponownie, gdy jest to wymaganie.
* Ma serwer zapasowy rezerwowy, nazywany **trybem przejściowym**.
* Użyj maszyn wirtualnych.

Jeśli nie używasz wbudowanej bazy danych SQL Express, należy również zapoznać się z sekcją [wysokiej dostępności SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Kompiluj ponownie w razie konieczności
Strategiczna strategia polega na zaplanowaniu odbudowy serwera w razie potrzeby. Zazwyczaj zainstalowanie aparatu synchronizacji i wykonanie wstępnego importu i synchronizacji może zakończyć się w ciągu kilku godzin. Jeśli nie jest dostępny serwer zapasowy, można tymczasowo użyć kontrolera domeny do hostowania aparatu synchronizacji.

Serwer aparatu synchronizacji nie przechowuje żadnego stanu dotyczącego obiektów, dzięki czemu baza danych może zostać odbudowana na podstawie danych z Active Directory i usługi Azure AD. Atrybut **sourceAnchor** służy do przyłączania obiektów z lokalizacji lokalnej i w chmurze. W przypadku odbudowania serwera z istniejącymi obiektami w środowisku lokalnym i w chmurze, aparat synchronizacji dopasowuje te obiekty ponownie w przypadku ponownej instalacji. Elementy potrzebne do dokumentowania i zapisania to zmiany konfiguracji wprowadzone na serwerze, takie jak filtrowanie i reguły synchronizacji. Te konfiguracje niestandardowe należy zastosować ponownie przed rozpoczęciem synchronizowania.

### <a name="have-a-spare-standby-server---staging-mode"></a>Posiadanie zapasowego serwera rezerwy w trybie przejściowym
Jeśli masz bardziej złożone środowisko, będzie zalecane posiadanie co najmniej jednego serwera rezerwowego. Podczas instalacji można włączyć **tryb przejściowy** serwera.

Aby uzyskać więcej informacji, zobacz [tryb przejściowy](#staging-mode).

### <a name="use-virtual-machines"></a>Korzystanie z maszyn wirtualnych
Typową i obsługiwaną metodą jest uruchomienie aparatu synchronizacji w maszynie wirtualnej. Jeśli na hoście wystąpił problem, obraz z serwerem aparatu synchronizacji można migrować na inny serwer.

### <a name="sql-high-availability"></a>Wysoka dostępność SQL
Jeśli nie używasz SQL Server Express, który jest dostarczany z Azure AD Connect, należy rozważyć wysoką dostępność dla SQL Server. Obsługiwane rozwiązania wysokiej dostępności obejmują klastrowanie SQL i AOA (zawsze włączone grupy dostępności). Nieobsługiwane rozwiązania obejmują dublowanie.

Obsługa usługi SQL AOA została dodana do Azure AD Connect w wersji 1.1.524.0. Przed zainstalowaniem Azure AD Connect należy włączyć funkcję SQL AOA. Podczas instalacji program Azure AD Connect wykrywa, czy podane wystąpienie SQL jest włączone dla usługi SQL AOA, czy nie. Jeśli jest włączona funkcja SQL AOA, Azure AD Connect więcej, jeśli SQL AOA jest skonfigurowany do korzystania z replikacji synchronicznej lub asynchronicznej. Podczas konfigurowania odbiornika grupy dostępności zaleca się ustawienie wartości 0 dla właściwości RegisterAllProvidersIP. Dzieje się tak, ponieważ Azure AD Connect obecnie używa SQL Native Client do nawiązywania połączenia z serwerem SQL, a SQL Native Client nie obsługuje użycia właściwości MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Dodatek CSAnalyzer
Zapoznaj się z sekcją [Sprawdzanie](#verify) , jak korzystać z tego skryptu.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**  

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)  
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)  
