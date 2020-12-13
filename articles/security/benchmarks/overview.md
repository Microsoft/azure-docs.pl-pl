---
title: Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2
description: Omówienie usługi Azure Security test w wersji 2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369161"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Omówienie testu porównawczego zabezpieczeń platformy Azure (wersja 2)

Produkt Azure Security test (ASB) zawiera najlepsze rozwiązania i zalecenia ułatwiające zwiększenie bezpieczeństwa obciążeń, danych i usług na platformie Azure.

Ten test porównawczy jest częścią zestawu całościowej wskazówki dotyczącego zabezpieczeń, który obejmuje również:

- **Struktura wdrażania w chmurze** — wskazówki dotyczące zabezpieczeń, w tym [strategia](/azure/cloud-adoption-framework/strategy/define-security-strategy), [role i obowiązki](/azure/cloud-adoption-framework/organize/cloud-security), [najlepsze rozwiązania dotyczące zabezpieczeń i 10 systemu Azure](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)oraz [implementację referencyjną](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Azure Well-Architected Framework** — wskazówki dotyczące [zabezpieczania obciążeń](/assessments/?mode=pre-assessment&session=local) na platformie Azure.
- **Najlepsze rozwiązania w zakresie zabezpieczeń firmy Microsoft** — [zalecenia](/security/compass/microsoft-security-compass-introduction) dotyczące przykładów na platformie Azure.

 Test porównawczy zabezpieczeń platformy Azure koncentruje się na obszarach formantów skoncentrowanych na chmurze. Te kontrolki są spójne z dobrze znanymi standardami zabezpieczeń, takimi jak te opisane w formantach Center for Internet Security (CIS), wersja 7,1 i National Institute of Standards and Technology (NIST) SP 800-53.
Do testu porównawczego zabezpieczeń platformy Azure należą następujące kontrolki:

| Domeny kontrolek ASB | Opis 
|--|--|
| [&nbsp;Zabezpieczenia sieci &nbsp; (NS)](security-controls-v2-network-security.md) | Zabezpieczenia sieci obejmują kontrolki zabezpieczające i chroniące sieci platformy Azure, w tym Zabezpieczanie sieci wirtualnych, ustanawianie prywatnych połączeń, zapobieganie i łagodzenie ataków zewnętrznych oraz Zabezpieczanie systemu DNS. |
| [Zarządzanie tożsamościami &nbsp; &nbsp;](security-controls-v2-identity-management.md) | Zarządzanie tożsamościami obejmuje kontrolki służące do nawiązywania bezpiecznej kontroli tożsamości i dostępu przy użyciu Azure Active Directory, w tym korzystania z logowania jednokrotnego, silnego uwierzytelniania, tożsamości zarządzanych (i zasad usługi) dla aplikacji, dostępu warunkowego i monitorowania anomalii kont. |
| [&nbsp;Dostęp uprzywilejowany &nbsp; (PA)](security-controls-v2-privileged-access.md) | Dostęp uprzywilejowany obejmuje kontrolki do ochrony uprzywilejowanego dostępu do dzierżawy i zasobów platformy Azure, w tym zakresu kontroli w celu ochrony modelu administracyjnego, kont administracyjnych i stacji roboczych uprzywilejowanego dostępu przed zamierzonym i przypadkowym ryzykiem. |
| [&nbsp;Ochrona danych &nbsp; (DP)](security-controls-v2-data-protection.md) | Ochrona danych obejmuje kontrolę nad ochroną danych w czasie spoczynku, w tranzycie oraz za pomocą autoryzowanych mechanizmów dostępu, w tym odnajdywania, klasyfikowania, ochrony i monitorowania poufnych zasobów danych przy użyciu funkcji kontroli dostępu, szyfrowania i rejestrowania na platformie Azure. |
| [&nbsp;Zarządzanie zasobami &nbsp; (am)](security-controls-v2-asset-management.md) | Zarządzanie zasobami obejmuje kontrolki zapewniające widoczność i nadzór nad zasobami platformy Azure, w tym zalecenia dotyczące uprawnień dla personelu zabezpieczeń, bezpieczeństwa dostępu do spisu zasobów oraz zarządzania zatwierdzeniami usług i zasobów (spis, śledzenie i poprawne). |
| [Rejestrowanie &nbsp; i &nbsp; &nbsp; wykrywanie zagrożeń (lt)](security-controls-v2-logging-threat-detection.md) | Rejestrowanie i wykrywanie zagrożeń obejmują kontrolę w zakresie wykrywania zagrożeń na platformie Azure oraz włączanie, zbieranie i przechowywanie dzienników inspekcji dla usług platformy Azure, w tym Włączanie procesów wykrywania, badania i korygowania przy użyciu kontrolek w celu generowania alertów o wysokiej jakości z natywnym wykrywaniem zagrożeń w usługach platformy Azure. obejmuje to również zbieranie dzienników z Azure Monitor, scentralizowanie analizy zabezpieczeń dzięki wskaźnikom danych platformy Azure, synchronizacji czasu i przechowywaniu dzienników. |
| [Odpowiedź na zdarzenia &nbsp; &nbsp; (IR)](security-controls-v2-incident-response.md) | Reagowanie na zdarzenia obejmuje kontrolki dotyczące cyklu życia, wykrywania i analizy, zawierania i działania po zdarzeniu, w tym za pomocą usług platformy Azure, takich jak Azure Security Center i Wskaźnikowanie, w celu zautomatyzowania procesu reagowania na zdarzenia. |
| [Stan &nbsp; i &nbsp; Zarządzanie lukami w zabezpieczeniach &nbsp; &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | Zarządzanie stanami i lukami koncentruje się na kontroli oceny i ulepszaniu usługi Azure Security stan, w tym o skanowaniu luk w zabezpieczeniach, testowaniu i korygowaniu konfiguracji zabezpieczeń, a także śledzeniu, raportowaniu i korekcji w zasobach platformy Azure. |
| [Zabezpieczenia punktów końcowych &nbsp; &nbsp;](security-controls-v2-endpoint-security.md) | Zabezpieczenia punktu końcowego obejmują kontrolki wykrywania i reagowania punktów końcowych, w tym używanie wykrywania i reagowania punktów końcowych (EDR) oraz usługi ochrony przed złośliwym oprogramowaniem dla punktów końcowych w środowiskach platformy Azure. |
| [Kopia zapasowa &nbsp; i &nbsp; odzyskiwanie &nbsp; (br)](security-controls-v2-backup-recovery.md) | Tworzenie kopii zapasowych i odzyskiwanie obejmuje kontrolki, aby zapewnić, że kopie zapasowe danych i konfiguracji w różnych warstwach usług są wykonywane, weryfikowane i chronione. |
| [Zarządzanie &nbsp; i &nbsp; strategia &nbsp; (GS)](security-controls-v2-governance-strategy.md) | Kierownictwo i strategia zapewniają wskazówki dotyczące zapewnienia spójnej strategii zabezpieczeń i udokumentowanego podejścia do nadzoru w zakresie zabezpieczeń i zapewnienia bezpieczeństwa, w tym ustanawiania ról i obowiązków związanych z różnymi funkcjami zabezpieczeń w chmurze, ujednoliconą strategią techniczną oraz obsługą zasad i standardów. |

## <a name="azure-security-benchmark-recommendations"></a>Zalecenia dotyczące usługi Azure Security test

Każde zalecenie zawiera następujące informacje:

- **Identyfikator platformy Azure**: Identyfikator testu porównawczego zabezpieczeń platformy Azure, który odpowiada zalecenia.
- Usługi **CIS Controls v 7.1 ID (s)**: WNP kontroluje kontrolki v 7.1, które odpowiadają temu rekomendacji.
- **NIST sp 800-53 R4 ID (s)**: Instytut nist SP 800-53 R4 (umiarkowane), który odpowiada temu rekomendacji.
- **Szczegóły**: uzasadnienie zalecenia i linki do wskazówek dotyczących sposobu jego wdrożenia. Jeśli zalecenie jest obsługiwane przez Azure Security Center, te informacje będą również wyświetlane.
- **Odpowiedzialność**: niezależnie od tego, czy klient, dostawca usług lub obie te osoby są odpowiedzialne za wdrożenie tego zalecenia. Obowiązki związane z zabezpieczeniami są udostępniane w chmurze publicznej. Niektóre funkcje kontroli zabezpieczeń są dostępne tylko dla dostawcy usług w chmurze, dlatego dostawca jest odpowiedzialny za adresowanie. Są to ogólne obserwacje — w przypadku niektórych poszczególnych usług odpowiedzialność będzie różna od tego, co jest wymienione w teście zabezpieczeń platformy Azure. Te różnice opisano w podstawowych zaleceniach dotyczących poszczególnych usług.
- **Uczestnicy zabezpieczeń klientów**: [funkcje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) w organizacji klienta, którzy mogą być odpowiedzialni, zobowiązani lub konsultowane w celu zapewnienia odpowiedniej kontroli. Może się to różnić od organizacji do organizacji, w zależności od struktury organizacji zabezpieczeń firmy oraz ról i obowiązków skonfigurowanych dla zabezpieczeń platformy Azure.

> [!NOTE]
> Mapowania formantów między testami ASB i branżowymi (takimi jak NIST i CIS) wskazują tylko, że konkretna funkcja platformy Azure może być używana do całkowitego lub częściowo rozwiązania wymagania kontroli zdefiniowanego w NIST lub WNP. Należy pamiętać, że taka implementacja nie musi być przetłumaczyć na pełną zgodność odpowiedniej kontroli w WNP lub NIST.

Poznamy Twoją szczegółową opinię i aktywne uczestnictwo w pracy z usługą Azure Security test. Jeśli chcesz podać dane wejściowe zespołu usługi Azure Security test, Wypełnij formularz na stronie https://aka.ms/AzSecBenchmark

## <a name="download"></a>Pobierz

Test porównawczy zabezpieczeń platformy Azure można pobrać w [formacie arkusza kalkulacyjnego](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="next-steps"></a>Następne kroki 
- Zobacz pierwszą kontrolę zabezpieczeń: [zabezpieczenia sieci](security-control-network-security.md)
- Przeczytaj [wprowadzenie do usługi Azure Security test](introduction.md)
- Poznaj [podstawy zabezpieczeń platformy Azure](../fundamentals/index.yml)