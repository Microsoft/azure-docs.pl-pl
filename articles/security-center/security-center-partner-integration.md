---
title: Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Poznaj sposób integracji usługi Azure Security Center z partnerami w celu poprawy ogólnego stanu zabezpieczeń zasobów platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758036"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań, takich jak ochrona przed złośliwym oprogramowaniem i ocena luk w zabezpieczeniach, usługa Security Center może aprowizować agenta na maszynach wirtualnych. W przypadku urządzeń zapory usługa Security Center może zająć się dużą wymaganą konfiguracją sieci.
- **Zintegrowane wykrywanie:** zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane jako część alertów i incydentów w centrum zabezpieczeń. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: klienci mogą używać zintegrowanych zdarzeń kondycji do błyskawicznego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczające obejmują ocenę luk w zabezpieczeniach przez [qualys](https://www.qualys.com/public-cloud/#azure) i [rapid7](https://www.rapid7.com/products/insightvm/) oraz zaporę aplikacji Microsoft Application Gateway Web.

> [!NOTE]
> Usługa Security Center nie instaluje agenta usługi Log Analytics na partnerskich urządzeniach wirtualnych, ponieważ większość dostawców zabezpieczeń zabrania agentów zewnętrznych działających na ich urządzeniach.

Aby dowiedzieć się więcej o integracji narzędzi do skanowania luk w zabezpieczeniach z qualys, w tym wbudowanego skanera dostępnego dla klientów warstwy standardowej, zobacz: 

- [Zintegrowany skaner luk w zabezpieczeniach dla maszyn wirtualnych](built-in-vulnerability-assessment.md).
- [Wdrażanie rozwiązania do skanowania luk](partner-vulnerability-assessment.md)w zabezpieczeniach partnera .

Usługa Security Center oferuje również analizę luk w zabezpieczeniach:

* Bazy danych SQL — zobacz [Eksplorowanie raportów oceny luk w zabezpieczeniach na pulpicie nawigacyjnym oceny luk w zabezpieczeniach](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Obrazy rejestru kontenerów platformy Azure — zobacz [integracja rejestru kontenerów platformy Azure z Centrum zabezpieczeń (wersja zapoznawcza)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można również połączyć inne źródła danych zabezpieczeń, w tym komputery z systemem lokalnym lub w innych chmurach.

[![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Z [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/)otwórz **Centrum zabezpieczeń**.

1. Z menu Centrum zabezpieczeń wybierz pozycję **Rozwiązania zabezpieczeń**.

Na stronie **Rozwiązania zabezpieczeń** możesz zobaczyć kondycję zintegrowanych rozwiązań zabezpieczeń platformy Azure i uruchomić podstawowe zadania zarządzania.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **Rozwiązania połączone** zawiera rozwiązania zabezpieczające, które są obecnie połączone z usługą Security Center. Pokazuje również stan kondycji każdego rozwiązania.  

![Rozwiązania połączone](./media/security-center-partner-integration/connected-solutions.png)

Stan rozwiązania partnerskiego może być:

* **Zdrowy** (zielony) - brak problemów zdrowotnych.
* **Niezdrowe** (czerwone) - istnieje problem zdrowotny, który wymaga natychmiastowej uwagi.
* **Zatrzymano raportowanie** (pomarańczowy) - rozwiązanie przestało zgłaszać swoje zdrowie.
* **Nie zgłoszono** (szary) — rozwiązanie nie zgłosiło jeszcze niczego i nie są dostępne żadne dane dotyczące kondycji. Stan rozwiązania może być niezgłoszony, jeśli był niedawno połączony i nadal jest wdrażany.

> [!NOTE]
> Jeśli dane o stanie kondycji nie są dostępne, usługa Security Center pokazuje datę i godzinę ostatniego odebranego zdarzenia, aby wskazać, czy rozwiązanie jest raportowanie, czy nie. Jeśli nie są dostępne żadne dane dotyczące kondycji i w ciągu ostatnich 14 dni nie odebrano żadnych alertów, usługa Security Center wskazuje, że rozwiązanie jest w złej kondycji lub nie jest raportowane.
>
>

Wybierz **widok, aby** uzyskać dodatkowe informacje i opcje, takie jak:

   - **Konsola rozwiązania** — otwiera środowisko zarządzania dla tego rozwiązania.
   - **Link VM** — otwiera stronę Aplikacje łącza. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuwanie rozwiązania**
   - **Konfiguruj**

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Usługa Security Center automatycznie odnajduje rozwiązania zabezpieczeń działające na platformie Azure, ale niesie ze sobą rozwiązania Security Center i wyświetla rozwiązania w sekcji **Odnalezione rozwiązania.** Rozwiązania te obejmują rozwiązania platformy Azure, takie jak [usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)i rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [Cennik,](security-center-pricing.md) aby dowiedzieć się więcej o warstwach cenowych.
>

Wybierz **connect** w ramach rozwiązania, aby zintegrować się z centrum zabezpieczeń i otrzymywać powiadomienia o alertach zabezpieczeń.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby uzyskać powiązane informacje, zobacz następujące artykuły:

* [Eksportuj alerty i zalecenia dotyczące zabezpieczeń](continuous-export.md). Dowiedz się, jak skonfigurować integrację z usługą Azure Sentinel lub dowolnym innym pakietem SIEM.
* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.