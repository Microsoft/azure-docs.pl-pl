---
title: Uwierzytelnianie elementów runbook usługi Azure Automation za pomocą usługi Amazon Web Services
description: W tym artykule opisano sposób tworzenia i sprawdzania poprawności poświadczeń w usłudze AWS dla elementów Runbook w usłudze Azure Automation zarządzającej zasobami usługi AWS.
keywords: uwierzytelnianie aws, konfigurowanie aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1bf60d17af1d9866de6a62ac538fa0bd9a15ce52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82113395"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Uwierzytelnianie elementów runbook usługi Azure Automation za pomocą usługi Amazon Web Services

Typowe zadania dotyczące zasobów w usłudze Amazon Web Services (AWS) można zautomatyzować za pomocą elementów Runbook usługi Azure Automation. Wiele zadań w usłudze AWS można zautomatyzować przy użyciu elementów Runbook usługi Automation, podobnie jak za pomocą zasobów platformy Azure. Wymagane są jedynie dwa elementy:

* Subskrypcja AWS i zestaw poświadczeń. W szczególności klucz dostępu AWS i klucz tajny. Więcej informacji można znaleźć w artykule [Using AWS Credentials (Korzystanie z poświadczeń usługi AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Subskrypcja platformy Azure i konto w usłudze Automation.

Aby wykonać uwierzytelnienie w usłudze AWS, należy określić zestaw poświadczeń usługi AWS do uwierzytelniania elementów Runbook uruchamianych z poziomu usługi Azure Automation. Jeśli masz już konto w usłudze Automation i chcesz go użyć do uwierzytelnienia w usłudze AWS, możesz wykonać kroki opisane w poniższej sekcji. Jeśli chcesz przeznaczyć konto dla elementów Runbook przeznaczonych dla zasobów AWS, należy najpierw utworzyć nowe [konto usługi Automation](automation-create-standalone-account.md) i pominąć ten krok w celu utworzenia konta Uruchom jako. Po utworzeniu konta postępuj zgodnie z poniższymi instrukcjami, aby zakończyć konfigurację.

## <a name="configure-automation-account"></a>Skonfiguruj konto usługi Automation

Aby usługa Azure Automation mogła się komunikować z usługą AWS, musisz najpierw pobrać poświadczenia usługi AWS i przechowywać je jako zasoby w usłudze Azure Automation. Wykonaj następujące czynności opisane w dokumencie usługi AWS [Zarządzanie kluczami dostępu konta usługi AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), aby utworzyć klucz dostępu i skopiować **identyfikator klucza dostępu** i **tajny klucz dostępu** (ewentualnie pobierz plik klucza, aby go bezpiecznie przechować w innym miejscu).

Po utworzeniu i skopiowaniu kluczy zabezpieczeń usługi AWS musisz utworzyć zasób poświadczeń przy użyciu konta usługi Azure Automation, aby bezpiecznie je przechowywać i odwoływać się do nich z poziomu elementów Runbook. Wykonaj kroki opisane w sekcji: **Aby utworzyć nowe poświadczenie** w obszarze [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) artykule i wprowadź następujące informacje:

1. W polu **Nazwa** wprowadź **AWScred** lub wartość odpowiadającą Twoim standardom nazewnictwa.
2. W polu **Nazwa użytkownika** wpisz swój **identyfikator dostępu** i **klucz dostępu klucza tajnego** w polu **hasło** i **Potwierdź hasło** .

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [Automating deployment of a VM in Amazon Web Services (Zautomatyzowane wdrażanie maszyn wirtualnych w usłudze Amazon Web Services)](automation-scenario-aws-deployment.md), aby dowiedzieć się, jak utworzyć elementy runbook w celu automatyzacji zadań w usłudze AWS.
