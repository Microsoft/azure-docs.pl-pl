---
title: Tożsamości zarządzane dla platformy Azure
description: Dowiedz się więcej o używaniu tożsamości zarządzanych dla platformy Azure za pomocą sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a26f188ed2f5e18bdf775cd1fb21001495ffdc89
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461450"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Używanie tożsamości zarządzanych dla platformy Azure z siecią szkieletową usług

Typowym wyzwaniem podczas tworzenia aplikacji w chmurze jest sposób bezpiecznego zarządzania poświadczeniami w kodzie do uwierzytelniania w różnych usługach bez zapisywania ich lokalnie na stacji roboczej dewelopera lub w kontroli źródła. *Tożsamości zarządzane dla platformy Azure* rozwiązać ten problem dla wszystkich zasobów w usłudze Azure Active Directory (Azure AD), zapewniając im automatycznie zarządzanych tożsamości w usłudze Azure AD. Tożsamości usługi można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń przechowywanych w kodzie.

*Tożsamości zarządzane dla zasobów platformy Azure* są bezpłatne dzięki subskrypcji usługi Azure AD dla platformy Azure. Nie ma żadnych dodatkowych kosztów.

> [!NOTE]
> *Tożsamości zarządzane dla platformy Azure* to nowa nazwa usługi wcześniej znanej jako tożsamość usługi zarządzanej (MSI).

## <a name="concepts"></a>Pojęcia

Tożsamości zarządzane dla platformy Azure opiera się na kilku kluczowych pojęciach:

- **Identyfikator klienta** — unikatowy identyfikator wygenerowany przez usługę Azure AD, który jest powiązany z jednostką aplikacji i usługi podczas początkowego inicjowania obsługi administracyjnej (zobacz również [identyfikator aplikacji](/azure/active-directory/develop/developer-glossary#application-id-client-id)).)

- **Identyfikator zabezpieczeń** — identyfikator obiektu jednostki usługi dla tożsamości zarządzanej, który jest używany do udzielania dostępu opartego na rolach do zasobu platformy Azure.

- **Podmiot usługi** — obiekt usługi Azure Active Directory, który reprezentuje projekcję aplikacji usługi AAD w danej dzierżawie (zobacz również [jednostkę usługi](../active-directory/develop/developer-glossary.md#service-principal-object).)

Istnieją dwa typy tożsamości zarządzanych:

- Tożsamość **zarządzana przypisana do systemu** jest włączona bezpośrednio w wystąpieniu usługi platformy Azure.  Cykl życia tożsamości przypisanej do systemu jest unikatowy dla wystąpienia usługi platformy Azure, na które jest włączona.
- **Tożsamość zarządzana przypisana przez użytkownika** jest tworzona jako autonomiczny zasób platformy Azure. Tożsamość można przypisać do jednego lub więcej wystąpień usługi platformy Azure i jest zarządzany oddzielnie od cyklu życia tych wystąpień.

Aby lepiej zrozumieć różnicę między typami tożsamości [zarządzanych,](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) zobacz Jak działają tożsamości zarządzane dla zasobów platformy Azure?

## <a name="supported-scenarios-for-service-fabric-applications"></a>Obsługiwane scenariusze dla aplikacji sieci szkieletowej usług

Tożsamości zarządzane dla sieci szkieletowej usług są obsługiwane tylko w klastrach sieci szkieletowej wdrożonych w usłudze Azure i tylko dla aplikacji wdrożonych jako zasoby platformy Azure; nie można przypisać tożsamości aplikacji, która nie jest wdrażana jako zasób platformy Azure. Koncepcyjnie rzecz biorąc obsługa tożsamości zarządzanych w klastrze sieci szkieletowej usług Azure składa się z dwóch faz:

1. Przypisywanie jednej lub więcej tożsamości zarządzanych do zasobu aplikacji; aplikacji można przypisać pojedynczą tożsamość przypisaną do systemu i/lub maksymalnie 32 tożsamości przypisane przez użytkownika.

2. W ramach definicji aplikacji mapuj jedną z tożsamości przypisanych do aplikacji do dowolnej indywidualnej usługi składającej się na aplikację.

Tożsamości przypisane przez system aplikacji jest unikatowa dla tej aplikacji; tożsamość przypisana przez użytkownika jest zasobem autonomicznym, który może być przypisany do wielu aplikacji. W ramach aplikacji pojedynczą tożsamość (przypisaną do systemu lub przypisaną przez użytkownika) można przypisać do wielu usług aplikacji, ale każdej pojedynczej usłudze można przypisać tylko jedną tożsamość. Wreszcie usługi należy przypisać tożsamość jawnie mieć dostęp do tej funkcji. W efekcie mapowanie tożsamości aplikacji do jej usług składowych umożliwia izolację w aplikacji — usługa może używać tylko tożsamości mapowane do niego.  

Obecnie dla tej funkcji obsługiwane są następujące scenariusze:

- Wdrażanie nowej aplikacji z jedną lub kilkoma usługami i co najmniej jedną przypisaną tożsamością

- Przypisywanie jednej lub więcej tożsamości zarządzanych do istniejącej aplikacji (wdrożonej w usłudze Azure) w celu uzyskania dostępu do zasobów platformy Azure

Następujące scenariusze nie są obsługiwane lub nie są zalecane; Należy pamiętać, że te akcje mogą nie być blokowane, ale mogą prowadzić do awarii w aplikacjach:

- Usuwanie lub zmienianie tożsamości przypisanych do aplikacji; Jeśli musisz wprowadzić zmiany, prześlij oddzielne wdrożenia, aby najpierw dodać nowe przypisanie tożsamości, a następnie usunąć wcześniej przypisane. Usunięcie tożsamości z istniejącej aplikacji może mieć niepożądane skutki, w tym pozostawienie aplikacji w stanie, który nie można uaktualnić. Bezpieczne jest całkowite usunięcie aplikacji, jeśli konieczne jest usunięcie tożsamości; Należy pamiętać, że spowoduje to usunięcie tożsamości przypisanej przez system (jeśli jest to zdefiniowane) skojarzonej z aplikacją i spowoduje usunięcie wszelkich skojarzeń z tożsamościami przypisanymi przez użytkownika przypisanymi do aplikacji.

- Obsługa sieci szkieletowej usług dla tożsamości zarządzanych nie jest obecnie zintegrowana z [platformą AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie nowego klastra sieci szkieletowej usług Azure z obsługą tożsamości zarządzanych](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Włączanie obsługi tożsamości zarządzanych w istniejącym klastrze sieci szkieletowej usług Azure](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Wykorzystaj zarządzaną tożsamość aplikacji sieci szkieletowej usług z kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
- [Udzielanie dostępu aplikacji sieci szkieletowej usługi Azure do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
- [Deklarowanie i używanie wpisów tajnych aplikacji jako keyvaultreferences](./service-fabric-keyvault-references.md)
