---
title: Łączenie danych usługi Microsoft 365 Defender z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak pozyskiwanie zdarzeń, alertów i nieprzetworzonych danych zdarzeń z programu Microsoft 365 Defender do platformy Azure.
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
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709346"
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

Łącznik [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) z funkcją integracji zdarzeń platformy Azure umożliwia strumieniowe przesyłanie wszystkich zdarzeń M365D i alertów do usługi Azure wskaźnikowych oraz zachowanie synchronizacji zdarzeń między obydwoma portalami. Zdarzenia M365D obejmują wszystkie ich alerty, jednostki i inne istotne informacje, a także są wzbogacane o alerty z usług M365D's Component Services **Microsoft Defender dla punktów końcowych**, **Microsoft Defender for Identity**, **Microsoft defender dla Office 365** i **Microsoft Cloud App Security**.

Łącznik umożliwia również strumieniowe przesyłanie **zaawansowanych zdarzeń polowania** z usługi Microsoft Defender dla punktów końcowych do platformy Azure, dzięki czemu można kopiować zaawansowane zapytania polowania w programie MDE do usługi Azure wskaźnikowej, wzbogacać alerty punktów kontrolnych z danymi zdarzeń w formacie MDE RAW, aby zapewnić dodatkowe szczegółowe informacje, a także przechowywać dzienniki o zwiększonej obtrzymywania w log Analytics.

Aby uzyskać więcej informacji na temat integracji zdarzeń i zaawansowanej kolekcji zdarzeń polowania, zobacz [Microsoft 365 integrację z platformą Azure](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Łącznik Microsoft 365 Defender jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję usługi Microsoft 365 Defender, zgodnie z opisem w sekcji [wymagania wstępne usługi Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- Użytkownik musi być **administratorem globalnym** lub **administratorem zabezpieczeń** w Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Nawiązywanie połączenia z usługą Microsoft 365 Defender

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, wybierz pozycję **Microsoft 365 Defender (wersja zapoznawcza)** z galerii i wybierz pozycję **Otwórz stronę łącznika**.

1. W obszarze **Konfiguracja** w sekcji **łączenie zdarzeń & alerty** kliknij przycisk **Połącz zdarzenia & alerty** .

1. Aby uniknąć duplikowania incydentów, zaleca się zaznaczenie pola wyboru z etykietą Wyłącz **wszystkie reguły tworzenia zdarzeń firmy Microsoft dla tych produktów.**

    > [!NOTE]
    > Po włączeniu łącznika Microsoft 365 Defender wszystkie łączniki składników M365D (wymienione na początku tego artykułu) zostaną automatycznie połączone w tle. Aby rozłączyć jeden z łączników składników, należy najpierw odłączyć Łącznik Microsoft 365 Defender.

1. Aby zbadać dane zdarzenia M365 Defender, należy użyć następującej instrukcji w oknie zapytania:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Jeśli chcesz zbierać zaawansowane zdarzenia polowania z usługi Microsoft Defender dla punktów końcowych, można zbierać następujące typy zdarzeń z odpowiednich zaawansowanych tabel polowania.

    1. Zaznacz pola wyboru w tabelach z typami zdarzeń, które chcesz zebrać:

       | Nazwa tabeli | Typ zdarzeń |
       |-|-|
       | DeviceInfo | Informacje o maszynie (w tym informacje o systemie operacyjnym) |
       | DeviceNetworkInfo | Właściwości sieci maszyn |
       | DeviceProcessEvents | Tworzenie procesów i powiązane zdarzenia |
       | DeviceNetworkEvents | Połączenie sieciowe i powiązane zdarzenia |
       | DeviceFileEvents | Tworzenie, modyfikowanie i inne zdarzenia systemu plików |
       | DeviceRegistryEvents | Tworzenie i modyfikowanie wpisów rejestru |
       | DeviceLogonEvents | Logowania i inne zdarzenia uwierzytelniania |
       | DeviceImageLoadEvents | Zdarzenia ładowania biblioteki DLL |
       | DeviceEvents | Dodatkowe typy zdarzeń |
       | DeviceFileCertificateInfo | Informacje o certyfikacie podpisanych plików |
       |

    1. Kliknij przycisk **Zastosuj zmiany**.

    1. Aby zbadać zaawansowane tabele polowania w Log Analytics, wprowadź nazwę tabeli z listy powyżej w oknie zapytania.

## <a name="verify-data-ingestion"></a>Weryfikuj pozyskiwanie danych

Wykres danych na stronie łącznika wskazuje, że dane są odbierane. Zobaczysz, że przedstawia on jeden wiersz dla zdarzeń, alertów i zdarzeń, a wiersz zdarzenia jest agregacją objętości zdarzenia we wszystkich włączonych tabelach. Po włączeniu łącznika można użyć następujących zapytań KQL w celu wygenerowania bardziej szczegółowych wykresów.

Poniższe zapytanie KQL służy do tworzenia grafu zdarzeń przychodzącej usługi M365 Defender:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Użyj następującego zapytania KQL do wygenerowania grafu ilości zdarzeń dla pojedynczej tabeli (Zmień tabelę *DeviceEvents* na wybraną tabelę w wyborze):

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

Na karcie **następne kroki** znajdziesz przydatne skoroszyty, przykładowe zapytania i szablony reguł analizy, które zostały uwzględnione. Można je uruchomić na miejscu lub zmodyfikować i zapisać.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób integrowania zdarzeń Microsoft 365 Defender i zaawansowane dane zdarzeń polowania z usługi Microsoft Defender dla punktów końcowych na platformie Azure, przy użyciu łącznika Microsoft 365 Defender. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
