---
title: Używanie licencji usługi Microsoft Defender dla punktu końcowego dołączonej do Azure Security Center
description: Dowiedz się więcej na temat usługi Microsoft Defender dla punktu końcowego i wdrażania jej z Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829894"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Ochrona punktów końcowych za Security Center rozwiązania EDR firmy Microsoft: Microsoft Defender dla punktu końcowego

Usługa Microsoft Defender dla punktu końcowego to całościowe rozwiązanie zabezpieczeń punktu końcowego dostarczane przez chmurę. Jego główne funkcje to:

- Zarządzanie lukami w zabezpieczeniach oparte na ryzyku i ocena ich 
- Zmniejszanie obszaru ataków
- Ochrona oparta na zachowaniu i oparta na chmurze
- Wykrywanie punktów końcowych i reagowanie na nie (EDR)
- Automatyczne badanie i korygowanie
- Zarządzane usługi łowieckie

> [!TIP]
> Pierwotnie był uruchomiony jako **Windows Defender ATP.** Nazwa tego produktu wykrywania i reagowania w punkcie końcowym (EDR) została zmieniona w 2019 r. na **Microsoft Defender ATP.**
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

- **Analiza zagrożeń**. Program Defender for Endpoint generuje alerty, gdy identyfikuje narzędzia, techniki i procedury osoby atakującej. Używa ona danych generowanych przez zespoły ds. zagrożeń i zespoły ds. zabezpieczeń firmy Microsoft, które są rozszerzane przez analizę dostarczaną przez partnerów.

Dzięki integracji usługi Defender for Endpoint z Security Center, będziesz korzystać z następujących dodatkowych możliwości:

- **Automatyczne dołączanie.** Security Center automatycznie włącza czujnik usługi Microsoft Defender dla punktu końcowego dla wszystkich serwerów z systemem Windows monitorowanych przez Security Center.

- **Pojedyncze okienko zeszytowe**. W Security Center zostanie wyświetlona usługa Microsoft Defender dla alertów punktu końcowego. Aby dokładniej zbadać ten temat, użyj usługi Microsoft Defender dla własnych stron portalu punktu końcowego, na których zobaczysz dodatkowe informacje, takie jak drzewo procesu alertów i wykres zdarzeń. Możesz również wyświetlić szczegółową oś czasu maszyny, która pokazuje każde zachowanie w okresie historycznym maksymalnie sześciu miesięcy.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Usługa Microsoft Defender dla własnego Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Jakie są wymagania dotyczące dzierżawy usługi Microsoft Defender dla punktu końcowego?

Gdy używasz usługi Azure Security Center do monitorowania serwerów, dzierżawa usługi Microsoft Defender dla punktu końcowego jest tworzona automatycznie. 

- **Lokalizacja:** Dane zbierane przez usługę Defender dla punktu końcowego są przechowywane w lokalizacji geograficznej dzierżawy, jak określono podczas aprowizacji. Dane klienta — w postaci pseudonimizowanej — mogą być również przechowywane w centralnych systemach magazynowania i przetwarzania w Stany Zjednoczone. Po skonfigurowaniu lokalizacji nie można jej zmienić. Jeśli masz własną licencję usługi Microsoft Defender dla punktu końcowego i musisz przenieść dane do innej lokalizacji, skontaktuj się z Pomoc techniczna Microsoft w celu zresetowania dzierżawy.
- **Przenoszenie subskrypcji:** Jeśli subskrypcja platformy Azure została przeniesiona między dzierżawami platformy Azure, przed wdrożeniem usługi Defender dla punktu końcowego Security Center ręczne przygotowanie. Aby uzyskać szczegółowe informacje, skontaktuj [się z pomocą techniczną firmy Microsoft.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Włączanie integracji usługi Microsoft Defender dla punktu końcowego

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że maszyna spełnia wymagania dotyczące usługi Defender dla punktu końcowego:

1. Upewnij się, że maszyna jest połączona z platformą Azure zgodnie z wymaganiami:

    - W **przypadku serwerów** z systemem Windows skonfiguruj ustawienia sieciowe opisane w te [tematach Configure device proxy and Internet connectivity settings (Konfigurowanie serwera proxy urządzenia i ustawień łączności internetowej)](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
    - W **przypadku maszyn lokalnych połącz** ją z maszynami Azure Arc jak wyjaśniono w tece Łączenie maszyn hybrydowych z Azure Arc [serwerami z obsługą usługi](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - W przypadku maszyn z systemami **Windows Server 2019** [i Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) upewnij się, że na maszynach jest uruchomiony agent usługi Log Analytics i że masz rozszerzenie MicrosoftMonitoringAgent.
    
1. Włącz **Azure Defender dla serwerów**. Zobacz [Szybki start: włączanie Azure Defender](enable-azure-defender.md).
1. Jeśli masz już licencję i wdrożono usługę Microsoft Defender for Endpoints na serwerach, usuń ją przy użyciu procedury opisanej w te tematze Odłącz serwery [z systemem Windows.](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)
1. Jeśli subskrypcja została przeniesiona między dzierżawami platformy Azure, wymagane są również pewne kroki ręcznego przygotowania. Aby uzyskać szczegółowe informacje, skontaktuj [się z pomocą techniczną firmy Microsoft.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Włączanie integracji
1. Z Security Center wybierz pozycję Cennik **&** i wybierz subskrypcję, którą chcesz zmienić.
1. Wybierz **pozycję Wykrywanie zagrożeń.**
1. Wybierz pozycję **Zezwalaj u programowi Microsoft Defender dla punktu końcowego na** dostęp do moich danych, a następnie wybierz **pozycję Zapisz.**

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Włączanie integracji między usługą Azure Security Center a rozwiązaniem EDR firmy Microsoft, Microsoft Defender dla punktu końcowego":::

    Azure Security Center automatycznie dołącza serwery do usługi Microsoft Defender dla punktu końcowego. Dołączanie może potrwać do 24 godzin.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Uzyskiwanie dostępu do portalu usługi Microsoft Defender dla punktu końcowego

1. Upewnij się, że konto użytkownika ma niezbędne uprawnienia. Aby dowiedzieć się [więcej, zobacz Assign user access to Centrum zabezpieczeń usługi Microsoft Defender (Przypisywanie](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)dostępu Centrum zabezpieczeń usługi Microsoft Defender ).

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
- [Jak mogę przełączać się z narzędzia do EDR innej firmy?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Jakie są wymagania licencyjne dotyczące usługi Microsoft Defender dla punktu końcowego?
Program Defender for Endpoint jest dołączony bez dodatkowych kosztów do usługi **Azure Defender dla serwerów**. Alternatywnie można kupić osobno dla 50 maszyn lub więcej.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Jeśli mam już licencję usługi Microsoft Defender dla punktu końcowego, czy mogę uzyskać rabat na Azure Defender?
Jeśli masz już licencję usługi Microsoft Defender dla punktu końcowego, nie musisz płacić za część licencji Azure Defender końcowej.

Aby potwierdzić rabat, skontaktuj Security Center pomocy technicznej firmy i podaj odpowiedni identyfikator obszaru roboczego, region i informacje o licencji dla każdej odpowiedniej licencji.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Jak mogę przełączać się z narzędzia do EDR innej firmy?
Pełne instrukcje dotyczące przełączania z rozwiązania punktu końcowego firmy innych niż Microsoft są dostępne w dokumentacji usługi Microsoft Defender dla punktu [końcowego: Omówienie migracji](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Zarządzanie zaleceniami Azure Security Center:](security-center-recommendations.md)dowiedz się, jak rekomendacje pomagają chronić zasoby platformy Azure.