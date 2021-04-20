---
title: Używanie licencji usługi Microsoft Defender dla punktu końcowego dołączonej do usługi Azure Security Center
description: Dowiedz się więcej na temat usługi Microsoft Defender dla punktu końcowego i wdrażania jej z Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: e12578fa6da679587d41fb25b17b00eb1645299a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718417"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Ochrona punktów końcowych za Security Center rozwiązania EDR firmy Microsoft: Microsoft Defender dla punktu końcowego

Usługa Microsoft Defender dla punktu końcowego to całościowe rozwiązanie zabezpieczeń punktu końcowego dostarczane przez chmurę. Jego główne funkcje to:

- Oparte na ryzyku zarządzanie lukami w zabezpieczeniach i ocena ich 
- Zmniejszanie obszaru ataków
- Ochrona oparta na zachowaniu i oparta na chmurze
- Wykrywanie punktów końcowych i reagowanie na nie (EDR)
- Automatyczne badanie i korygowanie
- Zarządzane usługi łowieckie

> [!TIP]
> Ten produkt, który pierwotnie był Windows Defender **ATP,** został nazwany w 2019 r. jako **Microsoft Defender ATP.**
>
> Na konferencji Ignite 2020 uruchomiliśmy pakiet XDR usługi [Microsoft Defender,](https://www.microsoft.com/security/business/threat-protection) a nazwa tego składnika EDR została zmieniona na **Microsoft Defender for Endpoint**.


## <a name="availability"></a>Dostępność

| Aspekt                          | Szczegóły                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:                  | Ogólnie dostępna (GA)                                                                                                                                                                                                                                                                                      |
| Ceny:                        | Wymaga [Azure Defender dla serwerów](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Obsługiwane platformy:            |  • Maszyny platformy Azure z systemem Windows<br> • Azure Arc z systemem Windows|
| Obsługiwane wersje systemu Windows do wykrywania:  |  • Windows Server 2019, 2016, 2012 R2 i 2008 R2 z dodatkiem SP1<br> • [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise wielu sesji](../virtual-desktop/windows-10-multisession-faq.yml) (dawniej Enterprise for Virtual Desktops (EVD)|
| Nieobsługiwane systemy operacyjne:  |  • Windows 10 (inne niż EVD lub WVD)<br> • Linux|
| Wymagane role i uprawnienia: | Aby włączyć/wyłączyć integrację: **Administrator zabezpieczeń lub** **Właściciel**<br>Aby wyświetlić alerty MDATP w Security Center: **czytelnik zabezpieczeń,** **Czytelnik,** Współautor grupy **zasobów,** Właściciel grupy **zasobów,** Administrator **zabezpieczeń,** Właściciel subskrypcji **lub** **Współautor subskrypcji**|
| Chmury:                         | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Funkcje usługi Microsoft Defender dla punktu końcowego w Security Center

Usługa Microsoft Defender dla punktu końcowego zapewnia:

- **Zaawansowane czujniki wykrywania po naruszeniu zabezpieczeń.** Usługa Defender dla czujników punktu końcowego dla maszyn z systemem Windows zbiera ogromną tablicę sygnałów behawioralnych.

- **Oparte na analizie, oparte na chmurze wykrywanie po naruszeniu zabezpieczeń.** Program Defender for Endpoint szybko dostosowuje się do zmieniających się zagrożeń. Używa ona zaawansowanej analizy i danych big data. Jest on wzmacniany przez możliwości usługi Intelligent Security Graph sygnałów w systemie Windows, na platformie Azure i w psłudze Office w celu wykrywania nieznanych zagrożeń. Udostępnia alerty z akcjami i umożliwia szybkie reagowanie.

- **Analiza zagrożeń**. Program Defender for Endpoint generuje alerty po zidentyfikowaniu narzędzi, technik i procedur osoby atakującej. Korzysta ona z danych generowanych przez zespoły ds. zagrożeń i zabezpieczeń firmy Microsoft, rozszerzane przez analizę dostarczaną przez partnerów.

Dzięki integracji usługi Defender for Endpoint z Security Center, będziesz korzystać z następujących dodatkowych możliwości:

- **Zautomatyzowane dołączanie.** Security Center automatycznie włącza czujnik usługi Microsoft Defender dla punktu końcowego dla wszystkich serwerów z systemem Windows monitorowanych przez Security Center.

- **Pojedyncze okienko .** W Security Center zostanie wyświetlona usługa Microsoft Defender dla alertów punktu końcowego. Aby dokładniej zbadać ten temat, użyj usługi Microsoft Defender dla własnych stron portalu punktu końcowego, na których zobaczysz dodatkowe informacje, takie jak drzewo procesu alertów i wykres zdarzenia. Można również wyświetlić szczegółową oś czasu maszyny, która pokazuje każde zachowanie w okresie historycznym nie dłuższym niż sześć miesięcy.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Usługa Microsoft Defender dla własnych Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Lokalizacja dzierżawy usługi Microsoft Defender dla punktu końcowego

Gdy używasz usługi Azure Security Center do monitorowania serwerów, automatycznie tworzona jest dzierżawa usługi Microsoft Defender dla punktu końcowego. Dane zbierane przez usługę Defender dla punktu końcowego są przechowywane w lokalizacji geograficznej dzierżawy zidentyfikowana podczas aprowizacji. Dane klienta — w postaci pseudonimizowanej — mogą być również przechowywane w centralnych systemach magazynowania i przetwarzania w Stany Zjednoczone. 

Po skonfigurowaniu lokalizacji nie można jej zmienić. Jeśli masz własną licencję usługi Microsoft Defender dla punktu końcowego i musisz przenieść dane do innej lokalizacji, skontaktuj się z Pomoc techniczna Microsoft w celu zresetowania dzierżawy.


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Włączanie integracji usługi Microsoft Defender dla punktu końcowego

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że maszyna spełnia wymagania dotyczące usługi Defender dla punktu końcowego:

1. Konfigurowanie ustawień sieciowych opisanych w te [tematach Konfigurowanie serwera proxy urządzenia i ustawień łączności z Internetem](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
1. Jeśli wdrażasz usługę Defender w punkcie końcowym na maszynach lokalnych, połącz ją z usługą Azure Arc, jak wyjaśniono w te tematu Connect hybrid machines with Azure Arc enabled servers (Łączenie maszyn hybrydowych z Azure Arc serwerami z [włączoną obsługą usługi)](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
1. Tylko w przypadku maszyn z systemem Windows Server 2019 upewnij się, że na maszynach działa prawidłowy agent i że masz rozszerzenie MicrosoftMonitoringAgent
1. Włącz **Azure Defender dla serwerów**. Zobacz [Szybki start: włączanie Azure Defender](enable-azure-defender.md).
1. Jeśli masz już licencję i wdrożono usługę Microsoft Defender dla punktów końcowych na serwerach, usuń ją przy użyciu procedury opisanej w te tematze Odłącz serwery [z systemem Windows.](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)
1. Jeśli subskrypcja została przeniesiona między dzierżawami platformy Azure, wymagane są również kroki ręcznego przygotowania. Aby uzyskać szczegółowe informacje, skontaktuj [się z pomocą techniczną firmy Microsoft.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Włączanie integracji
1. W Security Center menu wybierz pozycję **Cennik & i** wybierz subskrypcję, którą chcesz zmienić.
1. Wybierz **pozycję Wykrywanie zagrożeń.**
1. Wybierz **pozycję Zezwalaj u programowi Microsoft Defender dla punktu końcowego na** dostęp do moich danych, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Włączanie integracji między Azure Security Center rozwiązaniami EDR firmy Microsoft i usługą Microsoft Defender dla punktu końcowego":::

    Azure Security Center automatycznie dołącza serwery do usługi Microsoft Defender dla punktu końcowego. Dołączanie może potrwać do 24 godzin.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Uzyskiwanie dostępu do portalu usługi Microsoft Defender dla punktu końcowego

1. Upewnij się, że konto użytkownika ma niezbędne uprawnienia. Aby dowiedzieć się [więcej, zobacz Przypisywanie dostępu użytkownika do Centrum zabezpieczeń usługi Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Sprawdź, czy masz serwer proxy lub zaporę, która blokuje ruch anonimowy. Czujnik usługi Defender for Endpoint łączy się z kontekstu systemu, dlatego ruch anonimowy musi być dozwolony. Aby zapewnić nieskonkantowany dostęp do portalu usługi Defender dla punktu końcowego, wykonaj instrukcje z tematu Zapewnianie dostępu do adresów URL usługi [na serwerze proxy.](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

1. Otwórz Centrum zabezpieczeń usługi Microsoft Defender [portalu.](https://securitycenter.windows.com/) Dowiedz się więcej o funkcjach i ikonach portalu w [Centrum zabezpieczeń usługi Microsoft Defender portalu.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Wysyłanie alertu testowego

Aby wygenerować niegroźny alert testu usługi Microsoft Defender dla punktu końcowego:

1. Utwórz folder "C:\test-MDATP-test".
1. Użyj Pulpit zdalny, aby uzyskać dostęp do maszyny.
1. Otwórz okno wiersza polecenia.
1. Po wyświetleniu monitu skopiuj i uruchom następujące polecenie. Okno wiersza polecenia zostanie zamknięte automatycznie.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Okno wiersza polecenia z poleceniem w celu wygenerowania alertu testowego.":::

1. Jeśli polecenie powiedzie się, zobaczysz nowy alert na pulpicie nawigacyjnym usługi Azure Security Center i portalu usługi Microsoft Defender dla punktu końcowego. Ten alert może potrwać kilka minut.
1. Aby przejrzeć alert w Security Center, przejdź do tematu Alerty zabezpieczeń  >  **Podejrzane polecenie programu PowerShell Wiersz**.
1. W oknie badania wybierz link, aby przejść do portalu usługi Microsoft Defender dla punktu końcowego.

    > [!TIP]
    > Alert jest wyzwalany z **ważnością informacyjną.**

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Często zadawane pytania dotyczące Security Center zintegrowanej usługi Microsoft Defender dla punktu końcowego

- [Jakie są wymagania licencyjne dotyczące usługi Microsoft Defender dla punktu końcowego?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Jeśli mam już licencję usługi Microsoft Defender dla punktu końcowego, czy mogę uzyskać rabat na Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Jak mogę z narzędzia do EDR innej firmy?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Jakie są wymagania licencyjne dotyczące usługi Microsoft Defender dla punktu końcowego?
Program Defender dla punktu końcowego jest dołączony bez dodatkowych kosztów Azure Defender **dla serwerów**. Alternatywnie można kupić osobno dla 50 maszyn lub więcej.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Jeśli mam już licencję usługi Microsoft Defender dla punktu końcowego, czy mogę uzyskać rabat na Azure Defender?
Jeśli masz już licencję usługi Microsoft Defender dla punktu końcowego, nie musisz płacić za część licencji Azure Defender końcowej.

Aby potwierdzić rabat, skontaktuj Security Center pomocy technicznej firmy i podaj odpowiedni identyfikator obszaru roboczego, region i informacje o licencji dla każdej odpowiedniej licencji.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Jak mogę z narzędzia do EDR innej firmy?
Pełne instrukcje dotyczące przełączania z rozwiązania punktu końcowego firmy innych niż Microsoft są dostępne w dokumentacji usługi Microsoft Defender dla punktu [końcowego: Omówienie migracji.](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)
  


## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Zarządzanie zaleceniami Azure Security Center:](security-center-recommendations.md)dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.