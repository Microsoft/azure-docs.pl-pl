---
title: Usługa Azure Defender dla App Service — korzyści i funkcje
description: Dowiedz się więcej na temat możliwości usługi Azure Defender dla App Service i sposobu włączania jej w ramach subskrypcji
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100818"
---
# <a name="protect-your-web-apps-and-apis"></a>Ochrona aplikacji sieci Web i interfejsów API

## <a name="prerequisites"></a>Wymagania wstępne

Security Center jest natywnie zintegrowana z App Service, eliminując konieczność wdrażania i dołączania — integracja jest niewidoczna.

Aby chronić plan Azure App Service za pomocą usługi Azure Defender dla App Service, potrzebne są:

- Obsługiwany plan App Service skojarzony z dedykowanymi maszynami. Obsługiwane plany są wymienione w temacie [Availability (dostępność](#availability)).

- Usługa Azure Defender została włączona w ramach subskrypcji zgodnie z opisem w [przewodniku szybki start: Włączanie usługi Azure Defender](enable-azure-defender.md).

    > [!TIP]
    > Opcjonalnie można włączyć poszczególne plany w usłudze Azure Defender (na przykład usługa Azure Defender dla App Service).

## <a name="availability"></a>Dostępność

| Aspekt                       | Szczegóły                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:               | Ogólna dostępność (GA)                                                                                                                                                                      |
| Wpisaną                     | [Usługa Azure Defender dla App Service](azure-defender.md) jest rozliczana zgodnie z [cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/)<br>Opłaty są naliczane w oparciu o łączne wystąpienia obliczeniowe we wszystkich planach       |
| Obsługiwane App Service plany: | [Wszystkie plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/) są obsługiwane z wyjątkiem [Azure Functions w planie zużycia](../azure-functions/functions-scale.md). |
| Połączeń                      | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Jakie korzyści ma usługa Azure Defender dla App Service?

Azure App Service to w pełni zarządzana platforma do tworzenia i hostowania aplikacji i interfejsów API sieci Web. Ponieważ platforma jest w pełni zarządzana, nie trzeba martwić się o infrastrukturę. Umożliwia zarządzanie, monitorowanie i wgląd w dane operacyjne w celu spełnienia wymagań dotyczących wydajności, zabezpieczeń i zgodności klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Usługa Azure Defender dla App Service** używa skali chmury, aby identyfikować ataki ukierunkowane na aplikacje działające przez App Service. Osoby atakujące mogą wykrywać słabe i luki w zabezpieczeniach aplikacji sieci Web. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luk w zabezpieczeniach i ataków oraz do uczenia się nowych wzorców, które będą używane później.

Po włączeniu usługi Azure Defender dla App Service od razu skorzystasz z następujących usług oferowanych przez ten plan usługi Azure Defender:

- **Zabezpieczenia** Security Center oceniają zasoby objęte planem App Service i generują zalecenia dotyczące zabezpieczeń na podstawie ich wyników. Aby zabezpieczyć zasoby App Service, Skorzystaj z szczegółowych instrukcji przedstawionych w tych zaleceniach.

- **Wykrywanie** — usługa Azure Defender wykrywa wiele zagrożeń dla zasobów App Service przez monitorowanie:
    - wystąpienie maszyny wirtualnej, w której działa App Service, a jej interfejs zarządzania
    - żądania i odpowiedzi wysyłane do i z aplikacji App Service
    - podstawowe Piaskownice i maszyny wirtualne
    - App Service dzienniki wewnętrzne — dostępne w celu zapewnienia widoczności platformy Azure jako dostawcy chmury

Jako rozwiązanie natywne w chmurze usługa Azure Defender może identyfikować metodologie ataków mające zastosowanie do wielu celów. Na przykład z jednego hosta trudno jest zidentyfikować rozproszonego ataku z małego podzbioru adresów IP, przeszukiwanie do podobnych punktów końcowych na wielu hostach.

Dane dziennika i infrastruktury mogą poinformowania o tym scenariuszu: od nowego ataku w sieci dzikiej w celu naruszenia kompromisów na komputerach klienckich. W związku z tym nawet jeśli Security Center zostanie wdrożona po wykorzystaniu aplikacji sieci Web, może być możliwe wykrycie trwających ataków.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Jakie zagrożenia może wykryć usługa Azure Defender App Service?

### <a name="threats-by-mitre-attck-tactics"></a>Zagrożenia według MITRE ATT&taktykę

Usługa Azure Defender monitoruje wiele zagrożeń dla zasobów App Service. Alerty obejmują prawie kompletną listę MITRE ATT&taktykę przed atakiem na polecenie i kontrolę. Usługa Azure Defender może wykryć:

- **Zagrożenia przed atakami** — usługa Defender może wykryć wykonywanie wielu typów skanerów luk w zabezpieczeniach, których osoby atakujące często używają do sondowania aplikacji pod kątem słabych liter.

- **Początkowe zagrożenia dostępu**  -  [Analiza zagrożeń firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) przygotowuje te alerty, które obejmują wyzwalanie alertu, gdy znany złośliwy adres IP nawiązuje połączenie z interfejsem FTP Azure App Service.

- **Zagrożenia związane z wykonywaniem** — usługa Defender może wykryć próby uruchomienia poleceń o wysokim poziomie uprawnień, poleceń systemu Linux w systemie Windows App Service, zachowania ataku bezplikowego, narzędzi wyszukiwania w walutach cyfrowych oraz wielu innych podejrzanych i złośliwych działań wykonywania kodu.

### <a name="dangling-dns-detection"></a>Zawieszonego wykrywanie nazw DNS

Usługa Azure Defender dla App Service identyfikuje także wszystkie wpisy DNS pozostały w rejestrze DNS podczas likwidowania witryny sieci App Serviceowej — są one znane jako zawieszonego wpisy DNS. Po usunięciu witryny sieci Web i usunięciu jej domeny niestandardowej z rejestratora DNS wpis DNS wskazuje na nieistniejący zasób, a poddomena jest narażona na przejęcie. Usługa Azure Defender nie skanuje rejestratora DNS pod kątem *istniejących* wpisów DNS zawieszonego; generuje on alert, gdy witryna sieci Web w App Service zostanie zlikwidowana i jej domena niestandardowa (wpis DNS) nie zostanie usunięta.

Przejęcia poddomen są typowymi zagrożeniami o wysokiej ważności dla organizacji. Gdy aktor zagrożeń wykryje wpis DNS zawieszonego, tworzy własną witrynę na adresie docelowym. Ruch przeznaczony dla domeny organizacji jest następnie kierowany do witryny aktora zagrożeń i może korzystać z tego ruchu dla szerokiego zakresu złośliwych działań.

Zawieszonego ochrona DNS jest dostępna niezależnie od tego, czy domeny są zarządzane za pomocą Azure DNS, czy rejestratora domeny zewnętrznej i ma zastosowanie do App Service zarówno w systemie Windows, jak i Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Przykład alertu usługi Azure Defender dotyczący wykrytego wpisu DNS zawieszonego. Włącz usługę Azure Defender dla App Service, aby otrzymywać te i inne alerty dla danego środowiska." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Dowiedz się więcej o usłudze DNS zawieszonego i zagrożeniu przejęcia poddomeny, w obszarze [Zapobiegaj wpisom DNS zawieszonego i unikaj przejęcia domen podrzędnych](../security/fundamentals/subdomain-takeover.md).

Pełną listę alertów Azure App Service można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Usługa Defender może nie wyzwolić alertów DNS zawieszonego, jeśli domena niestandardowa nie wskazuje bezpośrednio na zasób App Service lub jeśli usługa Defender nie przekazała ruchu do witryny sieci Web od momentu włączenia ochrony DNS zawieszonego (ponieważ nie będzie dzienników w celu ułatwienia identyfikacji domeny niestandardowej).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla App Service. 

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- Aby wyeksportować alerty do platformy Azure, wszelkich SIEM innych firm lub innych zewnętrznych narzędzi, postępuj zgodnie z instrukcjami zawartymi w [alertach przesyłania strumieniowego do Siem, o lub rozwiązania do zarządzania usługami IT](export-to-siem.md).
- Listę alertów dotyczących usługi Azure Defender dla App Service można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureappserv).
- Aby uzyskać więcej informacji na temat planów App Service, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](enable-azure-defender.md)