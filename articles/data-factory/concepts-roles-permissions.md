---
title: Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)
description: Opisuje role i uprawnienia wymagane do tworzenia fabryk danych i pracy z zasobami podrzędnymi.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364481"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano role wymagane do tworzenia zasobów Azure Data Factory i zarządzania nimi oraz uprawnienia przyznane przez te role.

## <a name="roles-and-requirements"></a>Role i wymagania

Aby utworzyć Data Factory wystąpienia, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli *współautor* , roli *właściciela* lub *administratorem* subskrypcji platformy Azure. W witrynie Azure Portal wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Uprawnienia**, aby wyświetlić uprawnienia, które masz w subskrypcji. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. 

Aby utworzyć zasoby podrzędne — w tym zestawy danych, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime — oraz nimi zarządzać w usłudze Data Factory, należy spełnić następujące wymagania:
- Aby utworzyć zasoby podrzędne i zarządzać nimi w Azure Portal, musisz należeć do roli **współautor Data Factory** na poziomie **grupy zasobów** lub nowszej.
- W przypadku tworzenia zasobów podrzędnych i zarządzania nimi za pomocą programu PowerShell lub zestawu SDK rola **współautora** na poziomie grupy zasobów lub wyższej jest wystarczająca.

Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz artykuł [Add roles (Dodawanie ról)](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Konfigurowanie uprawnień

Po utworzeniu Data Factory można zezwolić innym użytkownikom na współpracę z fabryką danych. Aby zapewnić ten dostęp innym użytkownikom, należy dodać je do wbudowanej roli **współautor Data Factory** w **grupie zasobów** zawierającej Data Factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Zakres roli współautor Data Factory

Członkostwo roli **współautor Data Factory** umożliwia użytkownikom wykonywanie następujących czynności:
- Twórz, Edytuj i usuwaj fabryki danych i zasoby podrzędne, w tym zestawy, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime.
- Wdróż szablony Menedżer zasobów. Wdrożenie Menedżer zasobów to metoda wdrażania używana przez Data Factory w Azure Portal.
- Zarządzanie alertami usługi App Insights dla fabryki danych.
- Utwórz bilety pomocy technicznej.

Aby uzyskać więcej informacji na temat tej roli, zobacz [Data Factory roli współautor](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Wdrożenie szablonu Menedżer zasobów

Rola **współautor Data Factory** , na poziomie grupy zasobów lub nowszej, umożliwia użytkownikom wdrażanie szablonów Menedżer zasobów. W związku z tym członkowie roli mogą używać szablonów Menedżer zasobów do wdrożenia zarówno fabryk danych, jak i ich zasobów podrzędnych, takich jak zestawy, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime. Członkostwo w tej roli nie zezwala użytkownikowi na tworzenie innych zasobów.

Uprawnienia do Azure Repos i GitHub są niezależne od uprawnień Data Factory. W związku z tym użytkownik z uprawnieniami repozytorium, które jest członkiem roli czytelnik, może edytować Data Factory zasoby podrzędne i zatwierdzić zmiany w repozytorium, ale nie może opublikować tych zmian.


> [!IMPORTANT]
> Wdrożenie szablonu Menedżer zasobów z rolą **współautor Data Factory** nie podwyższa poziomu uprawnień. Na przykład jeśli wdrażasz szablon, który tworzy maszynę wirtualną platformy Azure, a nie masz uprawnień do tworzenia maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem z powodu błędu autoryzacji.

   W kontekście publikowania **firma Microsoft. DataFactory/fabryka/** uprawnienie do zapisu ma zastosowanie do następujących trybów.
- To uprawnienie jest wymagane tylko w trybie na żywo, gdy klient modyfikuje parametry globalne.
- To uprawnienie jest zawsze wymagane w trybie git od momentu opublikowania klienta, należy zaktualizować obiekt fabryki z ostatnim IDENTYFIKATORem zatwierdzenia.

### <a name="custom-scenarios-and-custom-roles"></a>Scenariusze niestandardowe i role niestandardowe

Czasami może być konieczne przyznanie różnych poziomów dostępu dla różnych użytkowników usługi Data Factory. Na przykład:
- Może być potrzebna Grupa, w której użytkownicy mają uprawnienia tylko do określonej fabryki danych.
- Lub może być potrzebna Grupa, w której użytkownicy mogą monitorować tylko fabrykę danych (lub fabryki), ale nie mogą jej modyfikować.

Te scenariusze niestandardowe można osiągnąć, tworząc role niestandardowe i przypisując użytkownikom te role. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe na platformie Azure](..//role-based-access-control/custom-roles.md).

Poniżej przedstawiono kilka przykładów demonstrujących to, co można osiągnąć za pomocą ról niestandardowych:

- Zezwól użytkownikowi na tworzenie, edytowanie lub usuwanie wszystkich fabryk danych w grupie zasobów z Azure Portal.

  Przypisz wbudowaną rolę **współautor Data Factory** na poziomie grupy zasobów dla użytkownika. Jeśli chcesz zezwolić na dostęp do wszystkich fabryk danych w ramach subskrypcji, przypisz rolę na poziomie subskrypcji.

- Zezwól użytkownikom na wyświetlanie i monitorowanie fabryki danych, ale nie należy jej edytować ani zmieniać.

  Przypisz wbudowaną rolę **czytnika** do zasobu fabryki danych dla użytkownika.

- Zezwól użytkownikowi na edytowanie jednej fabryki danych w Azure Portal.

  Ten scenariusz wymaga dwóch przypisań ról.

  1. Przypisz wbudowaną rolę **współautor** na poziomie fabryki danych.
  2. Utwórz rolę niestandardową z uprawnieniem  **Microsoft. resources/Deployments/**. Przypisz tę rolę niestandardową do użytkownika na poziomie grupy zasobów.

- Zezwalaj użytkownikowi na testowanie połączenia w połączonej usłudze lub Podgląd danych w zestawie danych

    Utwórz niestandardową rolę z uprawnieniami dla następujących akcji: **Microsoft. DataFactory/Factors/getFeatureValue/Read** i **Microsoft. DataFactory/Factors/getDataPlaneAccess/Action**. Przypisz tę rolę niestandardową do zasobu fabryki danych dla użytkownika.

- Zezwól użytkownikowi na aktualizację fabryki danych przy użyciu programu PowerShell lub zestawu SDK, ale nie w Azure Portal.

  Przypisz wbudowaną rolę **współautor** do zasobu fabryki danych dla użytkownika. Ta rola umożliwia użytkownikowi wyświetlanie zasobów w Azure Portal, ale użytkownik nie może uzyskać dostępu do przycisków  **Publikuj** i **Opublikuj wszystkie** .


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [Omówienie definicji ról](../role-based-access-control/role-definitions.md)

- Dowiedz się więcej o [roli współautor](../role-based-access-control/built-in-roles.md#data-factory-contributor) **Data Factory rola współautor** Data Factory.
