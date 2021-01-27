---
title: Zarządzane tożsamości dla platformy Azure
description: Dowiedz się więcej na temat używania tożsamości zarządzanych na platformie Azure z Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881724"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Korzystanie z tożsamości zarządzanych dla platformy Azure z Service Fabric

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób bezpiecznego zarządzania poświadczeniami w kodzie w celu uwierzytelniania w różnych usługach bez zapisywania ich lokalnie na stacji roboczej dewelopera lub w kontroli źródła. *Zarządzane tożsamości dla systemu Azure* rozwiązują ten problem dla wszystkich zasobów w Azure Active Directory (Azure AD), zapewniając im automatyczne tożsamości zarządzane w ramach usługi Azure AD. Tożsamości usługi można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń przechowywanych w kodzie.

*Zarządzane tożsamości dla zasobów platformy Azure* są bezpłatne za pomocą usługi Azure AD dla subskrypcji platformy Azure. Nie ma żadnych dodatkowych kosztów.

> [!NOTE]
> *Zarządzane tożsamości dla systemu Azure* to nowa nazwa usługi znana wcześniej jako tożsamość usługi ZARZĄDZANEJ (msi).

## <a name="concepts"></a>Pojęcia

Zarządzane tożsamości dla systemu Azure są oparte na kilku najważniejszych pojęciach:

- **Identyfikator klienta** — unikatowy identyfikator wygenerowany przez usługę Azure AD, który jest powiązany z aplikacją i jednostką usługi podczas początkowej aprowizacji (Zobacz również [Identyfikator aplikacji](../active-directory/develop/developer-glossary.md#application-id-client-id)).

- **Identyfikator podmiotu zabezpieczeń** — identyfikator obiektu jednostki usługi dla tożsamości zarządzanej, który jest używany do udzielania dostępu opartego na rolach do zasobu platformy Azure.

- Nazwa **główna usługi** — obiekt Azure Active Directory, który reprezentuje projekcję aplikacji usługi AAD w danej dzierżawie (Zobacz też [nazwę główną usługi](../active-directory/develop/developer-glossary.md#service-principal-object)).

Istnieją dwa typy tożsamości zarządzanych:

- **Tożsamość zarządzana przypisana przez system** jest włączona bezpośrednio w wystąpieniu usługi platformy Azure.  Cykl życia tożsamości przypisanej do systemu jest unikatowy dla wystąpienia usługi platformy Azure, na którym jest włączona.
- **Tożsamość zarządzana przypisana przez użytkownika** jest tworzona jako autonomiczny zasób platformy Azure. Tożsamość może być przypisana do co najmniej jednego wystąpienia usługi platformy Azure i jest zarządzana oddzielnie od cyklów życia tych wystąpień.

Aby dowiedzieć się więcej o różnicach między zarządzanymi typami tożsamości, zobacz [jak działają zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Obsługiwane scenariusze dla aplikacji Service Fabric

Zarządzane tożsamości dla Service Fabric są obsługiwane tylko w klastrach Service Fabric wdrożonych na platformie Azure i tylko w przypadku aplikacji wdrożonych jako zasoby platformy Azure; nie można przypisać tożsamości do aplikacji, która nie jest wdrożona jako zasób platformy Azure. Koncepcyjnie mówiąc, obsługa zarządzanych tożsamości w klastrze Service Fabric platformy Azure składa się z dwóch faz:

1. Przypisz co najmniej jedną zarządzaną tożsamość do zasobu aplikacji; do aplikacji można przypisać jedną tożsamość przypisaną do systemu i/lub do 32 tożsamości przypisanych do użytkownika.

2. W definicji aplikacji zamapuj jedną z tożsamości przypisanych do aplikacji do dowolnej usługi obejmującej aplikację.

Tożsamość przypisana przez system aplikacji jest unikatowa dla tej aplikacji; tożsamość przypisana przez użytkownika to zasób autonomiczny, który może być przypisany do wielu aplikacji. W ramach aplikacji można przypisać jedną tożsamość (którą przypisano do systemu lub przypisanej do użytkownika) do wielu usług aplikacji, ale do każdej usługi można przypisać tylko jedną tożsamość. Wreszcie do usługi należy przypisać tożsamość jawnie, aby mieć dostęp do tej funkcji. W efekcie mapowanie tożsamości aplikacji do jej usług składowych pozwala na izolację w aplikacji — usługa może korzystać tylko z zamapowanej tożsamości.  

Obecnie w tej funkcji są obsługiwane następujące scenariusze:

- Wdróż nową aplikację przy użyciu co najmniej jednej usługi i co najmniej jednej przypisanej tożsamości

- Przypisz co najmniej jedną zarządzaną tożsamość do istniejącej aplikacji (wdrożonej przez platformę Azure) w celu uzyskania dostępu do zasobów platformy Azure

Następujące scenariusze nie są obsługiwane lub nie są zalecane; Uwaga te akcje mogą nie być blokowane, ale mogą powodować awarię aplikacji:

- Usuwanie lub zmiana tożsamości przypisanych do aplikacji; Jeśli musisz wprowadzić zmiany, Prześlij oddzielne wdrożenia, aby najpierw dodać nowe przypisanie tożsamości, a następnie usunąć wcześniej przypisane. Usunięcie tożsamości z istniejącej aplikacji może mieć niepożądane skutki, w tym pozostawienie aplikacji w stanie, który nie jest uaktualniony. Całkowicie można bezpiecznie usunąć aplikację, jeśli konieczne jest usunięcie tożsamości; Uwaga: spowoduje to usunięcie tożsamości przypisanej do systemu (jeśli została zdefiniowana) skojarzonej z aplikacją i spowoduje usunięcie wszelkich skojarzeń z tożsamościami przypisanymi do aplikacji.

- Service Fabric obsługa tożsamości zarządzanych nie jest teraz zintegrowana z usługą [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication).

## <a name="next-steps"></a>Następne kroki

- [Wdróż nowy klaster Service Fabric platformy Azure z obsługą tożsamości zarządzanej](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Włączanie obsługi tożsamości zarządzanych w istniejącym klastrze Service Fabric platformy Azure](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Korzystanie z zarządzanej tożsamości aplikacji Service Fabric z poziomu kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
- [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
- [Deklarowanie i używanie wpisów tajnych aplikacji jako KeyVaultReferences](./service-fabric-keyvault-references.md)