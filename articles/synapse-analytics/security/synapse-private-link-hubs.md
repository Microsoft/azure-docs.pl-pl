---
title: Nawiązywanie połączenia z programem Synapse Studio przy użyciu linków prywatnych
description: W tym artykule omówiono sposób nawiązywania połączenia z platformą Azure Synapse Studio przy użyciu linków prywatnych
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d39beca60264023c8eb7c1bc78cd1ac15c3b45dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586628"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Nawiązywanie połączenia z usługą Azure Synapse Studio przy użyciu prywatnych centrów linków platformy Azure 

W tym artykule opisano sposób, w jaki prywatne linki do usługi Azure Synapse Analytics służą do bezpiecznego łączenia się z programem Synapse Studio. 

## <a name="azure-private-link-hubs"></a>Centra prywatnych łączy platformy Azure 
Możesz bezpiecznie połączyć się z usługą Azure Synapse Studio z sieci wirtualnej platformy Azure za pomocą linków prywatnych. Centra prywatnych linków usługi Azure Synapse Analytics to zasoby platformy Azure, które działają jako łączniki między bezpieczną siecią i środowiskiem sieci Web Synapse Studio. 

Istnieją dwa kroki umożliwiające nawiązanie połączenia z programem Synapse Studio przy użyciu linków prywatnych. Najpierw należy utworzyć zasób Hub linku prywatnego. Po drugie należy utworzyć prywatny punkt końcowy z sieci wirtualnej platformy Azure do tego prywatnego centrum linków. Następnie można używać prywatnych punktów końcowych do bezpiecznego komunikowania się z programem Synapse Studio. Należy zintegrować prywatne punkty końcowe z rozwiązaniem DNS — rozwiązaniem lokalnym lub Prywatna strefa DNS platformy Azure. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Centra prywatnych łączy platformy Azure i Azure Synapse Studio
Możesz użyć pojedynczego zasobu centrum linku prywatnego usługi Azure Synapse, aby połączyć się prywatnie ze wszystkimi obszarami roboczymi usługi Azure Synapse Analytics przy użyciu platformy Azure Synapse Studio. Obszary robocze nie muszą znajdować się w tym samym regionie co usługa Azure Synapse Private link Hub. Za pomocą usługi Azure Synapse Private link Hub można również korzystać z połączeń z obszarami roboczymi Synapse w różnych subskrypcjach lub dzierżawach usługi Azure AD.

Możesz utworzyć prywatne centrum linków, wyszukując w Azure Portal *centra prywatnych linków Synapse* i wybierając pozycję **Azure Synapse Analytics (prywatne centra połączeń)** z usług. Postępuj zgodnie z instrukcjami w przewodniku dotyczącym sposobu łączenia się z [zasobami obszaru roboczego z sieci z ograniczeniami](./how-to-connect-to-workspace-from-restricted-network.md) w celu uzyskania szczegółowych informacji.

>[!NOTE]
>Prywatne centra linków są przeznaczone do bezpiecznego ładowania statycznej zawartości Synapse Studio przez linki prywatne. Należy utworzyć **osobne, prywatne punkty końcowe** dla zasobów, z którymi chcesz nawiązać połączenie w obszarze roboczym, takie jak zainicjowane/dedykowane pule SQL lub pule Spark. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Centra prywatnych łączy platformy Azure i Virtual Network platformy Azure
Musisz połączyć sieć wirtualną platformy Azure z zasobem Synapse Private link Hub, aby zabezpieczyć kompleksowe połączenie z programem Synapse Studio. W tym celu należy utworzyć prywatny punkt końcowy z sieci wirtualnej w utworzonym przez Ciebie centrum linków prywatnych. Możesz użyć Azure Portal dla prywatnego centrum linków i przejść do sekcji prywatnego punktu końcowego. Wybierz pozycję "+ prywatny punkt końcowy", aby utworzyć nowy prywatny punkt końcowy, który nawiązuje połączenie z prywatnym koncentratorem linków.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający stronę połączeń prywatnych punktów końcowych.":::

Upewnij się, że typ zasobu "Microsoft. Synapse/privateLinkHubs" jest wybrany na karcie "zasób".

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Zrzut ekranu pokazujący stronę &quot;Tworzenie prywatnego punktu końcowego&quot; z wyróżnioną pozycją &quot;typ zasobu&quot;.":::

Na karcie "Konfiguracja" Wybierz opcję "privatelink.azuresynapse.net" dla stref Prywatna strefa DNS podczas integracji z siecią wirtualną i prywatną strefą DNS.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Tworzenie prywatnego punktu końcowego dla prywatnego centrum łączy":::

## <a name="next-steps"></a>Następne kroki

[Łączenie z zasobami obszaru roboczego z sieci z ograniczeniami](./how-to-connect-to-workspace-from-restricted-network.md)

Dowiedz się więcej o [zarządzanym obszarze roboczym Virtual Network](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)

[Łączenie z obszarem roboczym usługi Synapse przy użyciu prywatnych punktów końcowych](./how-to-connect-to-workspace-with-private-links.md)

