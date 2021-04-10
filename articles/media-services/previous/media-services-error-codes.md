---
title: Kody błędów Azure Media Services | Microsoft Docs
description: W zależności od problemów, takich jak tokeny uwierzytelniania, które nie są obsługiwane w programie Media Services, mogą zostać wyświetlone kody błędów HTTP z usługi. Ten artykuł zawiera omówienie kodów błędów interfejsu API Azure Media Services V2.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013418"
---
# <a name="azure-media-services-error-codes"></a>Kody błędów usługi Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W przypadku korzystania z Microsoft Azure Media Services można odbierać kody błędów HTTP z usługi w zależności od problemów, takich jak tokeny uwierzytelniania wygasające w przypadku akcji, które nie są obsługiwane w programie Media Services. Poniżej znajduje się lista **kodów błędów HTTP** , które mogą zostać zwrócone przez Media Services i możliwych dla nich przyczyn.  

## <a name="400-bad-request"></a>400 Nieprawidłowe żądanie
Żądanie zawiera nieprawidłowe informacje i zostało odrzucone z jednego z następujących powodów:

* Określono nieobsługiwaną wersję interfejsu API. Aby uzyskać najbardziej aktualną wersję, zobacz [Setup for Media Services API REST Development](media-services-rest-how-to-use.md).
* Nie określono wersji interfejsu API Media Services. Aby uzyskać informacje na temat sposobu określania wersji interfejsu API, zobacz temat [Informacje o interfejsie API REST Media Services Operations](/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Jeśli używasz zestawów SDK .NET lub Java do nawiązywania połączenia z Media Services, wersja interfejsu API jest określana dla Ciebie za każdym razem, gdy spróbujesz wykonać kilka czynności w odniesieniu do Media Services.
  > 
  > 
* Określono niezdefiniowaną właściwość. Nazwa właściwości znajduje się w komunikacie o błędzie. Można określić tylko te właściwości, które są elementami członkowskimi danej jednostki. Zobacz [Azure Media Services Dokumentacja interfejsu API REST](/rest/api/media/operations/azure-media-services-rest-api-reference) , aby uzyskać listę jednostek i ich właściwości.
* Określono nieprawidłową wartość właściwości. Nazwa właściwości znajduje się w komunikacie o błędzie. Zobacz poprzedni link, aby uzyskać prawidłowe typy właściwości i ich wartości.
* Brak wartości właściwości i jest ona wymagana.
* Część podanego adresu URL zawiera nieprawidłową wartość.
* Podjęto próbę zaktualizowania właściwości flagą writeonce.
* Podjęto próbę utworzenia zadania, które ma wejściowy element zawartości z AssetFileem głównym, który nie został określony lub nie można go określić.
* Podjęto próbę zaktualizowania lokalizatora SAS. Lokalizatory SAS można tworzyć i usuwać tylko. Można aktualizować lokalizatory przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [lokalizatory](/rest/api/media/operations/locator).
* Przesłano nieobsługiwaną operację lub zapytanie.

## <a name="401-unauthorized"></a>401 Brak autoryzacji
Nie można uwierzytelnić żądania (przed jego autoryzacją) z jednego z następujących powodów:

* Brak nagłówka uwierzytelniania.
* Zła wartość nagłówka uwierzytelniania.
  * Token wygasł. 
  * Token zawiera nieprawidłową sygnaturę.

## <a name="403-forbidden"></a>403 Zabronione
Żądanie jest niedozwolone z jednego z następujących powodów:

* Nie można odnaleźć konta Media Services lub zostało ono usunięte.
* Konto Media Services jest wyłączone, a typ żądania to nie HTTP GET. Operacje usługi zwróci także odpowiedź 403.
* Token uwierzytelniania nie zawiera informacji o poświadczeniach użytkownika: AccountName i/lub Identyfikator subskrypcji. Te informacje można znaleźć w Media Services rozszerzeniu interfejsu użytkownika dla konta Media Services w portal zarządzania platformy Azure.
* Nie można uzyskać dostępu do zasobu.
  
  * Podjęto próbę użycia MediaProcessor, która nie jest dostępna dla Twojego konta Media Services.
  * Podjęto próbę zaktualizowania JobTemplate zdefiniowanego przez Media Services.
  * Podjęto próbę zastąpienia innego lokalizatora konta Media Services.
  * Podjęto próbę zastąpienia innego ContentKey konta Media Services.
* Nie można utworzyć zasobu z powodu limitu przydziału usługi, który został osiągnięty dla konta Media Services. Aby uzyskać więcej informacji na temat przydziałów usługi, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 — Nie znaleziono
Żądanie jest niedozwolone w przypadku zasobu z jednego z następujących powodów:

* Podjęto próbę zaktualizowania jednostki, która nie istnieje.
* Podjęto próbę usunięcia jednostki, która nie istnieje.
* Podjęto próbę utworzenia jednostki, która łączy się z nieistniejącą jednostką.
* Podjęto próbę pobrania jednostki, która nie istnieje.
* Podjęto próbę określenia konta magazynu, które nie jest skojarzone z kontem Media Services.  

## <a name="409-conflict"></a>409 Konflikt
Żądanie jest niedozwolone z jednego z następujących powodów:

* Więcej niż jeden AssetFile ma określoną nazwę w obrębie elementu zawartości.
* Podjęto próbę utworzenia drugiego podstawowego AssetFile w elemencie zawartości.
* Podjęto próbę utworzenia ContentKey o określonym identyfikatorze.
* Podjęto próbę utworzenia lokalizatora o określonym identyfikatorze.
* Więcej niż jeden IngestManifestFile ma określoną nazwę w IngestManifest.
* Podjęto próbę połączenia drugiego ContentKey szyfrowania magazynu z zasobem zaszyfrowanym przez magazyn.
* Podjęto próbę połączenia tego samego ContentKey z zasobem.
* Podjęto próbę utworzenia lokalizatora do elementu zawartości, którego kontener magazynu brakuje lub nie jest już skojarzony z elementem zawartości.
* Podjęto próbę utworzenia lokalizatora dla elementu zawartości, który ma już 5 lokalizatorów w użyciu. (Usługa Azure Storage wymusza limit pięciu zasad dostępu współdzielonego w jednym kontenerze magazynu).
* Łączenie konta magazynu zasobu z IngestManifestAsset nie jest takie samo, jak konto magazynu używane przez nadrzędną IngestManifest.  

## <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera
Podczas przetwarzania żądania Media Services napotka jakiś błąd, który uniemożliwia kontynuowanie przetwarzania. Może to być spowodowane jedną z następujących przyczyn:

* Tworzenie zasobu lub zadania nie powiodło się, ponieważ informacje o limicie przydziału usługi konta Media Services są tymczasowo niedostępne.
* Tworzenie kontenera magazynu obiektów blob lub IngestManifest nie powiedzie się, ponieważ informacje o koncie magazynu konta są tymczasowo niedostępne.
* Inny nieoczekiwany błąd.

## <a name="503-service-unavailable"></a>503 — usługa niedostępna
Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowany przez nadmierną liczbę żądań do usługi. Mechanizm ograniczania usług Media Services ogranicza użycie zasobów dla aplikacji, które zgłaszają zbyt wiele żądań do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciąg kodu błędu, aby uzyskać bardziej szczegółowe informacje na temat przyczyny błędu 503. Ten błąd nie zawsze oznacza ograniczenie przepustowości.
> 
> 

Możliwe opisy stanu są następujące:

* "Serwer jest zajęty. Poprzednie uruchomienia tego typu żądania trwały ponad {0} sekund. "
* "Serwer jest zajęty. {0}Można ograniczyć liczbę żądań na sekundę.
* "Serwer jest zajęty. Maksymalna {0} Liczba żądań w ciągu kilku {1} sekund może być ograniczona. "

Aby obsłużyć ten błąd, zalecamy korzystanie z logiki ponawiania prób z powrotem. Oznacza to, że użycie stopniowo dłużej czeka między ponownymi próbami kolejnych odpowiedzi na błędy.  Aby uzyskać więcej informacji, zobacz [blok aplikacji obsługa błędów przejściowych](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> Jeśli używasz [zestawu Azure Media Services SDK dla platformy .NET](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logika ponowień dla błędu 503 została zaimplementowana przez zestaw SDK.  
> 
> 

## <a name="see-also"></a>Zobacz też
[Kody błędów zarządzania Media Services](/rest/api/media/)

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
