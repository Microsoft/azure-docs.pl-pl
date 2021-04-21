---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany Azure Security Center, które być może trzeba będzie wiedzieć i dla których może być konieczne zaplanowanie
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751002"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne nadchodzące zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą zostać znacząco zmodyfikowane przed ich wydaniem na rynek, jeśli kiedykolwiek wcześniej. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, jawnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane do Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak twój bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je na stronie Co nowego w [programie Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

| Planowana zmiana                                                                                                                                                        | Szacowana data zmiany |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 zaleceń dotyczących przechodzenia między mechanizmami kontroli zabezpieczeń](#21-recommendations-moving-between-security-controls)                                                           | Kwiecień 2021 r.                |
| [Dwa zalecenia z kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Kwiecień 2021 r.                |
| [Starsza implementacja standardu ISO 27001 jest zastępowana nowym standardem ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Czerwiec 2021 r.                 |
| [Zalecenia z usług AWS zostaną wydane w wersji ogólnie dostępnej](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Sierpień** 2021 r.           |
| [Ulepszenia zaleceń dotyczących klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | II kwartał 2021 r.                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 zaleceń dotyczących przechodzenia między mechanizmami kontroli zabezpieczeń 

**Szacowana data zmiany:** Kwiecień 2021 r.

Poniższe zalecenia są przenoszone do innej kontroli zabezpieczeń. Mechanizmy kontroli zabezpieczeń to logiczne grupy powiązanych zaleceń dotyczących zabezpieczeń, odzwierciedlające narażone na ataki ataki. Dzięki temu można zagwarantować, że każde z tych zaleceń będzie mieć najbardziej odpowiednią kontrolę, aby osiągnąć cel. 

Dowiedz się, które zalecenia znajdują się w poszczególnych mechanizmach kontroli zabezpieczeń, oraz zapoznaj się z ich zaleceniami.

|Zalecenie |Zmiana i wpływ  |
|---------|---------|
|Na serwerach SQL należy włączyć ocenę luk w zabezpieczeniach<br>Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL<br>Należy skorygować nowe luki w zabezpieczeniach baz danych SQL<br>Należy skorygować luki w zabezpieczeniach baz danych SQL na maszynach wirtualnych     |Przechodzenie z luk w zabezpieczeniach korygowania (warte 6 punktów)<br>aby skorygować konfiguracje zabezpieczeń (warte 4 punkty).<br>W zależności od środowiska te zalecenia będą miały mniejszy wpływ na ocenę.|
|Do subskrypcji powinien być przypisany więcej niż jeden właściciel<br>Zmienne konta usługi Automation powinny być szyfrowane<br>Urządzenia IoT — inspekcja procesu zatrzymała wysyłanie zdarzeń<br>Urządzenia IoT — niepowodzenie weryfikacji linii bazowej systemu operacyjnego<br>Urządzenia IoT — wymagane uaktualnienie pakietu szyfrowania TLS<br>Urządzenia IoT — otwieranie portów na urządzeniu<br>Urządzenia IoT — znaleziono zasady zapory w jednym z łańcuchów<br>Urządzenia IoT — znaleziono regułę zapory w łańcuchu danych wejściowych<br>Urządzenia IoT — znaleziono regułę zapory w łańcuchu danych wyjściowych<br>Dzienniki diagnostyczne w IoT Hub powinny być włączone<br>Urządzenia IoT — agent wysyła nie w pełni w pełni komunikaty<br>Urządzenia IoT — domyślne zasady filtrowania adresów IP powinny mieć wartość Odmów<br>Urządzenia IoT — duży zakres adresów IP reguły filtrowania adresów IP<br>Urządzenia IoT — interwały komunikatów agenta i rozmiar należy dostosować<br>Urządzenia IoT — identyczne poświadczenia uwierzytelniania<br>Urządzenia IoT — inspekcja procesu zatrzymała wysyłanie zdarzeń<br>Urządzenia IoT — należy naprawić konfigurację linii bazowej systemu operacyjnego|Przejście do **implementacji najlepszych rozwiązań w zakresie zabezpieczeń.**<br>Gdy zalecenie zostanie przeniesiony do kontroli zabezpieczeń Implementuj najlepsze rozwiązania w zakresie zabezpieczeń, która nie ma żadnych punktów, zalecenie nie będzie już miało wpływu na ocenę bezpieczeństwa.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa zalecenia z kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe

**Szacowana data zmiany:** Kwiecień 2021 r.

Następujące dwa zalecenia są przestarzałe:

-  Wersja systemu operacyjnego powinna być aktualizowana dla ról usługi w chmurze — domyślnie platforma Azure okresowo aktualizuje system operacyjny gościa do najnowszego obsługiwanego obrazu z rodziny systemów operacyjnych określonego w konfiguracji usługi (cscfg), na przykład Windows Server 2016.
- **Usługi Kubernetes należy** uaktualnić do wersji Kubernetes, która nie jest narażona na zagrożenia — oceny tego zalecenia nie są tak szerokie, jak chcemy. Bieżąca wersja tego zalecenia zostanie ostatecznie zastąpiona rozszerzoną wersją, która jest lepiej dostosowana do potrzeb klientów w zakresie zabezpieczeń.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Starsza implementacja standardu ISO 27001 jest zastępowana nowym standardem ISO 27001:2013

Starsza implementacja standardu ISO 27001 zostanie usunięta z pulpitu Security Center zgodności z przepisami firmy. Jeśli śledzisz zgodność iso 27001 z programem Security Center, dołączasz nowy standard ISO 27001:2013 dla wszystkich odpowiednich grup zarządzania lub subskrypcji, a bieżąca starsza wersja standardu ISO 27001 zostanie wkrótce usunięta z pulpitu nawigacyjnego.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center pulpit nawigacyjny zgodności z przepisami z komunikatem o usunięciu starszej implementacji standardu ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Zalecenia z usług AWS zostaną wydane w wersji ogólnie dostępnej

**Szacowana data zmiany:** Sierpień 2021 r.

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Rekomendacje pochodzące z usługi AWS Security Hub są w wersji zapoznawczej od momentu wprowadzenia łączników w chmurze. Rekomendacje oflagowane jako wersja zapoznawcza nie są uwzględniane w obliczeniach wskaźnika bezpieczeństwa, ale nadal powinny być korygowane wszędzie tam, gdzie jest to możliwe, tak aby po zakończeniu okresu wersji zapoznawczej współtworzą one twój wynik. 

W przypadku tej zmiany dwa zestawy zaleceń dotyczących usług AWS zostaną przejdą do poszczególnych części:

- [Kontrolki zabezpieczeń PCI DSS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Mechanizmy kontroli cis AWS Foundations Benchmark usługi Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Gdy są one dostępne w wersji gałęzie, a oceny są uruchamiane na zasobach platformy AWS, wyniki będą miały wpływ na połączony wynik bezpieczeństwa dla wszystkich zasobów w chmurze hybrydowej i wielu.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Ulepszenia zaleceń dotyczących klasyfikacji danych SQL

**Szacowana data zmiany:** II kwartał 2021 r.

Zalecenie Poufne dane w bazach danych  **SQL** powinny zostać sklasyfikowane w kontrolce Zabezpieczeń zastosuj klasyfikację danych. Zostanie ona zastąpiona nową wersją, która jest lepiej dostosowana do strategii klasyfikacji danych firmy Microsoft. W rezultacie identyfikator zalecenia również się zmieni (obecnie jest to b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o wszystkich najnowszych zmianach produktu, zobacz Co nowego w [Azure Security Center?](release-notes.md).