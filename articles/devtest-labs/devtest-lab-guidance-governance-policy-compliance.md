---
title: Zasady i zgodność firmy w Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące zarządzania zasadami firmy i zgodnością infrastruktury Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592351"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Zarządzanie infrastrukturą Azure DevTest Labs — zasady i zgodność firmy
Ten artykuł zawiera wskazówki dotyczące zarządzania zasadami firmy i zgodnością infrastruktury Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Prywatne repozytorium artefaktów Public a Private

### <a name="question"></a>Pytanie
Kiedy organizacja ma używać publicznego repozytorium artefaktów a repozytorium artefaktów prywatnych w usłudze DevTest Labs?

### <a name="answer"></a>Odpowiedź
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zawiera początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga w szybkim wdrożeniu bez konieczności inwestowania w odtworzenie popularnych narzędzi programistycznych i dodatków. Możesz wybrać wdrożenie własnego repozytorium prywatnego. Można równolegle używać publicznego i prywatnego repozytorium. Możesz również wyłączyć repozytorium publiczne. Kryteria służące do wdrażania repozytorium prywatnego powinny mieć następujące pytania i zagadnienia:

- Czy organizacja ma wymóg posiadania licencjonowanego oprogramowania firmowego jako części swojej oferty DevTest Labs? Jeśli odpowiedź ma wartość tak, należy utworzyć prywatne repozytorium.
- Czy organizacja opracowuje niestandardowe oprogramowanie, które zapewnia konkretną operację, która jest wymagana w ramach ogólnego procesu aprowizacji? Jeśli odpowiedź ma wartość tak, należy wdrożyć prywatne repozytorium.
- Jeśli zasady ładu organizacji wymagają izolacji, a zewnętrzne repozytoria nie są objęte bezpośrednim zarządzaniem konfiguracją przez organizację, należy wdrożyć prywatne repozytorium artefaktów. W ramach tego procesu można skopiować początkową kopię repozytorium publicznego i zintegrować ją z repozytorium prywatnym. Następnie repozytorium publiczne można wyłączyć, aby nikt nie mógł uzyskać do niego dostępu. Takie podejście wymusza, aby wszyscy użytkownicy w organizacji mieli tylko jedno repozytorium zatwierdzone przez organizację i zminimalizować dryf konfiguracji.

### <a name="single-repository-or-multiple-repositories"></a>Pojedyncze repozytorium lub wiele repozytoriów 

### <a name="question"></a>Pytanie
Czy należy zaplanować organizację dla jednego repozytorium czy zezwolić na wiele repozytoriów?

### <a name="answer"></a>Odpowiedź
W ramach ogólnej strategii zarządzania zarządzaniem i konfiguracją organizacji zalecamy używanie scentralizowanego repozytorium. W przypadku korzystania z wielu repozytoriów mogą one stać się silosami niezarządzanego oprogramowania w danym czasie. W przypadku repozytorium centralnego wiele zespołów może używać artefaktów z tego repozytorium dla swoich projektów. Wymusza ona standaryzację, bezpieczeństwo, łatwość zarządzania i eliminuje duplikowanie wysiłków. W ramach centralizacji następujące działania są zalecanymi rozwiązaniami w zakresie długoterminowego zarządzania i trwałości:

- Skojarz Azure Repos z tą samą dzierżawą Azure Active Directory, z której korzysta subskrypcja platformy Azure na potrzeby uwierzytelniania i autoryzacji.
- Utwórz grupę o nazwie **Wszyscy deweloperzy DevTest Labs** w Azure Active Directory zarządzanej centralnie. Wszyscy deweloperzy, którzy przyczyniają się do rozwoju artefaktów, powinni być umieszczani w tej grupie.
- Ta sama Grupa Azure Active Directory może służyć do zapewnienia dostępu do repozytorium Azure Repos i do laboratorium.
- W Azure Repos, rozgałęzianie lub rozwidlenie powinno być używane do oddzielenia repozytorium w środowisku deweloperskim z głównego repozytorium produkcyjnego. Zawartość jest dodawana tylko do głównej gałęzi z żądaniem ściągnięcia po prawidłowym przeglądzie kodu. Gdy recenzent kodu zatwierdza zmiany, programista lidera, który jest odpowiedzialny za konserwację głównej gałęzi, Scala zaktualizowany kod. 

## <a name="corporate-security-policies"></a>Firmowe zasady zabezpieczeń

### <a name="question"></a>Pytanie
Jak organizacja może zapewnić, że zasady zabezpieczeń firmy są stosowane?

### <a name="answer"></a>Odpowiedź
Organizacja może ją osiągnąć, wykonując następujące czynności:

1. Opracowywanie i publikowanie kompleksowych zasad zabezpieczeń. Zasady te są połączone z regułami akceptowalnego zastosowania skojarzonymi z oprogramowaniem, zasobami w chmurze. Definiuje również, co jasno narusza zasady. 
2. Opracowywanie obrazu niestandardowego, artefaktów niestandardowych i procesu wdrażania, który umożliwia aranżację w obszarze zabezpieczeń zdefiniowanym za pomocą usługi Active Directory. To podejście wymusza granicę firmy i ustawia wspólny zestaw kontrolek środowiska. Te kontrolki względem środowiska, które deweloperzy mogą rozważyć podczas opracowywania i postępują zgodnie z bezpiecznym cyklem rozwoju w ramach całego procesu. Celem jest również zapewnienie środowiska, które nie jest nadmiernie restrykcyjne, co może utrudnić programowanie, ale rozsądny zestaw kontrolek. Zasady grupy w jednostce organizacyjnej (OU) zawierające maszyny wirtualne laboratorium mogą być podzbiorem łącznej liczby zasad grupy, które znajdują się w środowisku produkcyjnym. Alternatywnie mogą być dodatkowym zestawem, aby prawidłowo ograniczyć określone zagrożenia.

## <a name="data-integrity"></a>Integralność danych

### <a name="question"></a>Pytanie
W jaki sposób organizacja zapewnia integralność danych, aby zapewnić, że deweloperzy zdalni nie mogą usunąć kodu ani wprowadzić złośliwego oprogramowania czy niezatwierdzonym oprogramowaniem?

### <a name="answer"></a>Odpowiedź
Istnieje kilka warstw kontroli, które ograniczają zagrożenia od zewnętrznych konsultantów, wykonawców lub pracowników, którzy są usługami zdalnymi w programie w celu współpracy w DevTest Labs. 

Jak wspomniano wcześniej, pierwszy krok musi mieć zaprojektowaną i zdefiniowaną zasadę akceptowalnego zastosowania, która jasno podkreśla konsekwencje, gdy ktoś narusza zasady. 

Pierwszą warstwą kontroli dostępu zdalnego jest zastosowanie zasad dostępu zdalnego za pośrednictwem połączenia sieci VPN, które nie jest bezpośrednio połączone z laboratorium. 

Druga warstwa formantów polega na zastosowaniu zestawu obiektów zasad grupy, które uniemożliwiają kopiowanie i wklejanie za pomocą pulpitu zdalnego. Można zaimplementować zasady sieciowe, aby nie zezwalać na usługi wychodzące ze środowiska, takiego jak usługi FTP i RDP poza środowiskiem. Routing zdefiniowany przez użytkownika może wymusić powrót całego ruchu sieciowego platformy Azure z powrotem do lokalnego, ale nie można było uwzględnić wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, chyba że są one kontrolowane za pomocą serwera proxy w celu skanowania zawartości i sesji. Publiczne adresy IP mogą być ograniczone w sieci wirtualnej wspierającej DevTest Labs, aby nie zezwalały na mostkowanie zewnętrznego zasobu sieciowego.

Na koniec tego samego typu ograniczeń należy zastosować w całej organizacji, która musiałaby również uwzględnić wszystkie możliwe metody nośników wymiennych lub zewnętrznych adresów URL, które mogą akceptować ogłoszenie zawartości. Zapoznaj się z specjalistą ds. zabezpieczeń, aby przejrzeć i wdrożyć zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft cybernetycznymi Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Następne kroki
Zobacz [migracja i integracja aplikacji](devtest-lab-guidance-governance-application-migration-integration.md).
