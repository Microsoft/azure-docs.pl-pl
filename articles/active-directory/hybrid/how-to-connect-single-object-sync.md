---
title: 'Azure AD Connect synchronizacji pojedynczego obiektu '
description: Dowiedz się, jak synchronizować jeden obiekt z Active Directory do usługi Azure AD w celu rozwiązywania problemów.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726095"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect synchronizacji pojedynczego obiektu 

Azure AD Connect narzędzia synchronizacji pojedynczego obiektu to polecenie cmdlet programu PowerShell, które może służyć do synchronizowania poszczególnych obiektów z Active Directory do Azure Active Directory. Wygenerowany raport może służyć do badania problemów związanych z synchronizacją obiektów i ich rozwiązywania. 

> [!NOTE]
> Narzędzie obsługuje synchronizację z Active Directory Azure Active Directory. Nie obsługuje synchronizacji z Azure Active Directory do Active Directory. 
>
> Narzędzie obsługuje synchronizowanie modyfikacji obiektów i aktualizowanie ich. Nie obsługuje synchronizowania modyfikacji obiektu Delete. 

## <a name="how-it-works"></a>Jak to działa
Narzędzie synchronizacji pojedynczego obiektu wymaga Active Directory nazwy wyróżniającej jako danych wejściowych do znalezienia łącznika źródłowego i partycji do zaimportowania. Eksportuje zmiany do Azure Active Directory. Narzędzie generuje dane wyjściowe JSON podobne do typu zasobu **provisioningObjectSummary** . 

Narzędzie synchronizacji pojedynczego obiektu wykonuje następujące czynności: 

 1. Ustal, czy domena obiektu (źródła) (Active Directory łącznik i partycja) w zakresie synchronizacji. 
 2. Ustal, czy domena obiektu (target) (Azure Active Directory łącznik i partycja) w zakresie synchronizacji. 
 3. Ustal, czy jednostka organizacyjna obiektu jest w zakresie synchronizacji. 
 4. Ustal, czy obiekt jest dostępny przy użyciu poświadczeń konta łącznika. 
 5. Ustal, czy typ obiektu jest w zakresie synchronizacji. 
 6. Ustal, czy obiekt jest w zakresie synchronizacji, jeśli włączono filtrowanie grup. 
 7. Importuj obiekt z Active Directory do Active Directory obszaru łącznika. 
 8. Importuj obiekt z Azure Active Directory do Azure Active Directory obszaru łącznika. 
 9. Synchronizuj obiekt z Active Directory przestrzeni łącznika. 
 10. Eksportuj obiekt z Azure Active Directory miejsca łącznika do Azure Active Directory. 

Oprócz danych wyjściowych JSON narzędzie generuje raport HTML, który zawiera wszystkie szczegóły operacji synchronizacji. Raport HTML znajduje się w **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm**. Ten raport HTML może być współużytkowany z zespołem pomocy technicznej w celu przeprowadzenia dalszej procedury rozwiązywania problemów, jeśli jest to konieczne. 

Raport HTML zawiera następujące elementy: 

|Tab|Opis|
|-----|-----|
|Kroki|Przedstawia kroki podejmowane w celu zsynchronizowania obiektu. Każdy krok zawiera szczegółowe informacje dotyczące rozwiązywania problemów. Kroki importu, synchronizacji i eksportu zawierają dodatkowe informacje o atrybutach, takie jak nazwa, to wiele wartości, typ, wartość, Dodawanie wartości, usuwanie wartości, operacja, reguła synchronizacji, typ mapowania i źródło danych.| 
|Rozwiązywanie problemów & zalecenia|Zawiera kod błędu i przyczynę. Informacje o błędzie są dostępne tylko wtedy, gdy wystąpi awaria.| 
|Zmodyfikowane właściwości|Pokazuje starą wartość i nową wartość. Jeśli nie ma starej wartości lub jeśli nowa wartość zostanie usunięta, ta komórka jest pusta. W przypadku atrybutów wielowartościowych wyświetlana jest liczba. Nazwa atrybutu jest łączem do karty kroki: Eksportuj obiekt z Azure Active Directory miejsca łącznika do Azure Active Directory: informacje o atrybutach, które zawierają dodatkowe szczegóły dotyczące atrybutu, takie jak nazwa, to wiele wartości, typ, wartość, Dodawanie wartości, usuwanie wartości, operacja, reguła synchronizacji, typ mapowania i źródło danych.| 
|Podsumowanie|Zawiera przegląd informacji o tym, co się stało i identyfikatory dla obiektu w systemie źródłowym i docelowym.| 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby można było korzystać z narzędzia synchronizacji jednego obiektu, należy użyć wersji 2021 marca Azure AD Connect lub nowszej. 

### <a name="run-the-single-object-sync-tool"></a>Uruchamianie narzędzia synchronizacji pojedynczego obiektu 

Aby uruchomić narzędzie synchronizacji pojedynczego obiektu, wykonaj następujące czynności: 

 1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji Uruchom jako administrator. 

 2. Ustaw [zasady wykonywania](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) na RemoteSigned lub nieograniczone. 

 3. Wyłącz harmonogram synchronizacji po sprawdzeniu, czy nie są uruchomione żadne operacje synchronizacji. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importowanie modułu diagnostyki AdSync 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Wywołaj polecenie cmdlet synchronizacji pojedynczego obiektu. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Włącz ponownie harmonogram synchronizacji. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Parametry wejściowe synchronizacji pojedynczego obiektu|Opis| 
|-----|----|
|DistinguishedName|Jest to wymagany parametr ciągu. </br></br>Jest to nazwa wyróżniająca obiektu Active Directory, która wymaga synchronizacji i rozwiązywania problemów.| 
|StagingMode|Jest to opcjonalny parametr przełącznika.</br></br>Tego parametru można użyć, aby uniemożliwić eksportowanie zmian do Azure Active Directory.</br></br>**Uwaga**: polecenie cmdlet spowoduje zatwierdzenie operacji synchronizacji. </br></br>**Uwaga**: serwer przemieszczania Azure AD Connect nie eksportuje zmian do Azure Active Directory.|
|NoHtmlReport|Jest to opcjonalny parametr przełącznika.</br></br>Tego parametru można użyć, aby zapobiec generowaniu raportu w formacie HTML. 

## <a name="single-object-sync-throttling"></a>Ograniczanie synchronizacji pojedynczego obiektu 

Narzędzie synchronizacji pojedynczego obiektu **jest** przeznaczone do badania i rozwiązywania problemów związanych z problemami z synchronizacją obiektów. **Nie** jest przeznaczone do zastępowania cyklu synchronizacji wykonywanego przez harmonogram. Import z Azure Active Directory i eksport do Azure Active Directory podlega ograniczeniom ograniczającym. Spróbuj ponownie za 5 minut, jeśli osiągniesz limit ograniczania. 

## <a name="next-steps"></a>Następne kroki
- [Rozwiązywanie problemów z synchronizacją obiektów](tshoot-connect-objectsync.md)
- [Rozwiązywanie problemów z niesynchronizacją obiektu](tshoot-connect-object-not-syncing.md)
- [Kompleksowe Rozwiązywanie problemów dotyczących Azure AD Connect obiektów i atrybutów](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
