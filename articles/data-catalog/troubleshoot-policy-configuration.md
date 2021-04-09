---
title: Jak rozwiązywać problemy z Azure Data Catalog
description: W tym artykule opisano typowe problemy związane z rozwiązywaniem problemów dotyczących zasobów Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674992"
---
# <a name="troubleshooting-azure-data-catalog"></a>Rozwiązywanie problemów z usługą Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

W tym artykule opisano typowe problemy związane z rozwiązywaniem problemów dotyczących zasobów Azure Data Catalog. 

## <a name="functionality-limitations"></a>Ograniczenia funkcjonalności

W przypadku korzystania z Azure Data Catalog następujące funkcje są ograniczone:

- Konta typu **rola gościa** nie są obsługiwane. Nie można dodać kont Gości jako użytkowników Azure Data Catalog, a użytkownicy-Goście nie mogą korzystać z portalu pod adresem [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- Tworzenie Azure Data Catalog zasobów przy użyciu szablonów Azure Resource Manager lub poleceń Azure PowerShell nie jest obsługiwane.

- Nie można przenieść zasobu Azure Data Catalog między dzierżawami platformy Azure.

## <a name="azure-active-directory-policy-configuration"></a>Konfiguracja zasad usługi Azure Active Directory

W niektórych sytuacjach po zalogowaniu się w portalu usługi Azure Data Catalog przy próbie logowania się za pomocą narzędzia do rejestracji źródła danych występuje komunikat o błędzie, który uniemożliwia logowanie. Ten błąd może wystąpić w przypadku korzystania z sieci firmowej lub połączenia spoza sieci firmowej.

Narzędzie rejestracji używa *uwierzytelniania formularzy* do weryfikowania logowania użytkowników w usłudze Azure Active Directory. Aby logowanie się powiodło, administrator usługi Azure Active Directory musi włączyć uwierzytelnianie formularzy za pomocą *globalnych zasad uwierzytelniania*.

Pozwoli to na włączenie uwierzytelniania oddzielnie dla połączeń intranetowych i ekstranetowych, jak pokazano na poniższej ilustracji. Błędy logowania mogą wystąpić, jeśli uwierzytelnianie formularzy nie jest włączone dla sieci, z której jest nawiązywane połączenie.

 ![Globalne zasady uwierzytelniania usługi Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zasad uwierzytelniania](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie usługi Azure Data Catalog](data-catalog-get-started.md)
