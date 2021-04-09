---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773576"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

| Planowana zmiana                                                                                                                                                        | Szacowana data zmiany |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Marzec 2021                |
| [Wycofanie z 11 alertów usługi Azure Defender](#deprecation-of-11-azure-defender-alerts)                                                                                   | Marzec 2021                |
| [21 zaleceń przenoszonych między kontrolkami zabezpieczeń](#21-recommendations-moving-between-security-controls)                                                           | Kwiecień 2021                |
| [Dwa dalsze zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | Kwiecień 2021                |
| [Zalecenia z usługi AWS zostaną wydane w celu uzyskania ogólnej dostępności (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | Kwiecień 2021                |
| [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe 

**Szacowana data zmiany:** Marzec 2021

Następujące dwa zalecenia zaplanowano jako przestarzałe w lutym 2021:

- **Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny**. Może to skutkować niewielkim wpływem na bezpieczny wynik.
- **Na maszynach należy zainstalować agenta monitorowania**. To zalecenie odnosi się tylko do maszyn lokalnych, a część jej logiki zostanie przetransferowana do innego zalecenia, **log Analytics na maszynach należy rozwiązać problemy z kondycją agenta**. Może to skutkować niewielkim wpływem na bezpieczny wynik.

Zalecamy sprawdzenie konfiguracji eksportu ciągłego i automatyzacji przepływu pracy, aby sprawdzić, czy te zalecenia są zawarte w nich. Ponadto należy odpowiednio zaktualizować wszystkie pulpity nawigacyjne lub inne narzędzia do monitorowania, które mogą z nich korzystać.

Więcej informacji na temat tych zaleceń znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).

### <a name="deprecation-of-11-azure-defender-alerts"></a>Wycofanie z 11 alertów usługi Azure Defender

**Szacowana data zmiany:** Marzec 2021

W następnym miesiącu jedenaście alertów usługi Azure Defender wymienionych poniżej zostanie wycofana.

- Nowe alerty zastępują te dwa alerty i zapewniają lepsze pokrycie:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | WERSJA zapoznawcza — wykryto uruchomienie funkcji "Get-AzureDomainInfo" zestawu narzędzi mikroserii |
    | ARM_MicroBurstRunbook    | WERSJA zapoznawcza — wykryto uruchomienie funkcji "Get-AzurePasswords" zestawu narzędzi mikroserii  |
    |                          |                                                                          |

- Te dziewięć alertów odnoszą się do łącznika Azure Active Directory Identity Protection, który został już uznany za przestarzały:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Nieznane właściwości logowania |
    | AnonymousLogin      | Anonimowy adres IP          |
    | InfectedDeviceLogin | Połączony adres IP złośliwego oprogramowania     |
    | ImpossibleTravel    | Nietypowa podróż               |
    | MaliciousIP         | Złośliwy adres IP          |
    | LeakedCredentials   | Ujawnione poświadczenia            |
    | PasswordSpray       | Rozpylanie hasła                |
    | LeakedCredentials   | Analiza zagrożeń usługi Azure AD  |
    | AADAI               | Usługa Azure AD AI                   |
    |                     |                               |
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 zaleceń przenoszonych między kontrolkami zabezpieczeń 

**Szacowana data zmiany:** Kwiecień 2021

Poniższe zalecenia są przenoszone do innej kontroli zabezpieczeń. Funkcje kontroli zabezpieczeń są logicznymi grupami powiązanych zaleceń dotyczących zabezpieczeń i odzwierciedlają podatne na ataki. Ten ruch gwarantuje, że każde z tych zaleceń znajduje się w najbardziej odpowiedniej kontrolce, aby zaspokoić jej cel. 

Dowiedz się, które zalecenia znajdują się w każdej kontroli zabezpieczeń w obszarze kontrola zabezpieczeń i ich zalecenia.

|Zalecenie |Zmiana i wpływ  |
|---------|---------|
|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL<br>Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL<br>Luki w zabezpieczeniach baz danych SQL należy skorygować nowe<br>Luki w zabezpieczeniach baz danych SQL w maszynach wirtualnych należy skorygować     |Przechodzenie od rozwiązywania luk w zabezpieczeniach (warte 6 punktów)<br>Aby skorygować konfiguracje zabezpieczeń (warta 4 punkty).<br>W zależności od środowiska te zalecenia będą miały obniżony wpływ na ocenę.|
|Do subskrypcji powinien być przypisany więcej niż jeden właściciel<br>Zmienne konta usługi Automation powinny być szyfrowane<br>Urządzenia IoT — proces inspekcji zatrzymał wysyłanie zdarzeń<br>Urządzenia IoT — niepowodzenie walidacji linii bazowej systemu operacyjnego<br>Urządzenia IoT — wymagana jest aktualizacja pakietu szyfrowania TLS<br>Urządzenia IoT — otwieranie portów na urządzeniu<br>Urządzenia IoT — w jednym z łańcuchów znaleziono zasady zapory.<br>Znaleziono regułę zapory ograniczającą urządzenia IoT w łańcuchu wejściowym<br>Znaleziono regułę zapory ograniczającą urządzenia IoT w łańcuchu wyjściowym<br>Dzienniki diagnostyczne w IoT Hub powinny być włączone<br>Urządzenia IoT — wysyłanie nieużywanych komunikatów przez agenta<br>Urządzenia IoT — domyślne zasady filtru IP powinny być Odmów<br>Urządzenia IoT — duży zakres adresów IP reguły filtru IP<br>Urządzenia IoT — należy dostosować interwały i rozmiary komunikatów agenta<br>Urządzenia IoT — identyczne poświadczenia uwierzytelniania<br>Urządzenia IoT — proces objęty inspekcją zatrzymał wysyłanie zdarzeń<br>Urządzenia IoT — konfiguracja linii bazowej systemu operacyjnego (OS) powinna być stała|Przechodzenie do **implementacji najlepszych** rozwiązań w zakresie zabezpieczeń.<br>Gdy zalecenie przejdzie do kontroli zabezpieczeń zaimplementowanie najlepszych rozwiązań w zakresie zabezpieczeń, która nie ma żadnych punktów, zalecenie nie ma już wpływu na swój Bezpieczny wynik.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa dalsze zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe

**Szacowana data zmiany:** Kwiecień 2021

Następujące dwa zalecenia są przestarzałe:

- **Wersja systemu operacyjnego powinna zostać zaktualizowana dla ról usługi w chmurze** — domyślnie platforma Azure okresowo AKTUALIZUJE system operacyjny gościa do najnowszej obsługiwanego obrazu w rodzinie systemów operacyjnych, która została określona w konfiguracji usługi (. cscfg), takiej jak Windows Server 2016.
- **Usługi Kubernetes należy uaktualnić do niezagrożonej wersji Kubernetes** — oceny tego zalecenia nie są tak samo szerokie jak te, które mają być stosowane. Aktualna wersja tego zalecenia zostanie ostatecznie zastąpiona ulepszoną wersją, która jest lepiej wyrównana do potrzeb związanych z bezpieczeństwem klientów.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Zalecenia z usługi AWS zostaną wydane w celu uzyskania ogólnej dostępności (GA)

**Szacowana data zmiany:** Kwiecień 2021

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Zalecenia pochodzące z Centrum zabezpieczeń AWS były dostępne w wersji zapoznawczej od momentu wprowadzenia łączników w chmurze. Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku, ale powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, dzięki czemu po zakończeniu okresu zapoznawczego dojdzie do oceny.

Dzięki tej zmianie dwa zestawy zaleceń AWS zostaną przeniesione do wersji ogólnie dostępnej:

- [Kontrolki PCI DSS Centrum zabezpieczeń](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Formanty testu porównawczego w centrum zabezpieczeń CIS AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Gdy są one dostępne, a oceny są uruchamiane w swoich zasobach AWS, wyniki będą miały wpływ na łączny, bezpieczny wynik dla wszystkich zasobów chmurowych i hybrydowych. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Ulepszenia zalecenia klasyfikacji danych SQL

**Szacowana data zmiany:** Q2 2021

Dane dotyczące rekomendacji **w bazach danych SQL powinny zostać sklasyfikowane** w kontroli zabezpieczeń **Zastosuj klasyfikację danych** w nowej wersji, która jest lepiej wyrównana przy użyciu strategii klasyfikacji danych firmy Microsoft. W efekcie identyfikator zalecenia również zostanie zmieniony (obecnie jest to b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).