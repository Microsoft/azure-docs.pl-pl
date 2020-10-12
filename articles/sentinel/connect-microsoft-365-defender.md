---
title: Łączenie danych pierwotnych usługi Microsoft 365 Defender z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak pozyskiwanie nieprzetworzonych danych zdarzeń z programu Microsoft 365 Defender do platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938481"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Łączenie danych z programu Microsoft 365 Defender z platformą Azure — wskaźnikiem

> [!IMPORTANT]
>
> Usługa **Microsoft 365 Defender** była znana wcześniej jako **Microsoft Threat Protection** lub **MTP**.
>
> Usługa **Microsoft Defender dla punktu końcowego** była znana wcześniej jako usługa **Microsoft Defender Advanced Threat Protection** lub **MDATP**.
>
> W danym czasie nadal mogą być widoczne stare nazwy.

## <a name="background"></a>Tło

Nowy łącznik usługi [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) umożliwia przesyłanie strumieniowe **zaawansowanych dzienników łowiectwa** — typu danych nieprzetworzonych zdarzeń — od Microsoft 365 Defender do punktów kontrolnych platformy Azure. 

Dzięki integracji usługi [Microsoft Defender dla punktów końcowych (MDATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) na parasol zabezpieczeń programu Microsoft 365 Defender można teraz zbierać [Zaawansowane zdarzenia polowania](https://aka.ms/mdatpAH) usługi Microsoft Defender for Endpoint przy użyciu łącznika Microsoft 365 Defender i przesyłać je bezpośrednio do nowych, wbudowanych tabel w obszarze roboczym wskaźnikowego platformy Azure. Tabele te są oparte na tym samym schemacie, który jest używany w portalu Microsoft 365 Defender, co zapewnia pełny dostęp do pełnego zestawu zaawansowanych dzienników łowiectwa i umożliwia wykonywanie następujących czynności:

- Łatwo Kopiuj istniejące zapytania o wylowanie zaawansowane usługi Microsoft Defender ATP do platformy Azure.

- Za pomocą nieprzetworzonych dzienników zdarzeń można uzyskać dodatkowe informacje dotyczące alertów, polowań i badań oraz skorelować zdarzenia z danymi z dodatkowych źródeł danych na platformie Azure.

- Przechowuj dzienniki z zwiększonym przechowywaniem, poza programem Microsoft Defender dla punktów końcowych lub Microsoft 365 usługi Defender — domyślnie zatrzymywanie 30 dni. Można to zrobić przez skonfigurowanie przechowywania obszaru roboczego lub skonfigurowanie przechowywania poszczególnych tabel w Log Analytics.

> [!IMPORTANT]
>
> Łącznik Microsoft 365 Defender jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję usługi Microsoft Defender for Endpoint, zgodnie z opisem w temacie [Konfigurowanie usługi Microsoft Defender for Endpoint Deployment](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Użytkownik musi mieć przypisaną rolę administratora globalnego w dzierżawie (w Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Nawiązywanie połączenia z usługą Microsoft 365 Defender

Jeśli program Microsoft Defender for Endpoint został wdrożony i pozyskuje dane, dzienniki zdarzeń można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, wybierz pozycję **Microsoft 365 Defender (wersja zapoznawcza)** z galerii i wybierz pozycję **Otwórz stronę łącznika**.

1. Następujące typy zdarzeń można zbierać z odpowiednich, zaawansowanych tabel polowań. Zaznacz pola wyboru typów zdarzeń, które chcesz zebrać:

    | Typ zdarzeń | Nazwa tabeli |
    |-|-|
    | Informacje o maszynie (w tym informacje o systemie operacyjnym) | DeviceInfo |
    | Właściwości sieci maszyn | DeviceNetworkInfo |
    | Tworzenie procesów i powiązane zdarzenia | DeviceProcessEvents |
    | Połączenie sieciowe i powiązane zdarzenia | DeviceNetworkEvents |
    | Tworzenie, modyfikowanie i inne zdarzenia systemu plików | DeviceFileEvents |
    | Tworzenie i modyfikowanie wpisów rejestru | DeviceRegistryEvents |
    | Logowania i inne zdarzenia uwierzytelniania | DeviceLogonEvents |
    | Zdarzenia ładowania biblioteki DLL | DeviceImageLoadEvents |
    | Dodatkowe typy zdarzeń | DeviceEvents |
    |

1. Kliknij przycisk **Zastosuj zmiany**. 

1. Aby zbadać zaawansowane tabele polowania w Log Analytics, wprowadź nazwę tabeli z listy powyżej w oknie zapytania.

## <a name="verify-data-ingestion"></a>Weryfikuj pozyskiwanie danych

Wykres danych na stronie łącznika wskazuje, że dane są odbierane. Zobaczysz, że pokazuje pojedynczy wiersz, który agreguje wolumin zdarzeń we wszystkich włączonych tabelach. Po włączeniu łącznika można użyć następującego zapytania KQL do wygenerowania podobnego grafu ilości zdarzeń dla pojedynczej tabeli (Zmień tabelę *DeviceEvents* na żądaną tabelę wybraną):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Na karcie **następne kroki** znajdziesz kilka przykładowych zapytań, które zostały uwzględnione. Można je uruchomić na miejscu lub zmodyfikować i zapisać.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób pobierania nieprzetworzonych danych zdarzeń z usługi Microsoft Defender for Endpoint do platformy Azure — wskaźnik przy użyciu łącznika Microsoft 365 Defender. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
