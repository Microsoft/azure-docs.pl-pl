---
title: Utwórz obszar roboczy z włączoną ochroną eksfiltracji danych
description: W tym artykule wyjaśniono, jak utworzyć obszar roboczy z ochroną danych eksfiltracji w usłudze Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598086"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Utwórz obszar roboczy z włączoną ochroną eksfiltracji danych
W tym artykule opisano sposób tworzenia obszaru roboczego z włączoną ochroną eksfiltracji danych oraz zarządzania zatwierdzonymi dzierżawcami usługi Azure AD dla tego obszaru roboczego.

>[!Note]
>Po utworzeniu obszaru roboczego nie można zmienić konfiguracji obszaru roboczego dla zarządzanej sieci wirtualnej i ochrony eksfiltracji danych.

## <a name="prerequisites"></a>Wymagania wstępne
- Uprawnienia do tworzenia zasobów obszaru roboczego na platformie Azure.
- Uprawnienia obszaru roboczego Synapse do tworzenia zarządzanych prywatnych punktów końcowych.
- Subskrypcje zarejestrowane dla dostawcy zasobów sieciowych. [Dowiedz się więcej.](../../azure-resource-manager/management/resource-providers-and-types.md)

Wykonaj kroki opisane w sekcji [Szybki Start: Tworzenie obszaru roboczego Synapse](../quickstart-create-workspace.md) , aby rozpocząć tworzenie obszaru roboczego. Przed utworzeniem obszaru roboczego Skorzystaj z poniższych informacji, aby dodać ochronę eksfiltracji danych do obszaru roboczego.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Dodawanie ochrony eksfiltracji danych podczas tworzenia obszaru roboczego
1. Na karcie Sieć zaznacz pole wyboru "Włącz zarządzaną sieć wirtualną".
1. Wybierz opcję "tak" dla opcji "Zezwalaj na ruch wychodzący tylko dla zatwierdzonych elementów docelowych".
1. Wybierz zatwierdzone dzierżawy usługi Azure AD dla tego obszaru roboczego.
1. Przejrzyj konfigurację i Utwórz obszar roboczy.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Zrzut ekranu pokazujący Tworzenie obszaru roboczego Synapse z wybraną opcją &quot;Włącz zarządzanie siecią wirtualną&quot;.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Zarządzanie zatwierdzonymi dzierżawcami Azure Active Directory dla obszaru roboczego
1. W Azure Portal obszaru roboczego przejdź do "zatwierdzone dzierżawy usługi Azure AD". Lista zatwierdzonych dzierżawców usługi Azure AD dla obszaru roboczego zostanie wyświetlona w tym miejscu. Dzierżawca obszaru roboczego jest domyślnie uwzględniany i nie jest wyświetlany na liście.
1. Użyj "+ Dodaj", aby dołączyć nowe dzierżawy do zatwierdzonej listy.
1. Aby usunąć dzierżawę usługi Azure AD z listy zatwierdzonych, wybierz dzierżawcę i wybierz opcję "Usuń", a następnie pozycję "Zapisz".
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Tworzenie obszaru roboczego przy użyciu ochrony danych eksfiltracji":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Nawiązywanie połączenia z zasobami platformy Azure w zatwierdzonych dzierżawach usługi Azure AD

Można utworzyć zarządzane prywatne punkty końcowe, aby połączyć się z zasobami platformy Azure, które znajdują się w dzierżawach usługi Azure AD, które są zatwierdzone dla obszaru roboczego. Wykonaj kroki opisane w przewodniku dotyczącym [tworzenia zarządzanych prywatnych punktów końcowych](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Zasoby w dzierżawach innych niż dzierżawa obszaru roboczego nie mogą blokować reguł zapory, aby pule SQL mogły się z nimi łączyć. Zasoby w zarządzanej sieci wirtualnej obszaru roboczego, takie jak klastry Spark, mogą łączyć zarządzane linki prywatne z zasobami chronionymi przez zaporę.

## <a name="known-limitations"></a>Znane ograniczenia
Użytkownicy mogą udostępnić plik konfiguracyjny środowiska, aby zainstalować pakiety języka Python z publicznych repozytoriów, takich jak PyPI. W chronionych obszarach roboczych danych eksfiltracji połączenia z repozytoriami wychodzącymi są blokowane. W związku z tym biblioteka języka Python zainstalowana z repozytoriów publicznych, takich jak PyPI, nie jest obsługiwana. 

Alternatywnie użytkownicy mogą przekazywać pakiety obszarów roboczych lub utworzyć kanał prywatny w ramach swojego konta podstawowego Azure Data Lake Storage. Aby uzyskać więcej informacji, odwiedź stronę [Zarządzanie pakietami w usłudze Azure Synapse Analytics](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) 
  
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat ochrony danych eksfiltracji w obszarach roboczych Synapse](./workspace-data-exfiltration-protection.md)

Dowiedz się więcej o [zarządzanym obszarze roboczym Virtual Network](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
