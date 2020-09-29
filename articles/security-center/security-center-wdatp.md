---
title: Microsoft Defender Advanced Threat Protection — Azure Security Center
description: W tym dokumencie przedstawiono integrację między programem Azure Security Center i zaawansowaną ochroną przed zagrożeniami w usłudze Microsoft Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: 66a04ebec6eb9d7219d4d5db3bdd1dfd780a730f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446004"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender przy użyciu Azure Security Center

Azure Security Center integruje się z usługą [Microsoft Defender Advanced Threat Protection (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) , aby zapewnić kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR).


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Wymaga [usługi Azure Defender](security-center-pricing.md)|
|Obsługiwane maszyny:|![Tak](./media/icons/yes-icon.png) Maszyny platformy Azure z systemem Windows<br>![Tak](./media/icons/yes-icon.png) Komputery z systemem Windows w usłudze Azure Arc|
|Wymagane role i uprawnienia:|Aby włączyć/wyłączyć integrację: **administrator zabezpieczeń** lub **właściciel**<br>Aby wyświetlić alerty MDATP w Security Center: **czytelnik zabezpieczeń**, **czytelnik**, **współautor grupy zasobów**, **właściciel grupy zasobów**, **administrator zabezpieczeń**, **właściciel subskrypcji**lub **współautor subskrypcji**|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne.<br>![Nie](./media/icons/no-icon.png) Klienci korzystający z usług w zatoce w publicznych chmurach platformy Azure<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkcje ATP dotyczące programu Microsoft Defender w Security Center

Usługa Microsoft Defender ATP oferuje następujące informacje:

- **Zaawansowane czujniki wykrywania po naruszeniu**oprogramowania: czujniki usługi Microsoft Defender ATP dla serwerów z systemem Windows zbierają ogromną gamę sygnałów z zachowaniem.

- **Wykrywanie naruszeń oprogramowania oparte na analizie w chmurze**: Usługa Microsoft Defender ATP szybko dostosowuje się do zmieniających się zagrożeń. Używa zaawansowanej analizy i danych Big Data. Usługa Microsoft Defender ATP jest wzmocniona możliwościami Intelligent Security Graph z sygnałami w systemach Windows, Azure i Office w celu wykrywania nieznanych zagrożeń. Zapewnia alerty funkcjonalne i umożliwia szybkie reagowanie na nie.

- **Analiza zagrożeń**: Usługa Microsoft Defender ATP generuje alerty po zidentyfikowaniu narzędzi, technik i procedur osoby atakującej. Używa on danych wygenerowanych przez program Microsoft Threat myśliwych i zespoły ds. zabezpieczeń, uzupełnione o analizy udostępniane przez partnerów.


Dzięki integracji usługi Defender ATP z Azure Security Center można także skorzystać z następujących dodatkowych możliwości:

- **Automatyczne**dołączanie: integracja automatycznie włącza czujnik usługi Microsoft Defender ATP dla serwerów z systemem Windows monitorowanych przez Azure Security Center (chyba że jest uruchomiony system Windows Server 2019).

- **Pojedyncze okienko szkła**: w konsoli Azure Security Center są wyświetlane alerty programu Microsoft Defender ATP. Aby przeprowadzić dalsze badanie, użyj programu Microsoft Defender ATP. Usługa Microsoft Defender ATP udostępnia dodatkowe informacje, takie jak drzewo procesu alertu i wykres incydentu. Możesz również wyświetlić szczegółową oś czasu komputera, która pokazuje każde zachowanie w okresie historycznym przez maksymalnie sześć miesięcy.

    ![Strona Microsoft Defender ATP ze szczegółowymi informacjami o alercie](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Usługa Microsoft Defender ATP w Security Center obsługuje wykrywanie w systemach Windows Server 2016, 2012 R2 i 2008 R2 z dodatkiem SP1. W przypadku maszyn wirtualnych platformy Azure potrzebna jest usługa Azure Defender w ramach subskrypcji, a dla maszyn wirtualnych z systemem innym niż Azure potrzebna jest możliwość włączenia usługi Azure Defender tylko na poziomie obszaru roboczego.

Monitorowanie punktu końcowego serwera przy użyciu tej integracji zostało wyłączone dla klientów usługi Office 365 w zatoce.

## <a name="data-storage-location"></a>Lokalizacja przechowywania danych

W przypadku korzystania z Azure Security Center do monitorowania serwerów usługa Microsoft Defender ATP zostanie utworzona automatycznie. Dane zbierane przez usługę Microsoft Defender ATP są przechowywane w lokalizacji geograficznej dzierżawy, która została zidentyfikowana podczas aprowizacji. Dane klienta w formie pseudonimów mogą być również przechowywane w centralnych systemach magazynowania i przetwarzania w Stany Zjednoczone. 

Po skonfigurowaniu nie można zmienić lokalizacji, w której są przechowywane dane. Jeśli musisz przenieść dane do innej lokalizacji, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu zresetowania dzierżawy.


## <a name="onboard-servers-to-security-center"></a>Dołączanie serwerów do Security Center 

Aby dołączać serwery do Security Center, kliknij przycisk **Przejdź do Azure Security Center do** dołączania serwerów z usługi Microsoft Defender ATP Server dołączanie.

1. W obszarze **Onboarding** dołączania wybierz lub Utwórz obszar roboczy, w którym mają zostać zapisane dane.

2. Jeśli nie widzisz wszystkich obszarów roboczych, może to być spowodowane brakiem uprawnień, upewnij się, że obszar roboczy jest chroniony przez usługę Azure Defender.
    
3. Wybierz pozycję **Dodaj serwery** , aby wyświetlić instrukcje dotyczące instalowania agenta log Analytics. 

4. Po dołączeniu można monitorować maszyny w [spisie zasobów](asset-inventory.md).

   ![Dołączanie komputerów](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Włącz integrację z programem Microsoft Defender ATP

Aby sprawdzić, czy integracja z usługą Microsoft Defender ATP jest włączona, wybierz pozycję Cennik usługi **Security Center**  >  **& ustawienia** > wybierz subskrypcję.

W tym miejscu można zobaczyć aktualnie włączone integracje.

  ![Strona ustawień wykrywania zagrożeń Azure Security Center z włączoną integracją z usługą Microsoft Defender ATP](media/security-center-wdatp/enable-integrations.png)

- Jeśli usługa Azure Defender została już włączona, nie są wymagane żadne dalsze działania. Azure Security Center automatycznie dołączać serwery do programu Microsoft Defender ATP. Dołączanie może potrwać do 24 godzin.

- Jeśli serwery nie zostały dołączone do Azure Security Center, Dołącz je do Azure Security Center i Włącz usługę Azure Defender w zwykły sposób.

- W przypadku dołączania serwerów za pomocą programu Microsoft Defender ATP:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki dotyczące [odłączania maszyn serwera](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Dołącz te serwery do Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Dostęp do portalu Microsoft Defender ATP

1. Postępuj zgodnie z instrukcjami w temacie [Przypisywanie dostępu użytkowników do portalu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Sprawdź, czy masz serwer proxy lub zaporę blokującą ruch anonimowy. Czujnik usługi Defender ATP nawiązuje połączenie z kontekstem systemowym, więc należy zezwolić na ruch anonimowy. Aby zapewnić niezakłócony dostęp do portalu usługi Microsoft Defender ATP, postępuj zgodnie z instrukcjami zawartymi w temacie [Włączanie dostępu do adresów URL usługi Microsoft Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testowanie funkcji

Aby wygenerować niegroźny alert dotyczący testu Microsoft Defender ATP:

1. Utwórz folder "C:\test-MDATP-test".

1. Użyj Pulpit zdalny, aby uzyskać dostęp do maszyny wirtualnej z systemem Windows Server 2012 R2 lub z maszyną wirtualną z systemem Windows Server 2016. Otwórz okno wiersza polecenia.

1. W wierszu polecenia skopiuj i uruchom następujące polecenie. Okno wiersza polecenia zostanie zamknięte automatycznie.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Okno wiersza polecenia z powyższym poleceniem](media/security-center-wdatp/image4.jpeg)

1. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowy Alert na pulpicie nawigacyjnym Azure Security Center i portalu Microsoft Defender ATP. Ten alert może potrwać kilka minut.

1. Aby sprawdzić alert w Security Center, przejdź do **alertów zabezpieczeń**  >  **podejrzany wiersz polecenia programu PowerShell**.

1. W oknie badanie wybierz link, aby przejść do portalu usługi Microsoft Defender ATP.

## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md): informacje na temat sposobu, w jaki zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.