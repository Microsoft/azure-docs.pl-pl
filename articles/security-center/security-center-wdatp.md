---
title: Korzystanie z licencji usługi Microsoft Defender dla punktu końcowego zawartej w programie Azure Security Center
description: Dowiedz się więcej o usłudze Microsoft Defender dla punktów końcowych i wdrażaj ją z poziomu Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/08/2021
ms.author: memildin
ms.openlocfilehash: 085f3a5295d60b83536683a57a34b51abccd3067
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043023"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Ochrona punktów końcowych za pomocą zintegrowanego rozwiązania EDR Security Center: Microsoft Defender for Endpoint

Microsoft Defender for Endpoint to całościowe, dostarczone w chmurze rozwiązanie zabezpieczeń punktu końcowego. Główne funkcje są następujące:

- Zarządzanie lukami w zabezpieczeniach i Ocena 
- Zmniejszanie obszaru ataków
- Ochrona oparta na zasadach i w chmurze
- Wykrywanie i reagowanie punktów końcowych (EDR)
- Automatyczne badanie i korygowanie
- Zarządzane usługi polowania

> [!TIP]
> Pierwotnie uruchomiono usługę **Windows Defender ATP**, nazwa produktu wykrywania i reagowania punktu końcowego (EDR) została zmieniona w 2019 jako usługa **Microsoft Defender ATP**.
>
> W przypadku zapłonu 2020 został uruchomiony [pakiet Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) i zmieniono nazwę usługi **Microsoft Defender dla punktu końcowego**.


## <a name="availability"></a>Dostępność

| Aspekt                          | Szczegóły                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:                  | Ogólnie dostępna (GA)                                                                                                                                                                                                                                                                                      |
| Wpisaną                        | Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Obsługiwane platformy:            |  • Maszyny platformy Azure z systemem Windows<br> • Komputery z systemem Windows w usłudze Azure Arc|
| Obsługiwane wersje systemu Windows:  |  • **Ogólna dostępność (ga) —** wykrywanie w systemach Windows Server 2016, 2012 R2 i 2008 R2 SP1<br> • Wykrywanie **wersji zapoznawczej** w systemie windows Server 2019, [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)i [Windows 10 Enterprise — wiele sesji](../virtual-desktop/windows-10-multisession-faq.md) (dawniej Enterprise for Virtual Desktops (EVD)|
| Nieobsługiwane systemy operacyjne:  |  • System Windows 10 (inny niż EVD lub WVD)<br> • System Linux|
| Wymagane role i uprawnienia: | Aby włączyć/wyłączyć integrację: **administrator zabezpieczeń** lub **właściciel**<br>Aby wyświetlić alerty MDATP w Security Center: **czytelnik zabezpieczeń**, **czytelnik**, **współautor grupy zasobów**, **właściciel grupy zasobów**, **administrator zabezpieczeń**, **właściciel subskrypcji** lub **współautor subskrypcji**|
| Połączeń                         | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Funkcje usługi Microsoft Defender dla punktów końcowych w Security Center

Usługa Microsoft Defender dla punktu końcowego oferuje następujące informacje:

- **Zaawansowane czujniki wykrywania po naruszeniu naruszeń**. Usługa Defender dla czujników punktu końcowego dla maszyn z systemem Windows zbiera ogromną gamę sygnałów z zachowaniem.

- Wykrywanie z obsługą **analiz, oparte na chmurze, wykrycie po naruszeniu**. Usługa Defender dla punktów końcowych szybko dostosowuje się do zmieniających się zagrożeń. Używa zaawansowanej analizy i danych Big Data. Jest to wzmacniane przez możliwości Intelligent Security Graph z sygnałami w systemach Windows, Azure i Office w celu wykrywania nieznanych zagrożeń. Zapewnia alerty funkcjonalne i umożliwia szybkie reagowanie na nie.

- **Analiza zagrożeń**. Usługa Defender dla punktu końcowego generuje alerty w przypadku identyfikacji narzędzi, technik i procedur osoby atakującej. Używa on danych wygenerowanych przez program Microsoft Threat myśliwych i zespoły ds. zabezpieczeń, uzupełnione o analizy udostępniane przez partnerów.

Dzięki integracji usługi Defender dla punktów końcowych z Security Center można korzystać z następujących dodatkowych możliwości:

- **Automatyczne** dołączanie. Security Center automatycznie włącza czujnik usługi Microsoft Defender for Endpoint dla wszystkich serwerów z systemem Windows monitorowanych przez Security Center.

- **Pojedyncze okienko szkła**. W konsoli Security Center są wyświetlane alerty usługi Microsoft Defender dla punktów końcowych. Aby dowiedzieć się więcej, Użyj usługi Microsoft Defender dla własnych stron portalu punktu końcowego, na których zobaczysz dodatkowe informacje, takie jak drzewo procesu alertu i wykres incydentu. Możesz również wyświetlić szczegółową oś czasu komputera, która pokazuje każde zachowanie w okresie historycznym przez maksymalnie sześć miesięcy.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Security Center własne usługi Microsoft Defender dla punktu końcowego" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Lokalizacja dzierżawy usługi Microsoft Defender dla punktu końcowego

W przypadku używania Azure Security Center do monitorowania serwerów usługa Microsoft Defender dla dzierżawy punktu końcowego jest tworzona automatycznie. Dane zbierane przez usługę Defender for Endpoint są przechowywane w lokalizacji geograficznej dzierżawy, która została zidentyfikowana podczas aprowizacji. Dane klienta w formie pseudonimowej — mogą być również przechowywane w centralnych systemach magazynowania i przetwarzania w Stany Zjednoczone. 

Po skonfigurowaniu lokalizacji nie można jej zmienić. Jeśli masz własną licencję usługi Microsoft Defender for Endpoint i chcesz przenieść dane do innej lokalizacji, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu zresetowania dzierżawy.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Włączanie integracji usługi Microsoft Defender for Endpoint

1. Upewnij się, że maszyna spełnia wymagania wymagane przez usługę Defender for Endpoint:

    - Dla **wszystkich wersji systemu Windows**:
        - Konfigurowanie ustawień sieci opisanych w temacie [Konfigurowanie serwera proxy urządzenia i ustawień łączności z Internetem](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
        - Jeśli program Defender jest wdrażany na maszynach lokalnych, połącz go z usługą Azure Arc zgodnie z opisem w temacie [łączenie maszyn hybrydowych z serwerami z obsługą usługi Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - Ponadto w przypadku **maszyn z systemem Windows Server 2019** upewnij się, że są uruchomione prawidłowy Agent i mają rozszerzenie MicrosoftMonitoringAgent

1. Włącz **usługę Azure Defender dla serwerów**. Zobacz [Szybki Start: Włączanie usługi Azure Defender](enable-azure-defender.md).

1. Jeśli masz już licencjonowane i wdrożone usługi Microsoft Defender dla punktów końcowych na serwerach, usuń je, korzystając z procedury opisanej w artykule [odłączania Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz subskrypcję, którą chcesz zmienić.
1. Wybierz pozycję **wykrywanie zagrożeń**.
1. Wybierz pozycję **Zezwalaj usłudze Microsoft Defender for Endpoint na dostęp do moich danych** i wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Włącz integrację między Azure Security Center i rozwiązaniem EDR firmy Microsoft, Microsoft Defender for Endpoint":::

    Azure Security Center automatycznie dołączać serwery do usługi Microsoft Defender dla punktu końcowego. Dołączanie może potrwać do 24 godzin.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Dostęp do portalu usługi Microsoft Defender dla punktów końcowych

1. Upewnij się, że konto użytkownika ma wymagane uprawnienia. Dowiedz się więcej w temacie [Przypisywanie dostępu użytkowników do programu Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Sprawdź, czy masz serwer proxy lub zaporę blokującą ruch anonimowy. Usługa Defender dla czujnika punktu końcowego nawiązuje połączenie z kontekstem systemowym, więc należy zezwolić na ruch anonimowy. Aby zapewnić niezakłócony dostęp do programu Defender for Endpoint Portal, postępuj zgodnie z instrukcjami w temacie [Włączanie dostępu do adresów URL usługi na serwerze proxy](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Otwórz [portal Security Center Microsoft Defender](https://securitycenter.windows.com/). Więcej informacji na temat funkcji i ikon portalu można znaleźć w temacie [Omówienie usługi Microsoft Defender Security Center Portal](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Wysyłanie alertu testowego

Aby wygenerować niegroźny alert dotyczący programu Microsoft Defender for Endpoint test:

1. Utwórz folder "C:\test-MDATP-test".
1. Użyj Pulpit zdalny, aby uzyskać dostęp do komputera.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia skopiuj i uruchom następujące polecenie. Okno wiersza polecenia zostanie zamknięte automatycznie.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Okno wiersza polecenia z poleceniem do wygenerowania alertu testowego.":::

1. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowy Alert na pulpicie nawigacyjnym Azure Security Center oraz w portalu usługi Microsoft Defender dla punktów końcowych. Ten alert może potrwać kilka minut.
1. Aby sprawdzić alert w Security Center, przejdź do **alertów zabezpieczeń**  >  **podejrzany wiersz polecenia programu PowerShell**.
1. W oknie badanie wybierz link, aby przejść do portalu usługi Microsoft Defender dla punktu końcowego.

    > [!TIP]
    > Alert jest wyzwalany z ważnością **informacyjną** .

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Często zadawane pytania dotyczące zintegrowanego Security Center usługi Microsoft Defender for Endpoint

- [Jakie są wymagania dotyczące licencjonowania usługi Microsoft Defender for Endpoint?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Jeśli mam już licencję usługi Microsoft Defender for Endpoint dla punktu końcowego, możesz uzyskać rabat za usługę Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Jak mogę przełączać się z narzędzia EDR innej firmy?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Jakie są wymagania dotyczące licencjonowania usługi Microsoft Defender for Endpoint?
Usługa Defender dla punktów końcowych jest uwzględniana bez dodatkowych kosztów w **usłudze Azure Defender dla serwerów**. Alternatywnie można ją zakupić osobno dla maszyn 50 lub więcej.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Jeśli mam już licencję usługi Microsoft Defender for Endpoint dla punktu końcowego, możesz uzyskać rabat za usługę Azure Defender?
Jeśli masz już licencję na usługę Microsoft Defender for Endpoint, nie musisz uiszczać tej części licencji usługi Azure Defender.

Aby potwierdzić swój rabat, skontaktuj się z zespołem pomocy technicznej Security Center i podaj odpowiedni identyfikator obszaru roboczego, region i informacje o licencji dla każdej odpowiedniej licencji.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Jak mogę przełączać się z narzędzia EDR innej firmy?
Pełne instrukcje dotyczące przełączania z rozwiązania punktu końcowego innego niż Microsoft są dostępne w dokumentacji usługi Microsoft Defender dla punktów końcowych: [Omówienie migracji](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md): informacje na temat sposobu, w jaki zalecenia ułatwiają ochronę zasobów platformy Azure.