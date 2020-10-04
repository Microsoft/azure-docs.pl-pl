---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711426"
---
| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **Typ geotype** | Transakcja z klastra podstawowego lub pomocniczego. Dostępne wartości to **podstawowy** i **pomocniczy**. Dotyczy on dostępu do odczytu geograficznie nadmiarowego magazynu (RA-GRS) podczas odczytywania obiektów z pomocniczej dzierżawy. |
| **Responsetype** | Typ odpowiedzi transakcji. Dostępne wartości obejmują: <br/><br/> <li>**ServerOtherError**: Wszystkie inne błędy po stronie serwera z wyjątkiem opisanych </li> <li>**ServerBusyError**: Uwierzytelnione żądanie, które zwróciło kod stanu HTTP 503. </li> <li>**ServerTimeoutError**: Uwierzytelnione żądanie z przekroczonym limitem czasu, które zwróciło kod stanu HTTP 500. Przekroczenie limitu czasu wystąpiło z powodu błędu serwera. </li> <li>**AuthorizationError**: Uwierzytelnione żądanie, które nie powiodło się z powodu nieautoryzowanego dostępu do danych lub błędu autoryzacji. </li> <li>**NetworkError**: Uwierzytelnione żądanie, które nie powiodło się z powodu błędów sieci. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed wygaśnięciem limitu czasu. </li><li>**ClientAccountBandwidthThrottlingError**: żądanie jest ograniczane na przepustowość, aby przekroczyć [limity skalowalności konta magazynu](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: żądanie jest ograniczone przy częstotliwości żądania w przypadku przekroczenia [limitów skalowalności konta magazynu](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: inne błędy ograniczania po stronie klienta. ClientAccountBandwidthThrottlingError i ClientAccountRequestThrottlingError są wykluczone.</li> <li>**ClientTimeoutError**: Uwierzytelnione żądanie z przekroczonym limitem czasu, które zwróciło kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiony na mniejszą wartość niż oczekiwana przez usługę magazynu, jest to oczekiwany limit czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError. </li> <li>**ClientOtherError**: Wszystkie inne błędy po stronie klienta z wyjątkiem opisanych. </li> <li>**Success**: Żądanie zakończone powodzeniem</li> <li> **SuccessWithThrottling**: pomyślne żądanie, gdy klient SMB zostanie w pierwszej kolejności określony, ale po ponownych próbach zakończy się pomyślnie.</li> |
| **ApiName** | Nazwa operacji. 
| **Authentication** | Typ uwierzytelniania używany w transakcjach. Dostępne wartości obejmują: <br/> <li>**AccountKey**: transakcja jest uwierzytelniana przy użyciu klucza konta magazynu.</li> <li>**SAS**: transakcja jest uwierzytelniana za pomocą sygnatur dostępu współdzielonego.</li> <li>**OAuth**: transakcja jest uwierzytelniana przy użyciu tokenów dostępu OAuth.</li> <li>**Anonimowe**: transakcja jest wymagana anonimowo. Nie obejmuje to żądań inspekcji wstępnej.</li> <li>**AnonymousPreflight**: transakcja jest żądaniem wstępnym.</li> |