---
title: Uwierzytelnianie elementów runbook usługi Azure Automation za pomocą usługi Amazon Web Services
description: W tym artykule opisano sposób uwierzytelniania elementów Runbook za pomocą Amazon Web Services.
keywords: uwierzytelnianie aws, konfigurowanie aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83837193"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Uwierzytelnianie elementów runbook w usłudze Amazon Web Services

Typowe zadania dotyczące zasobów w usłudze Amazon Web Services (AWS) można zautomatyzować za pomocą elementów Runbook usługi Azure Automation. Wiele zadań w usłudze AWS można zautomatyzować przy użyciu elementów Runbook usługi Automation, podobnie jak za pomocą zasobów platformy Azure. W celu uwierzytelnienia musisz mieć subskrypcję platformy Azure.

## <a name="obtain-aws-subscription-and-credentials"></a>Uzyskaj subskrypcję usługi AWS i poświadczenia

Aby uwierzytelnić się za pomocą usługi AWS, musisz uzyskać subskrypcję usługi AWS i określić zestaw poświadczeń AWS do uwierzytelniania elementów Runbook uruchomionych z Azure Automation. Wymagane są określone poświadczenia — klucz dostępu AWS i klucz tajny. Zobacz [używanie poświadczeń AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Skonfiguruj konto usługi Automation

Możesz użyć istniejącego konta usługi Automation do uwierzytelniania za pomocą usługi AWS. Alternatywnie można przypisać konto dla elementów Runbook przeznaczonych dla zasobów AWS. W takim przypadku Utwórz nowe [konto usługi Automation](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Przechowywanie poświadczeń AWS

Poświadczenia AWS muszą być przechowywane jako zasoby w Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia klucza dostępu i klucza tajnego, zobacz [Zarządzanie kluczami dostępu dla konta usługi AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) . Gdy klucze są dostępne, Skopiuj identyfikator klucza dostępu i identyfikator klucza tajnego w bezpiecznym miejscu. Plik klucza można pobrać, aby przechowywać go w bezpiecznym miejscu.

## <a name="create-credential-asset"></a>Utwórz zasób poświadczeń

Po utworzeniu i skopiowaniu kluczy zabezpieczeń AWS należy utworzyć zasób poświadczeń przy użyciu konta usługi Automation. Zasób umożliwia bezpieczne przechowywanie kluczy AWS i odwoływanie się do nich w elementach Runbook. Zobacz [Tworzenie nowego zasobu poświadczeń przy użyciu Azure Portal](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Wprowadź następujące informacje AWS w udostępnionych polach:
    
* **Nazwa**  -  **AWScred**lub odpowiednią wartość zgodnie ze standardami nazewnictwa
* **Nazwa użytkownika** — identyfikator dostępu
* **Hasło** — nazwa klucza tajnego 

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć elementy Runbook w celu zautomatyzowania zadań w programie AWS, zobacz [Wdrażanie maszyny wirtualnej Amazon Web Services za pomocą elementu Runbook](automation-scenario-aws-deployment.md).