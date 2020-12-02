---
title: Jak używać uprawnień w chmurze Azure wiosennej
description: W tym artykule opisano sposób tworzenia ról niestandardowych dla uprawnień w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498646"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Jak używać uprawnień w chmurze Azure wiosennej
W tym artykule opisano sposób tworzenia ról niestandardowych delegowania uprawnień do zasobów chmurowych z platformy Azure. Role niestandardowe zwiększają [wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md) z różnymi uprawnieniami magazynowymi.

Zostaną zaimplementowane następujące role niestandardowe:

* **Rola dewelopera**: 
    * Wdrażanie
    * Testowanie
    * Ponowne uruchamianie aplikacji
    * Można stosować i wprowadzać zmiany w konfiguracjach aplikacji w repozytorium git
    * Może pobrać strumień dziennika
* **Ops-site niezawodność Inżynieria**: 
    * Ponowne uruchamianie aplikacji
    * Pobierz strumienie dzienników
    * Nie można wprowadzać zmian w aplikacjach lub konfiguracjach
* **Rola akcji Azure Pipelines/Jenkins/GitHub**:
    * Może wykonywać operacje tworzenia, odczytu, aktualizacji i usuwania
    * Może tworzyć i konfigurować wszystkie elementy w chmurze i aplikacjach ze sprężyną platformy Azure w ramach usługi Service instance: Azure Pipelines, Jenkins lub GitHub, przy użyciu szablonów Terraform lub ARM

## <a name="define-developer-role"></a>Zdefiniuj rolę dewelopera

Rola deweloper obejmuje uprawnienia do ponownego uruchamiania aplikacji i wyświetlania ich strumieni dzienników, ale nie może wprowadzać zmian w aplikacjach, konfiguracji.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Przejdź do kontroli dostępu do subskrypcji i grupy zasobów (IAM)

Wykonaj następujące kroki, aby rozpocząć Definiowanie roli.

1. W Azure Portal Otwórz subskrypcję i grupę zasobów, w której chcesz przypisać rolę niestandardową.
2. Otwórz **kontrolę dostępu (IAM)**.
3. Kliknij pozycję **+ Dodaj**.
4. Kliknij pozycję **Dodaj rolę niestandardową**.
5. Kliknij przycisk **Dalej**.

   ![Tworzenie roli niestandardowej](media/spring-cloud-permissions/create-custom-role.png)

6. Kliknij pozycję **Dodaj uprawnienia**.

   ![Rozpoczęcie dodawania uprawnień](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Wyszukaj uprawnienia chmury Azure wiosny:
7. W polu wyszukiwania Wyszukaj *aplikację Microsoft. app*.
Wybierz pozycję *Microsoft Azure wiosną*.

   ![Wybierz chmurę wiosenną platformy Azure](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Wybierz uprawnienia dla roli Deweloper:

Z **Microsoft. AppPlatform/Sprężyna** wybierz:
* Zapis: Tworzenie lub aktualizowanie wystąpienia usługi w chmurze Azure wiosny
* Odczytaj: Pobieranie wystąpienia usługi w chmurze Azure wiosennej
* Inne: Lista kluczy testu wystąpienia usługi w chmurze Azure wiosny

W **witrynie Microsoft. AppPlatform/wiosenn/Apps** wybierz pozycję:
* Read: Read Microsoft Azure wiosennej aplikacji w chmurze
* Inne: Pobierz adres URL przekazywania zasobu aplikacji w chmurze Microsoft Azure sprężyny

W obszarze **Microsoft. AppPlatform/Sprężyna/aplikacje/powiązania** wybierz pozycję:
* Odczytaj: odczyt powiązania aplikacji w chmurze Microsoft Azure wiosennej

W **witrynie Microsoft. AppPlatform/wiosenn/Apps/Deployments** wybierz pozycję:
* Write: Write Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Read: Read Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: rozpoczęcie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: zatrzymywanie wdrażania aplikacji w chmurze Microsoft Azure sprężyny
* Inne: ponowne uruchamianie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: Pobierz adres URL pliku dziennika wdrażania aplikacji w chmurze Microsoft Azure sprężyny

W obszarze **Microsoft. AppPlatform/Sprężyna/aplikacje/domeny** wybierz pozycję:
* Odczytaj: odczyt domeny niestandardowej aplikacji w chmurze Microsoft Azure wiosennej

W **witrynie Microsoft. AppPlatform/wiosenn/Certificates** wybierz pozycję:
* Odczytaj: odczyt certyfikatu chmury Microsoft Azure wiosennej

W **witrynie Microsoft. AppPlatform/Locations/operationResults/sprężynowe** wybierz pozycję:
* Odczyt: wynik operacji odczytu

W **witrynie Microsoft. AppPlatform/Locations/operationStatus/operationId** wybierz pozycję:
* Odczytaj: stan operacji odczytu

    [![Utwórz uprawnienia ](media/spring-cloud-permissions/developer-permissions-box.png) Developler](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Kliknij pozycję **Dodaj**.

10. Przejrzyj uprawnienia.

11. Kliknij pozycję **Przejrzyj i utwórz**.

## <a name="define-devops-engineer-role"></a>Zdefiniuj rolę Inżyniera DevOps
Ta procedura definiuje rolę z uprawnieniami do wdrażania, testowania i ponownego uruchamiania aplikacji w chmurze usługi Azure wiosennej.

1. Powtórz procedurę, aby nawigować po stronie subskrypcja, Grupa zasobów i kontrola dostępu (IAM).
2. Wybierz uprawnienia dla roli inżyniera DevOps:

Z **Microsoft. AppPlatform/Sprężyna** wybierz:
* Zapis: Tworzenie lub aktualizowanie wystąpienia usługi w chmurze Azure wiosny
* Usuwanie: usuwanie wystąpienia usługi w chmurze Azure wiosennej
* Odczytaj: Pobieranie wystąpienia usługi w chmurze Azure wiosennej
* Inne: Włącz punkt końcowy testu wystąpienia usługi w chmurze ze sprężyną Azure
* Inne: Wyłącz punkt końcowy testowego wystąpienia usługi w chmurze Azure wiosennej
* Inne: Lista kluczy testu wystąpienia usługi w chmurze Azure wiosny
* Inne: Wygeneruj ponownie klucz testu wystąpienia usługi w chmurze wiosny Azure

W **witrynie Microsoft. AppPlatform/wiosenn/Apps** wybierz pozycję:
* Zapis: zapisywanie aplikacji w chmurze Microsoft Azure wiosennej
* Usuwanie: Usuwanie aplikacji w chmurze Microsoft Azure wiosennej
* Read: Read Microsoft Azure wiosennej aplikacji w chmurze
* Inne: Pobierz adres URL przekazywania zasobu aplikacji w chmurze Microsoft Azure sprężyny
* Inne: Weryfikuj domenę niestandardową aplikacji w chmurze Microsoft Azure wiosennej

W obszarze **Microsoft. AppPlatform/Sprężyna/aplikacje/powiązania** wybierz pozycję:
* Zapis: zapisywanie powiązania aplikacji w chmurze Microsoft Azure wiosennej
* Usuwanie: Usuwanie powiązania aplikacji w chmurze Microsoft Azure wiosennej
* Odczytaj: odczyt powiązania aplikacji w chmurze Microsoft Azure wiosennej

W **witrynie Microsoft. AppPlatform/wiosenn/Apps/Deployments** wybierz pozycję:
* Write: Write Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Usuwanie: usuwanie wdrożenia aplikacji w chmurze ze sprężyną Azure
* Read: Read Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: rozpoczęcie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: zatrzymywanie wdrażania aplikacji w chmurze Microsoft Azure sprężyny
* Inne: ponowne uruchamianie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: Pobierz adres URL pliku dziennika wdrażania aplikacji w chmurze Microsoft Azure sprężyny

W **witrynie Microsoft. AppPlatform/wiosenn/Apps/Deployments/SKU** wybierz pozycję:
* Odczytaj: lista dostępnych jednostek SKU wdrożenia aplikacji

W **witrynie Microsoft. AppPlatform/Locations** wybierz pozycję:
* Inne: Sprawdź dostępność nazwy

W witrynie Microsoft. AppPlatform/Locations/operationResults/sprężynowe SELECT: Read: odczyt wynik operacji

W **witrynie Microsoft. AppPlatform/Locations/operationStatus/operationId** wybierz pozycję:
* Odczytaj: stan operacji odczytu

Z **Microsoft. AppPlatform/SKU** wybierz:
* Odczytaj: lista dostępnych jednostek SKU

   [![Uprawnienia ](media/spring-cloud-permissions/dev-ops-permissions.png) dev/op](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Kliknij pozycję **Dodaj**.

4. Przejrzyj uprawnienia.

5. Kliknij pozycję **Przejrzyj i utwórz**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definiowanie roli Ops-site niezawodność Inżynieria
Ta procedura definiuje rolę z uprawnieniami do wdrażania, testowania i ponownego uruchamiania aplikacji w chmurze usługi Azure wiosennej.

1. Powtórz procedurę, aby nawigować po stronie subskrypcja, Grupa zasobów i kontrola dostępu (IAM).

2. Wybierz uprawnienia dla roli Ops-site niezawodność Inżynieria:

Z **Microsoft. AppPlatform/Sprężyna** wybierz:
* Odczytaj: Pobieranie wystąpienia usługi w chmurze Azure wiosennej
* Inne: Lista kluczy testu wystąpienia usługi w chmurze Azure wiosny

W **witrynie Microsoft. AppPlatform/wiosenn/Apps** wybierz pozycję:
* Read: Read Microsoft Azure wiosennej aplikacji w chmurze

W **witrynie Microsoft. AppPlatform/Apps/Deployments** wybierz pozycję:
* Read: Read Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: rozpoczęcie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: zatrzymywanie wdrażania aplikacji w chmurze Microsoft Azure sprężyny
* Inne: ponowne uruchamianie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze

W **witrynie Microsoft. AppPlatform/Locations/operationResults/sprężynowe** wybierz pozycję:
* Odczyt: wynik operacji odczytu

W **witrynie Microsoft. AppPlatform/Locations/operationStatus/operationId** wybierz pozycję:
* Odczytaj: stan operacji odczytu

   [![Operacje Ops/SRE ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Kliknij pozycję **Dodaj**.

4. Przejrzyj uprawnienia.

5. Kliknij pozycję **Przejrzyj i utwórz**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Zdefiniuj rolę Azure Pipelines/aprowizacji
Ta rola akcji Jenkins/GitHub umożliwia tworzenie i Konfigurowanie wszystkich elementów w chmurze i aplikacjach ze sprężyną platformy Azure z wystąpieniem usługi. Ta rola służy do zwalniania i wdrażania kodu.

1. Powtórz procedurę, aby nawigować po stronie subskrypcja, Grupa zasobów i kontrola dostępu (IAM).

2. Otwórz Opcje **uprawnień** .

3. Wybierz uprawnienia dla roli Azure Pipelines/aprowizacji:
  
Z **Microsoft. AppPlatform/Sprężyna** wybierz:
* Zapis: Tworzenie lub aktualizowanie wystąpienia usługi w chmurze Azure wiosny
* Usuwanie: usuwanie wystąpienia usługi w chmurze Azure wiosennej
* Odczytaj: Pobieranie wystąpienia usługi w chmurze Azure wiosennej
* Inne: Włącz punkt końcowy testu wystąpienia usługi w chmurze ze sprężyną Azure
* Inne: Wyłącz punkt końcowy testowego wystąpienia usługi w chmurze Azure wiosennej
* Inne: Lista kluczy testu wystąpienia usługi w chmurze Azure wiosny
* Inne: Wygeneruj ponownie klucz testu wystąpienia usługi w chmurze wiosny Azure

W **witrynie Microsoft. AppPlatform/wiosenn/Apps** wybierz pozycję:
* Zapis: zapisywanie aplikacji w chmurze Microsoft Azure wiosennej
* Usuwanie: Usuwanie aplikacji w chmurze Microsoft Azure wiosennej
* Read: Read Microsoft Azure wiosennej aplikacji w chmurze
* Inne: Pobierz adres URL przekazywania zasobu aplikacji w chmurze Microsoft Azure sprężyny
* Inne: Weryfikuj domenę niestandardową aplikacji w chmurze Microsoft Azure wiosennej

W obszarze **Microsoft. AppPlatform/Sprężyna/aplikacje/powiązania** wybierz pozycję:
* Zapis: zapisywanie powiązania aplikacji w chmurze Microsoft Azure wiosennej
* Usuwanie: Usuwanie powiązania aplikacji w chmurze Microsoft Azure wiosennej
* Odczytaj: odczyt powiązania aplikacji w chmurze Microsoft Azure wiosennej

W **witrynie Microsoft. AppPlatform/wiosenn/Apps/Deployments** wybierz pozycję:
* Write: Write Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Usuwanie: usuwanie wdrożenia aplikacji w chmurze ze sprężyną Azure
* Read: Read Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: rozpoczęcie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: zatrzymywanie wdrażania aplikacji w chmurze Microsoft Azure sprężyny
* Inne: ponowne uruchamianie Microsoft Azure wiosenne wdrożenie aplikacji w chmurze
* Inne: Pobierz adres URL pliku dziennika wdrażania aplikacji w chmurze Microsoft Azure sprężyny

Z **Microsoft. AppPlatform/SKU** wybierz:
* Odczytaj: lista dostępnych jednostek SKU

W **witrynie Microsoft. AppPlatform/Locations** wybierz pozycję:
* Inne: Sprawdź dostępność nazwy

W **witrynie Microsoft. AppPlatform/Locations/operationResults/sprężynowe** wybierz pozycję:
* Odczyt: wynik operacji odczytu

W **witrynie Microsoft. AppPlatform/Locations/operationStatus/operationId** wybierz pozycję:
* Odczytaj: stan operacji odczytu

Z **Microsoft. AppPlatform/SKU** wybierz:
* Odczytaj: lista dostępnych jednostek SKU

   [![Uprawnienia ](media/spring-cloud-permissions/pipelines-permissions-box.png) potoków](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Kliknij pozycję **Dodaj**.

5. Przejrzyj uprawnienia.

6. Kliknij pozycję **Przejrzyj i utwórz**.


## <a name="see-also"></a>Zobacz też
* [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure za pomocą witryny Azure Portal](../role-based-access-control/custom-roles-portal.md)

Aby uzyskać więcej informacji na temat trzech metod, które definiują uprawnienia niestandardowe, zobacz:
* [Klonowanie roli](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Rozpoczynanie od zera](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Rozpocznij od formatu JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)