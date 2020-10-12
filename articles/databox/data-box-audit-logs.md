---
title: Inspekcja dzienników dla Azure Data Box, zdarzeń Azure Data Box Heavy | Microsoft Docs
description: Zawiera opis pełnych dzienników inspekcji dla urządzenie Data Box zbieranych na różnych etapach Azure Data Box i kolejności Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209977"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Dzienniki inspekcji dla Azure Data Box i Azure Data Box Heavy

Dzienniki są niezmienne, czyli sygnatury czasowe zdarzeń dyskretnych, które wystąpiły w czasie. Dzienniki zawierają informacje diagnostyczne, inspekcji i zabezpieczeń z urządzenia.  

Urządzenie Data Box lub Data Box Heavy kolejność przechodzi przez następujące kroki w trakcie jego działania: kolejność, konfiguracja, kopiowanie danych, zwracanie, przekazywanie na platformę Azure oraz sprawdzanie i wymazywanie danych. Dla każdego z tych kroków wszystkie zdarzenia są poddawane inspekcji i rejestrowane.

Ten artykuł zawiera informacje na temat dzienników inspekcji urządzenie Data Box, w tym typów dzienników i zbieranych informacji oraz lokalizacji dzienników. 

Informacje przedstawione w tym artykule dotyczą obu programów, urządzenie Data Box i Data Box Heavy. W kolejnych sekcjach wszystkie odwołania do urządzenie Data Box dotyczą również Data Box Heavy. Dzienniki zebrane z usługi urządzenie Data Box działającej na platformie Azure nie zostały omówione w tym artykule. 


## <a name="about-audit-logs"></a>Informacje o dziennikach inspekcji 

Na urządzenie Data Box są zbierane następujące dzienniki:

- **Dzienniki systemu** — urządzenie Data Box to urządzenie z systemem Windows, wszystkie zdarzenia dotyczące sprzętu, oprogramowania i systemu są rejestrowane. Zestaw tych zdarzeń jest zbierany i raportowany w dziennikach inspekcji systemu. 

- **Zabezpieczenia** — urządzenie Data Box to urządzenie z systemem Windows, wszystkie zdarzenia zabezpieczeń są rejestrowane. Zestaw tych zdarzeń jest zbierany i raportowany w dziennikach inspekcji zabezpieczeń. 

- **Aplikacja** — te dzienniki są specyficzne tylko dla urządzenie Data Box. Te dzienniki zawierają wszystkie zdarzenia wygenerowane na urządzeniu w odpowiedzi na usługi urządzenie Data Box, które są uruchomione.

Każdy z tych dzienników został omówiony w poniższej sekcji.

### <a name="system-logs"></a>Dzienniki systemu

Następujące identyfikatory zdarzeń dziennika systemu są zbierane jako dzienniki inspekcji systemu na urządzenie Data Box:

|Nazwa dostawcy zdarzeń     |Zebrany identyfikator zdarzenia   |Opis zdarzenia   |
|-------------------|----------|----------------|
|Microsoft-Windows-Kernel-General|12  |Czas UTC, gdy system operacyjny został uruchomiony ponownie.   |
|                                |13  |Czas UTC zamknięcia systemu operacyjnego. |
|    |                              |
|Microsoft-Windows-jądro  |41  |System został uruchomiony ponownie bez czystego zamknięcia.| 
|    |                              |
|Microsoft-Windows-BitLocker-sterownik|Wszystkie|    |

### <a name="security-logs"></a>Dzienniki zabezpieczeń

Następujące identyfikatory zdarzeń dzienników zabezpieczeń są zbierane jako dzienniki inspekcji zabezpieczeń na urządzenie Data Box:

|Nazwa dostawcy zdarzeń                   |Zebrany identyfikator zdarzenia    |Opis zdarzenia       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-zabezpieczenia-Inspekcja   |4624        |Pomyślne logowanie. |
|                                      |4625        |Logowanie do konta nie powiodło się. Nieznana nazwa użytkownika lub nieprawidłowe hasło. |
|                                     

### <a name="application-logs"></a>Dzienniki aplikacji

Następujące identyfikatory zdarzeń dzienników aplikacji są zbierane jako część dzienników inspekcji pakietu na urządzenie Data Box.     

- **Microsoft-Azure-DataBox-OOBE-Audit** -zawiera zdarzenia występujące w lokalnym interfejsie użytkownika. 
- **Microsoft-Azure-DataBox-reaprowizacji-Audit** -zawiera zdarzenia dotyczące ponownego inicjowania obsługi urządzenia urządzenie Data Box. Ponowne Inicjowanie obsługi urządzenie Data Box występuje, gdy urządzenie zostanie zresetowane za pomocą lokalnego interfejsu użytkownika. Należy wybrać tę opcję, jeśli chcesz wymazać skopiowane dane, usuwając istniejące udziały i ponownie tworząc udziały w ramach ponownego inicjowania obsługi administracyjnej lub resetowania urządzenia.
- **Microsoft-Azure-DataBox-HcsMgmt-Audit** -zawiera zdarzenia związane tylko z etapem **przygotowywania do wysłania** , zanim urządzenie zostanie wysłane z powrotem do centrum danych platformy Azure. 
- **Microsoft-Azure-DataBox-IfxAudit** — zawiera komunikaty zarejestrowane przez różne jednostki produktu o zadaniach, dzienniki wskazujące więcej informacji na temat tego, co się dzieje w niektórych przepływach.

Poniżej przedstawiono tabelę zawierającą podsumowanie różnych dostawców zdarzeń i identyfikatory zdarzeń, które są zbierane w każdym przypadku.

|Nazwa dostawcy zdarzeń    |Identyfikator zdarzenia    | Uwagi |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-Audit |4624        |Pomyślne logowanie.|
|                                      |4625        |Logowanie do konta nie powiodło się. Nieznana nazwa użytkownika lub nieprawidłowe hasło.|
|                                     |4634        |Wyloguj zdarzenie.|
|                                   |  | |
|Microsoft-Azure-DataBox-rezastrzeganie-Audit    |65001       |Pomyślne ponowne Inicjowanie obsługi zdarzenia.|
|                                                  |65002       |Nie można ponownie zainicjować zdarzenia.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-Audit        |65003       |NotStarted stanu zdarzenia przygotowanie do wysłania, InProgress, niepomyślne, anulowane, zakończone powodzeniem, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |Wszystkie |Wszystkie zdarzenia są rejestrowane za pomocą interfejsu API dziennika inspekcji w kodzie |

Oto przykład dziennika inspekcji struktury Instrumentacji (IFX):

|     Zadanie/zadanie/interfejs API                              |     Zarejestrowane zdarzenia                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Czyszczenie                                   |     Zdarzenia związane z uruchamianiem, ukończeniem lub niepowodzeniem zadania oczyszczania są rejestrowane. |                                              
|     Przygotowywanie urządzenia do wysłania przez klienta    |     Rejestrowane są zdarzenia związane z uruchamianiem, uzupełnianiem lub niepowodzeniem przygotowania urządzenia do wysłania. |
|     Provision                                 |     Rejestrowane są zdarzenia związane z uruchamianiem, kończeniem lub niepowodzeniem zadania aprowizacji urządzeń.|
|     Zadanie inspekcji pakietu                       |     Rejestrowane są zdarzenia związane z uruchamianiem, ukończeniem lub niepowodzeniem zadania inspekcji pakietu, które tworzy łańcuch dzienników opieki.|
|     Zastępowanie dysku                          |     Niepowodzenie zastępowania dysku jest rejestrowane.|
|     Włączanie lub wyłączanie zdalnego programu PowerShell     |     Rejestrowane są zdarzenia związane z włączaniem lub wyłączaniem zdalnego programu PowerShell na urządzeniu. |
|     Pobierz szczegóły fazy instalacji               |     Zdarzenia związane z instalacją oprogramowania na urządzeniu w fazach są rejestrowane w centrum danych platformy Azure.|
|     Odblokuj lub Zablokuj wolumin funkcji BitLocker           |     Zdarzenia są rejestrowane w celu wskazania stanu funkcji BitLocker woluminu *basevolume* i *hcsdata* .|
|     Oczyszczanie dysku                              |     Rejestrowane są zdarzenia związane z awarią dysków fizycznych, których nie można wymazać, oraz zdarzenia, w przypadku których wszystkie dyski fizyczne w urządzeniu zostały pomyślnie wymazane. |
|     Włączanie lub wyłączanie użytkownika lokalnego               |     Rejestrowane są zdarzenia związane z włączaniem lub wyłączaniem kont użytkowników lokalnych dla StorSimpleAdmin i PodSupportAdminUser.| 
|     Resetowanie hasła                          |     Zdarzenia związane z pomyślne lub niepowodzeniem resetowania hasła dla użytkownika lokalnego StorSimpleAdmin są rejestrowane. |


Poza dziennikami inspekcji IFX urządzenie Data Box zbierane są również łańcuchy dzienników inspekcji. Te dzienniki nie mogą być wyświetlane w czasie rzeczywistym, ale dopiero po zakończeniu zadania i usunięciu danych z dysków urządzenie Data Box. Te dzienniki zawierają podzestaw informacji zawartych w dziennikach inspekcji IFX.

Aby uzyskać więcej informacji na temat łańcucha dzienników inspekcji opieki, zobacz [pobieranie łańcucha przechowywania dzienników po usunięciu danych](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji

Te dzienniki są przechowywane na platformie Azure i nie można uzyskać do nich bezpośredniego dostępu. Jeśli chcesz uzyskać dostęp do tych dzienników, należy uzyskać bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Contact pomoc techniczna firmy Microsoft](data-box-disk-contact-microsoft-support.md). 

Po zgłoszeniu biletu pomocy technicznej firma Microsoft pobierze i udostępni dostęp do tych dzienników.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak rozwiązywać problemy dotyczące urządzenie Data Box i Data Box Heavy](data-box-troubleshoot.md).
