---
title: Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)
description: W tym artykule opisano role i uprawnienia wymagane do tworzenia fabryk danych i pracy z zasobami podrzędnymi.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8b7791c5c04e986b30959d2fcae17142fdd8b7ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418324"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano role wymagane do tworzenia zasobów usługi Azure Data Factory i zarządzanie nimi oraz uprawnienia przyznane przez te role.

## <a name="roles-and-requirements"></a>Role i wymagania

Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli *współautora* lub *właściciela* albo *administratorem* subskrypcji platformy Azure. W witrynie Azure Portal wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Uprawnienia**, aby wyświetlić uprawnienia, które masz w subskrypcji. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. 

Aby utworzyć zasoby podrzędne — w tym zestawy danych, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime — oraz nimi zarządzać w usłudze Data Factory, należy spełnić następujące wymagania:
- Aby utworzyć zasoby podrzędne i nimi zarządzać, trzeba należeć do roli **Współautor w usłudze Data Factory** na poziomie grupy zasobów lub wyższej.
- W przypadku tworzenia zasobów podrzędnych i zarządzania nimi za pomocą programu PowerShell lub zestawu SDK rola **współautora** na poziomie grupy zasobów lub wyższej jest wystarczająca.

Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz artykuł [Add roles (Dodawanie ról)](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Konfigurowanie uprawnień

Po utworzeniu fabryki danych można umożliwić innym użytkownikom pracę z fabryką danych. Aby nadać ten dostęp innym użytkownikom, należy dodać je do wbudowanej roli **współautora fabryki danych** w grupie zasobów zawierającej fabrykę danych.

### <a name="scope-of-the-data-factory-contributor-role"></a>Zakres roli współautora fabryki danych

Członkostwo w roli **współautora fabryki danych** umożliwia użytkownikom wykonać następujące czynności:
- Tworzenie, edytowanie i usuwanie fabryk danych i zasobów podrzędnych, w tym zestawów danych, połączonych usług, potoków, wyzwalaczy i środowiskach uruchomieniowych integracji.
- Wdrażanie szablonów Menedżera zasobów. Wdrożenie menedżera zasobów to metoda wdrażania używana przez usługę Data Factory w witrynie Azure portal.
- Zarządzanie alertami usługi App Insights dla fabryki danych.
- Tworzenie biletów pomocy technicznej.

Aby uzyskać więcej informacji na temat tej roli, zobacz [Rola współautora fabryki danych](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Wdrażanie szablonu Menedżera zasobów

Rola **Współautora fabryki danych** na poziomie grupy zasobów lub wyższej umożliwia użytkownikom wdrażanie szablonów Menedżera zasobów. W rezultacie członkowie roli mogą używać szablonów Menedżera zasobów do wdrażania zarówno fabryk danych, jak i ich zasobów podrzędnych, w tym zestawów danych, połączonych usług, potoków, wyzwalaczy i środowiskach uruchomień integracji. Członkostwo w tej roli nie pozwala użytkownikowi tworzyć inne zasoby, jednak.

Uprawnienia do repozytoriów platformy Azure i usługi GitHub są niezależne od uprawnień usługi Data Factory. W rezultacie użytkownik z uprawnieniami repozytorium, który jest tylko członkiem roli Czytnik, może edytować zasoby podrzędne fabryki danych i zatwierdzać zmiany w repozytorium, ale nie może publikować tych zmian.

> [!IMPORTANT]
> Wdrożenie szablonu Menedżera zasobów z rolą **Współautor fabryki danych** nie podnosi uprawnień. Na przykład jeśli wdrożysz szablon, który tworzy maszynę wirtualną platformy Azure i nie masz uprawnień do tworzenia maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem z błędem autoryzacji.

### <a name="custom-scenarios-and-custom-roles"></a>Scenariusze niestandardowe i role niestandardowe

Czasami może być konieczne przyznanie różnych poziomów dostępu dla różnych użytkowników fabryki danych. Przykład:
- Może być potrzebna grupa, w której użytkownicy mają uprawnienia tylko do określonej fabryki danych.
- Możesz też potrzebować grupy, w której użytkownicy mogą monitorować tylko fabrykę danych (lub fabryki), ale nie można jej zmodyfikować.

Te scenariusze niestandardowe można osiągnąć, tworząc role niestandardowe i przypisując użytkowników do tych ról. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Role niestandardowe na platformie Azure](..//role-based-access-control/custom-roles.md).

Oto kilka przykładów, które pokazują, co można osiągnąć za pomocą ról niestandardowych:

- Pozwól użytkownikowi tworzyć, edytować lub usuwać dowolną fabrykę danych w grupie zasobów z witryny Azure Portal.

  Przypisz wbudowaną rolę **współautora usługi Data Factory** na poziomie grupy zasobów dla użytkownika. Jeśli chcesz zezwolić na dostęp do dowolnej fabryki danych w ramach subskrypcji, przypisz rolę na poziomie subskrypcji.

- Pozwól użytkownikowi przeglądać (czytać) i monitorować fabrykę danych, ale nie edytuj ani nie zmieniaj jej.

  Przypisz wbudowaną rolę **czytnika** do zasobu fabryki danych dla użytkownika.

- Pozwól użytkownikowi edytować pojedynczą fabrykę danych w witrynie Azure portal.

  Ten scenariusz wymaga dwóch przypisań ról.

  1. Przypisz wbudowaną rolę **współautora** na poziomie fabryki danych.
  2. Utwórz rolę niestandardową z uprawnieniem **Microsoft.Resources/deployments/**. Przypisz tę rolę niestandardową do użytkownika na poziomie grupy zasobów.

- Zezwalanie użytkownikowi na testowanie połączenia tylko w połączonej usłudze

    Utwórz rolę roli niestandardowej z uprawnieniami dla następujących akcji: **Microsoft.DataFactory/factorys/getFeatureValue/read** i **Microsoft.DataFactory/factorys/getDataPlaneAccess/read**. Przypisz tę rolę niestandardową do zasobu fabryki danych dla użytkownika.

- Pozwól użytkownikowi zaktualizować fabrykę danych z programu PowerShell lub SDK, ale nie w witrynie Azure portal.

  Przypisz wbudowaną rolę **współautora** do zasobu fabryki danych dla użytkownika. Ta rola umożliwia użytkownikowi wyświetlanie zasobów w witrynie Azure portal, ale użytkownik nie może uzyskać dostępu do **przycisków Publikuj** i **Publikuj wszystkie.**

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [poznaj definicje ról](../role-based-access-control/role-definitions.md)

- Dowiedz się więcej o roli **współautora fabryki danych** — [rola współautora fabryki danych](../role-based-access-control/built-in-roles.md#data-factory-contributor).
