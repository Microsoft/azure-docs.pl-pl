---
title: Omówienie aplikacji zarządzanych
description: Opisuje koncepcje Azure Managed Applications, które oferują rozwiązania w chmurze, które są łatwe do wdrożenia i obsługi przez klientów.
author: tfitzmac
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 7641c1e3bc3620b294c2953b993e9179a4875536
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951812"
---
# <a name="azure-managed-applications-overview"></a>Omówienie usługi Azure Managed Applications

Usługa Azure Managed Applications pozwala tworzyć rozwiązania w chmurze, które klienci mogą łatwo wdrożyć i obsługiwać. Wdrożenie infrastruktury i zapewnienie bieżącej obsługi należy do Ciebie. Aby udostępnić aplikację zarządzaną wszystkim klientom, opublikuj ją w witrynie Azure Marketplace. Aby udostępnić aplikację tylko użytkownikom w Twojej organizacji, opublikuj ją w katalogu wewnętrznym. 

Aplikacja zarządzana jest podobna do szablonu rozwiązania w witrynie Marketplace. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania.

> [!NOTE]
> Wcześniej dokumentacja dotycząca dostawców niestandardowych platformy Azure została uwzględniona w dokumentacji aplikacji zarządzanych. Ta dokumentacja została przeniesiona. Teraz Zobacz temat [Dostawcy niestandardowi platformy Azure](../custom-providers/overview.md).

## <a name="advantages-of-managed-applications"></a>Zalety związane z używaniem aplikacji zarządzanych

Aplikacje zarządzane ułatwiają użytkownikom korzystanie z Twoich rozwiązań. Nie muszą oni dysponować wiedzą na temat infrastruktury w chmurze. Klienci mają ograniczony dostęp do kluczowych zasobów i nie mają problemów wynikających z błędnego zarządzania aplikacją. 

Aplikacje zarządzane pozwalają nawiązać trwałe relacje z klientami. Ty określasz warunki zarządzania aplikacją, a wszystkie opłaty są obsługiwane za pośrednictwem rozliczeń platformy Azure.

Klienci wdrażają aplikacje zarządzane w swoich subskrypcjach, ale nie muszą ich konserwować, aktualizować ani obsługiwać. Można zagwarantować, że wszyscy klienci używają zatwierdzonych wersji. Klienci nie zarządzają aplikacjami, więc nie wymaga się od nich znajomości domeny specyficznej dla aplikacji. Automatycznie otrzymują oni aktualizacje aplikacji i nie muszą rozwiązywać problemów ani diagnozować usterek związanych z aplikacjami. 

Aplikacje zarządzane umożliwiają zespołom IT udostępnianie wstępnie zatwierdzonych rozwiązań użytkownikom w organizacji. Wiesz, że te rozwiązania są zgodne ze standardami używanymi w organizacji.

Zarządzane aplikacje obsługują [zarządzane tożsamości dla zasobów platformy Azure](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>Typy aplikacji zarządzanych

Aplikację zarządzaną można opublikować zewnętrznie lub wewnętrznie.

![Publikowanie wewnętrzne i zewnętrzne](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Wykaz usług

Wykaz usług to wewnętrzny katalog zatwierdzonych rozwiązań, przeznaczonych dla użytkowników w organizacji. Umożliwia on zapewnienie zgodności oferowanych rozwiązań ze standardami obowiązującymi w organizacji. Wykaz pozwala pracownikom łatwo znajdować aplikacje zalecane i zatwierdzone przez działy IT. Mogą oni korzystać z aplikacji zarządzanych udostępnionych przez inne osoby.

Aby dowiedzieć się, jak opublikować aplikację zarządzaną w wykazie usług, zobacz [Tworzenie aplikacji w katalogu usług](publish-service-catalog-app.md).

### <a name="marketplace"></a>Portal Marketplace

Dostawcy, którzy oferują płatne usługi, mogą udostępniać aplikacje zarządzane w witrynie Azure Marketplace. Opublikowana aplikacja jest dostępna dla użytkowników spoza organizacji. Dzięki temu dostawcy usług zarządzanych (MSP), niezależni dostawcy oprogramowania (ISV) oraz integratorzy systemów (SI) mogą oferować swoje rozwiązania wszystkim klientom platformy Azure.

Aby uzyskać informacje dotyczące publikowania aplikacji zarządzanych w witrynie Marketplace, zobacz [Tworzenie aplikacji w witrynie Marketplace](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md).

## <a name="resource-groups-for-managed-applications"></a>Grupy zasobów aplikacji zarządzanych

Zazwyczaj zasoby dla aplikacji zarządzanej znajdują się w dwóch grupach zasobów. Jedną z nich zarządza użytkownik, a drugą — wydawca. Podczas definiowania aplikacji zarządzanej wydawca określa poziomy dostępu. Wydawca może zażądać trwałego przypisania roli lub [dostępu just in Time](request-just-in-time-access.md) do przypisania, które jest ograniczone do przedziału czasu.

Ograniczanie dostępu do [operacji na danych](../../role-based-access-control/role-definitions.md) nie jest obecnie obsługiwane dla wszystkich dostawców danych na platformie Azure.

Na poniższej ilustracji przedstawiono scenariusz, w którym wydawca żąda roli właściciela zarządzanej grupy zasobów. Na tę grupę zasobów wydawca nakłada blokadę tylko do odczytu. Tożsamości wydawcy, które mają dostęp do zarządzanej grupy zasobów, są wykluczone z blokady.

![Dostęp do grupy zasobów](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupa zasobów aplikacji

Ta grupa zasobów zawiera wystąpienie aplikacji zarządzanej. Może ona zawierać tylko jeden zasób. Typ zasobu aplikacji zarządzanej to **Microsoft.Solutions/applications**.

Klient, który ma pełny dostęp do grupy zasobów, zarządza cyklem życia aplikacji zarządzanej.

### <a name="managed-resource-group"></a>Zarządzana grupa zasobów

Ta grupa zasobów zawiera wszystkie zasoby, które są wymagane przez aplikację zarządzaną. Na przykład może ona obejmować maszyny wirtualne, konta magazynu i sieci wirtualne używane przez rozwiązanie. Klient ma ograniczony dostęp do tej grupy zasobów, ponieważ nie zarządza on poszczególnymi zasobami aplikacji zarządzanej. Dostęp wydawcy do grupy zasobów odpowiada roli określonej w definicji aplikacji zarządzanej. Na przykład wydawca może zażądać roli właściciela lub współautora dla tej grupy zasobów. Dostęp jest trwały lub ograniczony do określonego czasu.

W przypadku publikowania [aplikacji zarządzanej w portalu Marketplace](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)Wydawca może udzielić konsumentom możliwości wykonywania określonych akcji na zasobach w zarządzanej grupie zasobów. Na przykład Wydawca może określić, że klienci mogą ponownie uruchamiać maszyny wirtualne. Wszystkie inne akcje poza akcją odczytu nadal są odrzucane. Zmiany w zasobach w zarządzanej grupie zasobów przez odbiorcę z przyznanymi akcjami podlegają [Azure Policy](../../governance/policy/overview.md) przypisań w ramach dzierżawy odbiorców objętych zakresem do uwzględnienia zarządzanej grupy zasobów.

Usunięcie aplikacji zarządzanej powoduje również usunięcie zarządzanej grupy zasobów.

## <a name="azure-policy"></a>Azure Policy

Aby przeprowadzić inspekcję zarządzanej aplikacji, można zastosować [Azure Policy](../../governance/policy/overview.md) . Należy zastosować definicje zasad, aby upewnić się, że wdrożone wystąpienia aplikacji zarządzanej spełniają wymagania dotyczące danych i zabezpieczeń. Jeśli aplikacja wchodzi w interakcje z danymi poufnymi, upewnij się, że oceniono sposoby ochrony tych danych. Na przykład jeśli aplikacja współdziała z danymi z Microsoft 365, Zastosuj definicję zasad, aby upewnić się, że szyfrowanie danych jest włączone.

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono korzyści z używania aplikacji zarządzanych. Przejdź do następnego artykułu, aby utworzyć definicję aplikacji zarządzanej.

> [!div class="nextstepaction"]
> [Szybki start: publikowanie definicji aplikacji zarządzanej platformy Azure](publish-service-catalog-app.md)