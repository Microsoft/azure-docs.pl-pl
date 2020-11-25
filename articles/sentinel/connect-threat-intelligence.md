---
title: Łączenie danych analizy zagrożeń z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane analizy zagrożeń z wskaźnikiem kontrolnym platformy Azure.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 223f3e45f25e3aed3ed6fa15e5b9ea04b17f6c59
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023912"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Łączenie danych od dostawców analizy zagrożeń

> [!IMPORTANT]
> Łączniki danych analizy zagrożeń w usłudze Azure wskaźnikowej są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja wskaźnikowa platformy Azure umożliwia importowanie wskaźników zagrożeń, które są używane przez organizację, co może zwiększyć możliwości wykrywania znanych zagrożeń i ustalania priorytetów przez analityków zabezpieczeń. Niektóre funkcje ze wskaźnikiem na platformie Azure są dostępne lub udoskonalone:

- **Analiza** obejmuje zestaw szablonów zaplanowanej reguły, które można włączyć, aby generować alerty i zdarzenia na podstawie dopasowań zdarzeń dzienników ze wskaźników zagrożeń.

- **Skoroszyty** zawierają podsumowanie informacji o wskaźnikach zagrożeń zaimportowanych do platformy Azure, a także wszystkich alertów wygenerowanych na podstawie reguł analizy, które pasują do wskaźników zagrożeń.

- Zapytania **polowania** umożliwiają badaczom zabezpieczeń korzystanie z wskaźników zagrożeń w kontekście typowych scenariuszy polowania.

- **Notesy** mogą używać wskaźników zagrożeń podczas badania anomalii i wyszukiwania złośliwych zachowań.

Za pomocą jednego z produktów zintegrowanej platformy do analizy zagrożeń (TIP), które wymieniono w następnej sekcji, nawiązuje połączenie z serwerami TAXII lub za pomocą bezpośredniej integracji [Microsoft Graph z interfejsem API zabezpieczeń tiIndicators](/graph/api/resources/tiindicator), można przesyłać wskaźniki zagrożeń na platformę Azure.

## <a name="integrated-threat-intelligence-platform-products"></a>Zintegrowane produkty platformy do analizy zagrożeń

- [MISP platforma analizy zagrożeń Open Source](https://www.misp-project.org/)
    
    Przykładowy skrypt, który zapewnia klientom z wystąpieniami MISP Migrowanie wskaźników zagrożeń do interfejsu API zabezpieczeń Microsoft Graph, można znaleźć w artykule [MISP do Microsoft Graph skrypt zabezpieczeń](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Aby pobrać Integrator ThreatStream i rozszerzenia oraz instrukcje dotyczące łączenia analizy ThreatStream z interfejsem API zabezpieczeń Microsoft Graph, zobacz stronę [pliki do pobrania ThreatStream](https://ui.threatstream.com/downloads) .

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Aby uzyskać instrukcje z przewodnikiem, zobacz [wysyłanie IOCs do interfejsu API zabezpieczeń Microsoft Graph przy użyciu MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)

    Aby uzyskać więcej informacji, zobacz [ThreatConnect Integrations](https://threatconnect.com/integrations/) i poszukaj Microsoft Graph Security API na stronie.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Połącz wskaźnik platformy Azure z platformą analizy zagrożeń

### <a name="prerequisites"></a>Wymagania wstępne  

- Rola usługi Azure AD administratora globalnego lub administratora zabezpieczeń umożliwiająca Przyznawanie uprawnień do produktu TIP lub aplikacji niestandardowej korzystającej Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń.

- Uprawnienia do odczytu i zapisu do obszaru roboczego wskaźnikowego platformy Azure w celu przechowywania wskaźników zagrożeń.

### <a name="instructions"></a>Instrukcje

1. [Zarejestruj aplikację](/graph/auth-v2-service#1-register-your-app) w Azure Active Directory, aby uzyskać identyfikator aplikacji, klucz tajny aplikacji i identyfikator dzierżawy Azure Active Directory. Te wartości są potrzebne podczas konfigurowania zintegrowanego produktu lub aplikacji TIP, która korzysta Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń.

2. [Skonfiguruj uprawnienia interfejsu API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) dla zarejestrowanej aplikacji: dodaj uprawnienie Microsoft Graph aplikacji **ThreatIndicators. ReadWrite. OwnedBy** do zarejestrowanej aplikacji.

3. Skontaktuj się z administratorem dzierżawy Azure Active Directory, aby przyznać administratorowi zgodę na zarejestrowana aplikacja w organizacji. W Azure Portal: **Azure Active Directory**  >  **rejestracje aplikacji**  >  **\<_app name_>**  >  **Wyświetl uprawnienia dla interfejsu API**  >  **Udziel zgody administratora \<_tenant name_> na**.

4. Skonfiguruj produkt lub aplikację TIP, która korzysta Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń w celu wysyłania wskaźników do wskaźnika platformy Azure, określając następujące kwestie:
    
    a. Wartości identyfikatora, wpisu tajnego i identyfikatora dzierżawy zarejestrowanej aplikacji.
    
    b. Dla produktu docelowego określ platformę Azure.
    
    c. Dla akcji Określ alert.

5. W Azure Portal przejdź do łączników **danych wskaźnikowych platformy Azure**,  >  **Data connectors** a następnie wybierz łącznik **platformy analizy zagrożeń (wersja zapoznawcza)** .

6. Wybierz **Otwórz stronę łącznika**, a następnie **Połącz się**.

7. Aby wyświetlić wskaźniki zagrożeń zaimportowane do usługi Azure wskaźnikowej, przejdź do **platformy Azure — dzienniki**  >  i **SecurityInsights**, a następnie rozwiń węzeł **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Łączenie z serwerem TAXII platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do odczytu i zapisu do obszaru roboczego wskaźnikowego platformy Azure w celu przechowywania wskaźników zagrożeń.

- Adres URI serwera TAXII 2,0 i identyfikator kolekcji.

### <a name="instructions"></a>Instrukcje

1. W Azure Portal przejdź do łączników **danych wskaźnikowych platformy Azure**,  >  **Data connectors** a następnie wybierz łącznik **analizy zagrożeń — TAXII (wersja zapoznawcza)** .

2. Wybierz pozycję **Otwórz stronę łącznika**.

3. Określ wymagane i opcjonalne informacje w polach tekstowych.

4. Wybierz pozycję **Dodaj** , aby włączyć połączenie z serwerem TAXII 2,0.

5. Jeśli masz dodatkowe serwery z TAXII 2,0: Powtórz kroki 3 i 4.

6. Aby wyświetlić wskaźniki zagrożeń zaimportowane do usługi Azure wskaźnikowej, przejdź do **platformy Azure — dzienniki**  >  i **SecurityInsights**, a następnie rozwiń węzeł **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dostawcy analizy zagrożeń z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły.

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).