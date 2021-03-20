---
title: Zarządzanie kontami deweloperów przy użyciu grup w usłudze Azure API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak zarządzać kontami deweloperów przy użyciu grup w usłudze Azure API Management. Utwórz grupy, a następnie skojarz je z produktami lub programistami.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: ea674981036b4be292329a4b30b43180ed26d642
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92092787"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Jak tworzyć grupy do zarządzania kontami deweloperów w usłudze Azure API Management i jak z nich korzystać

W usłudze API Management grupy służą do zarządzania widocznością produktów dla deweloperów. Produkty są najpierw widoczne dla grup, a następnie deweloperzy w tych grupach mogą wyświetlać i subskrybować produkty, które są skojarzone z tymi grupami. 

Usługa API Management ma następujące niezmienne grupy systemowe:

* **Administratorzy** — do tej grupy należą administratorzy subskrypcji platformy Azure. Administratorzy zarządzają wystąpieniami usługi API Management, tworząc interfejsy API, operacje i produkty, które są używane przez deweloperów.
* **Deweloperzy** — do tej grupy należą uwierzytelnieni użytkownicy portalu dla deweloperów. Deweloperzy to klienci, którzy tworzą aplikacje przy użyciu interfejsów API. Deweloperzy otrzymują dostęp do portalu dla deweloperów i tworzą aplikacje, które wywołują operacje interfejsów API.
* **Goście** — do tej grupy należą nieuwierzytelnieni użytkownicy portalu dla deweloperów, tacy jak potencjalni klienci odwiedzający portal dla deweloperów wystąpienia usługi API Management. Mogą mieć przyznany pewien zakres dostępu tylko do odczytu, czyli np. możliwość wyświetlania interfejsów API, ale nie ich wywoływania.

Oprócz tych grup systemowych administratorzy mogą tworzyć niestandardowe grupy lub [wykorzystać zewnętrzne grupy w skojarzonych dzierżawach usługi Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Niestandardowe i zewnętrzne grupy mogą być używane razem z grupami systemowymi, zapewniając deweloperom widoczność produktów interfejsu API i dostęp do nich. Można na przykład utworzyć jedną grupę niestandardową dla deweloperów powiązanych z konkretną organizacją partnera i zezwolić im na dostęp do interfejsów API z produktów zawierających tylko odpowiednie interfejsy API. Użytkownik może należeć do więcej niż jednej grupy.

W tym przewodniku pokazano, jak Administratorzy wystąpienia API Management mogą dodawać nowe grupy i kojarzyć je z produktami i programistami.

Oprócz tworzenia grup i zarządzania nimi w portalu wydawców można tworzyć grupy i zarządzać nimi przy użyciu jednostki [grupy](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) interfejsu API REST API Management.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj zadania w tym artykule: [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Tworzenie grupy

W tej sekcji przedstawiono sposób dodawania nowej grupy do konta API Management.

1. Wybierz kartę **grupy** po lewej stronie ekranu.
2. Kliknij pozycję **+ Dodaj**.
3. Wprowadź unikatową nazwę grupy i opcjonalny opis.
4. Kliknij przycisk **Utwórz**.

    ![Dodawanie nowej grupy](./media/api-management-howto-create-groups/groups001.png)

Po utworzeniu grupy zostanie ona dodana do listy **grup** . <br/>Aby edytować **nazwę** lub **Opis** grupy, kliknij nazwę grupy i **Ustawienia**.<br/>Aby usunąć grupę, kliknij jej nazwę i naciśnij klawisz **delete**.

Po utworzeniu grupy można ją skojarzyć z produktami i programistami.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Kojarzenie grupy z produktem

1. Wybierz kartę **produkty** z lewej strony.
2. Kliknij nazwę żądanego produktu.
3. Naciśnij pozycję **Kontrola dostępu**.
4. Kliknij pozycję **+ Dodaj grupę**.

    ![Zrzut ekranu, który podświetla przycisk Dodaj grupę.](./media/api-management-howto-create-groups/groups002.png)
5. Wybierz grupę, którą chcesz dodać.

    ![Zrzut ekranu pokazujący wybraną grupę i wyróżniony przycisk Wybierz.](./media/api-management-howto-create-groups/groups003.png)

    Aby usunąć grupę z produktu, kliknij przycisk **Usuń**.

    ![Usuwanie grupy](./media/api-management-howto-create-groups/groups004.png)

Po skojarzeniu produktu z grupą deweloperzy z tej grupy mogą wyświetlać i subskrybować produkt.

> [!NOTE]
> Aby dodać grupy Azure Active Directory, zapoznaj się z tematem [jak autoryzować konta dewelopera przy użyciu Azure Active Directory na platformie Azure API Management](api-management-howto-aad.md).

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Kojarzenie grup z programistami

W tej sekcji pokazano, jak skojarzyć grupy z członkami.

1. Wybierz kartę **grupy** po lewej stronie ekranu.
2. Wybierz **członków**.

    ![Dodawanie członków](./media/api-management-howto-create-groups/groups005.png)
3. Naciśnij klawisze **+ Dodaj** i wybierz element członkowski.

    ![Zrzut ekranu, który podświetla przycisk Dodaj, wybrany użytkownik i przycisk Wybierz.](./media/api-management-howto-create-groups/groups006.png)
4. Naciśnij **pozycję Wybierz**.

Po dodaniu skojarzenia między deweloperem a grupą można wyświetlić ją na karcie **Użytkownicy** .

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki

* Po dodaniu dewelopera do grupy mogą oni wyświetlać i subskrybować produkty skojarzone z tą grupą. Aby uzyskać więcej informacji, zobacz [jak utworzyć i opublikować produkt na platformie Azure API Management][How create and publish a product in Azure API Management],
* Oprócz tworzenia grup i zarządzania nimi w portalu wydawców można tworzyć grupy i zarządzać nimi przy użyciu jednostki [grupy](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) interfejsu API REST API Management.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
