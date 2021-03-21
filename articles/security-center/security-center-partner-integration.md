---
title: Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Poznaj sposób integracji usługi Azure Security Center z partnerami w celu poprawy ogólnego stanu zabezpieczeń zasobów platformy Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099254"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań takich jak oprogramowanie chroniące przed złośliwym kodem i Ocena luk w zabezpieczeniach Security Center można zainicjować obsługę agenta na maszynach wirtualnych. W przypadku urządzeń zapory Security Center może wymagać większości wymaganych konfiguracji sieci.
- **Zintegrowane wykrywania**: zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane w ramach Security Center alertów i zdarzeń. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: klienci mogą używać zintegrowanych zdarzeń kondycji do błyskawicznego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczeń obejmują ocenę luk w zabezpieczeniach przez [Qualys](https://www.qualys.com/public-cloud/#azure) i [Rapid7](https://www.rapid7.com/products/insightvm/) oraz [Microsoft Azure zaporę aplikacji sieci Web na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Security Center nie instaluje agenta Log Analytics na urządzeniach wirtualnych partnera, ponieważ większość dostawców zabezpieczeń zabroni zewnętrznych agentów działających na ich urządzeniach.

Aby dowiedzieć się więcej na temat integracji narzędzi do skanowania luk w zabezpieczeniach z programu Qualys, w tym wbudowanego skanera dostępnego dla klientów usługi Azure Defender, zobacz [ocenę luk w zabezpieczeniach dla Virtual Machines platformy Azure](deploy-vulnerability-assessment-vm.md).

Security Center oferuje również analizę luk w zabezpieczeniach dla:

* Bazy danych SQL — zobacz [Eksplorowanie raportów oceny luk w zabezpieczeniach na pulpicie nawigacyjnym oceny luk w zabezpieczeniach](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Obrazy Azure Container Registry — zobacz [Korzystanie z usługi Azure Defender dla rejestrów kontenerów do skanowania obrazów pod kątem luk w zabezpieczeniach](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można także połączyć inne źródła danych zabezpieczeń, w tym komputery działające lokalnie lub w innych chmurach.

[![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. W [Azure Portal](https://azure.microsoft.com/features/azure-portal/)Otwórz **Security Center**.

1. Z menu Security Center wybierz pozycję **rozwiązania zabezpieczeń**.

Na stronie **rozwiązania zabezpieczeń** można sprawdzić kondycję zintegrowanych rozwiązań zabezpieczeń platformy Azure i uruchomić podstawowe zadania zarządzania.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **połączone rozwiązania** zawiera rozwiązania zabezpieczeń, które są obecnie połączone z Security Center. Pokazuje również stan kondycji każdego rozwiązania.  

![Rozwiązania połączone](./media/security-center-partner-integration/connected-solutions.png)

Stanem rozwiązania partnerskiego może być:

* **Zdrowy** (zielony) — brak problemów z kondycją.
* **Zła kondycja** (czerwony) — występuje problem z kondycją, który wymaga natychmiastowej uwagi.
* **Zatrzymane raportowanie** (pomarańczowy) — rozwiązanie przestało zgłaszać swoją kondycję.
* **Nie zgłoszono** (szare) — rozwiązanie nie zgłosiło jeszcze niczego i żadne dane kondycji nie są dostępne. Stan rozwiązania może być nieraportowany, jeśli był on ostatnio połączony i nadal jest wdrażany.

> [!NOTE]
> Jeśli dane stanu kondycji są niedostępne, Security Center pokazuje datę i godzinę ostatniego odebranego zdarzenia, aby wskazać, czy rozwiązanie zgłasza, czy nie. Jeśli dane dotyczące kondycji nie są dostępne i nie odebrano żadnych alertów w ciągu ostatnich 14 dni, Security Center wskazuje, że rozwiązanie jest w złej kondycji lub nie jest raportowane.
>
>

Wybierz opcję **Widok** , aby uzyskać dodatkowe informacje i opcje, takie jak:

   - **Konsola rozwiązania** — otwiera środowisko zarządzania dla tego rozwiązania.
   - **Link do maszyny wirtualnej** — otwiera stronę łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuwanie rozwiązania**
   - **Ustaw opcję**

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Security Center automatycznie wykrywa rozwiązania zabezpieczeń działające na platformie Azure, ale nie połączyły się z Security Center i wyświetla rozwiązania w sekcji **odnalezione rozwiązania** . Rozwiązania te obejmują rozwiązania platformy Azure, takie jak [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)i rozwiązania partnerskie.

> [!NOTE]
> Włącz **usługę Azure Defender** na poziomie subskrypcji dla odnalezionych rozwiązań. Dowiedz się więcej z [przewodnika Szybki Start: Włączanie usługi Azure Defender](enable-azure-defender.md).

Wybierz pozycję **Połącz** w ramach rozwiązania, aby przeprowadzić integrację z usługą Security Center i otrzymywać powiadomienia o alertach zabezpieczeń.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się, jak skonfigurować integrację z platformą Azure, lub innymi SIEM, zobacz [ciągły eksport danych Security Center](continuous-export.md).