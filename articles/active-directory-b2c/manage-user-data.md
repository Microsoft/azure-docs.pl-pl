---
title: Zarządzanie danymi użytkowników w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak usunąć lub wyeksportować dane użytkowników w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d9bdf7258296b82e65e03f6b8af8021b9a7be0f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94952475"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Zarządzanie danymi użytkowników w Azure Active Directory B2C

 W tym artykule omówiono sposób zarządzania danymi użytkownika w programie Azure Active Directory B2C (Azure AD B2C) przy użyciu operacji dostarczonych przez [interfejs API Microsoft Graph](/graph/use-the-api). Zarządzanie danymi użytkowników obejmuje usuwanie lub eksportowanie danych z dzienników inspekcji.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Usuwanie danych użytkownika

Dane użytkownika są przechowywane w katalogu Azure AD B2C i w dziennikach inspekcji. Wszystkie dane inspekcji użytkownika są przechowywane przez 7 dni w Azure AD B2C. Jeśli chcesz usunąć dane użytkownika w tym 7-dniowym okresie, możesz użyć operacji [usuwania użytkownika](/graph/api/user-delete) . Operacja usuwania jest wymagana dla każdej dzierżawy Azure AD B2C, w której mogą znajdować się dane.

Każdy użytkownik w Azure AD B2C ma przypisany identyfikator obiektu. Identyfikator obiektu zawiera niejednoznaczny identyfikator, który służy do usuwania danych użytkownika w Azure AD B2C. W zależności od architektury, identyfikator obiektu może być przydatnym identyfikatorem korelacji między innymi usługami, takimi jak finansowe, marketingowe i bazy danych zarządzania relacjami z klientami.

Najdokładniejszym sposobem uzyskania identyfikatora obiektu dla użytkownika jest uzyskanie go w ramach kursu uwierzytelniania z Azure AD B2C. Jeśli otrzymasz prawidłowe żądanie danych od użytkownika przy użyciu innych metod, proces offline, taki jak wyszukiwanie przez agenta obsługi klienta, może być konieczne, aby znaleźć użytkownika i zanotować identyfikator skojarzonego obiektu.

Poniższy przykład pokazuje możliwy przepływ usuwania danych:

1. Użytkownik loguje się i wybiera opcję **Usuń moje dane**.
2. Aplikacja oferuje opcję usuwania danych w sekcji administracji aplikacji.
3. Aplikacja wymusza uwierzytelnianie do Azure AD B2C. Azure AD B2C udostępnia token z IDENTYFIKATORem obiektu użytkownika z powrotem do aplikacji.
4. Token jest odbierany przez aplikację, a identyfikator obiektu służy do usuwania danych użytkownika za pomocą wywołania interfejsu API Microsoft Graph. Interfejs API Microsoft Graph usuwa dane użytkownika i zwraca kod stanu 200 OK.
5. Aplikacja organizuje usuwanie danych użytkownika w innych systemach organizacyjnych w razie konieczności przy użyciu identyfikatora obiektu lub innych identyfikatorów.
6. Aplikacja potwierdza usunięcie danych i udostępnia użytkownikowi następne kroki.

## <a name="export-customer-data"></a>Eksportowanie danych klienta

Proces eksportowania danych klienta z Azure AD B2C jest podobny do procesu usuwania.

Azure AD B2C dane użytkownika są ograniczone do:

- **Dane przechowywane w Azure Active Directory**: można pobrać dane z Azure AD B2C użytkownika uwierzytelniania przy użyciu identyfikatora obiektu lub dowolnej nazwy logowania, na przykład adresu e-mail lub nazwy użytkownika.
- **Raport dotyczący zdarzeń inspekcji specyficznych dla użytkownika**: dane można indeksować przy użyciu identyfikatora obiektu.

W poniższym przykładzie przepływu danych eksportu kroki, które są opisane jako wykonywane przez aplikację, można także wykonać przez proces zaplecza lub użytkownika z rolą administratora w katalogu:

1. Użytkownik loguje się do aplikacji. W razie konieczności Azure AD B2C wymusza uwierzytelnianie w usłudze Azure AD Multi-Factor Authentication.
2. Aplikacja używa poświadczeń użytkownika w celu wywołania Microsoft Graph operacji interfejsu API w celu pobrania atrybutów użytkownika. Interfejs API Microsoft Graph udostępnia dane atrybutów w formacie JSON. W zależności od schematu można ustawić zawartość tokenu identyfikatora, aby uwzględnić wszystkie dane osobowe użytkownika.
3. Aplikacja pobiera działanie inspekcji użytkownika. Interfejs API Microsoft Graph udostępnia dane zdarzenia do aplikacji.
4. Aplikacja agreguje dane i udostępni je użytkownikowi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać dostępem użytkowników do aplikacji, zobacz [Zarządzanie dostępem użytkowników](manage-user-access.md).