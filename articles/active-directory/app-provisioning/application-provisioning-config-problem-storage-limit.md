---
title: Wystąpił problem podczas zapisywania poświadczeń administratora Konfigurowanie aplikacji w galerii usługi Azure AD
description: Dowiedz się, jak rozwiązywać problemy związane z zapisywaniem poświadczeń administratora podczas konfigurowania aprowizacji użytkowników w aplikacji Galerii Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7a3340e72499087dce7773264272601dfce8a50f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266786"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Wystąpił problem podczas zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników w aplikacji Galerii Azure Active Directory 

W przypadku korzystania z Azure Portal do konfigurowania [automatycznej aprowizacji użytkowników](user-provisioning.md) dla aplikacji przedsiębiorstwa może wystąpić sytuacja, w której:

* **Poświadczenia administratora** wprowadzone dla aplikacji są prawidłowe i przycisk **Testuj połączenie** działa. Nie można jednak zapisać poświadczeń, a Azure Portal zwraca ogólny komunikat o błędzie.

Jeśli Rejestracja jednokrotna oparta na protokole SAML jest również skonfigurowana dla tej samej aplikacji, najprawdopodobniej przyczyną błędu jest przekroczenie wewnętrznego limitu magazynu dla poszczególnych aplikacji usługi Azure AD dla certyfikatów i poświadczeń.

Usługa Azure AD ma obecnie maksymalną pojemność magazynu wynoszącą 1024 bajtów dla wszystkich certyfikatów, tokenów tajnych, poświadczeń i powiązanych danych konfiguracji skojarzonych z pojedynczym wystąpieniem aplikacji (nazywanej także rekordem głównym usługi w usłudze Azure AD).

Po skonfigurowaniu logowania jednokrotnego opartego na protokole SAML certyfikat używany do podpisywania tokenów SAML jest przechowywany w tym miejscu i często wykorzystuje ponad 50% procent miejsca.

Wszelkie tajne tokeny, identyfikatory URI, adresy e-mail powiadomień, nazwy użytkowników i hasła wprowadzane podczas instalacji aprowizacji użytkowników mogą spowodować przekroczenie limitu magazynu.

## <a name="how-to-work-around-this-issue"></a>Jak obejść ten problem 

Istnieją dwa możliwe sposoby obejścia tego problemu Dzisiaj:

1. **Użyj dwóch wystąpień aplikacji z galerii, jednej do logowania jednokrotnego i jednego do aprowizacji użytkowników** — przejmowanie aplikacji galerii na przykład w celu [podniesienia](../saas-apps/linkedinelevate-tutorial.md) uprawnień do serwisu LinkedIn z galerii i skonfigurowania jej do logowania jednokrotnego. W celu aprowizacji Dodaj kolejne wystąpienie podniesienia uprawnień z galerii aplikacji usługi Azure AD, a następnie nadaj mu nazwę "podnoszenie uprawnień do serwisu LinkedIn (Inicjowanie obsługi administracyjnej)". W przypadku tego drugiego wystąpienia Skonfiguruj [Inicjowanie obsługi administracyjnej](../saas-apps/linkedinelevate-provisioning-tutorial.md), ale nie Logowanie jednokrotne. W przypadku korzystania z tego rozwiązania te same Użytkownicy i grupy muszą być [przypisane](../manage-apps/assign-user-or-group-access-portal.md) do obu aplikacji. 

2. **Zmniejsz ilość przechowywanych danych konfiguracyjnych** — wszystkie dane wprowadzone w sekcji [poświadczenia administratora](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) na karcie aprowizacji są przechowywane w tym samym miejscu co certyfikat SAML. Chociaż może nie być możliwe zmniejszenie długości wszystkich tych danych, niektóre opcjonalne pola konfiguracji, takie jak **wiadomości e-mail z powiadomieniem** , mogą zostać usunięte.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie aprowizacji użytkowników i cofanie aprowizacji w aplikacjach SaaS](user-provisioning.md)
