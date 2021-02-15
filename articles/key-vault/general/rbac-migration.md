---
title: Migrowanie do kontroli dostępu opartej na rolach na platformie Azure | Microsoft Docs
description: Dowiedz się, jak przeprowadzić migrację z zasad dostępu do magazynu do ról platformy Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e7a8fd53e78e1aeab9db5af0432d0c3f1d786823
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526956"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrowanie z zasad dostępu do magazynu do modelu uprawnień kontroli dostępu opartej na rolach na platformie Azure

Model zasad dostępu do magazynu to istniejący system autoryzacji oparty na Key Vault w celu zapewnienia dostępu do kluczy, wpisów tajnych i certyfikatów. Dostęp można kontrolować, przypisując indywidualne uprawnienia do podmiotu zabezpieczeń (użytkownika, grupy, nazwy głównej usługi, tożsamości zarządzanej) w Key Vault zakresie. 

Kontrola dostępu oparta na rolach (Azure RBAC) to system autoryzacji oparty na [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure. Dzięki funkcji RBAC platformy Azure można kontrolować dostęp do zasobów przez tworzenie przypisań ról, które składają się z trzech elementów: podmiot zabezpieczeń, definicja roli (wstępnie zdefiniowany zestaw uprawnień) i zakres (Grupa zasobów lub pojedynczy zasób). Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md).

Przed przeprowadzeniem migracji do usługi Azure RBAC ważne jest zapoznanie się z jej zaletami i ograniczeniami.

Korzyści wynikające z korzystania z usługi Azure RBAC Key i zasad dostępu do magazynu:
- Zapewnia ujednolicony model kontroli dostępu dla zasobów platformy Azure — ten sam interfejs API w ramach usług platformy Azure
- Scentralizowane zarządzanie dostępem dla administratorów — zarządzanie wszystkimi zasobami platformy Azure w jednym widoku
- Integracja z [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) na potrzeby kontroli dostępu opartej na czasie
- Odmów przypisań — możliwość wykluczenia podmiotu zabezpieczeń w określonym zakresie. Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów na platformie Azure](../../role-based-access-control/deny-assignments.md)

Wady RBAC platformy Azure:
- Opóźnienie dla przypisań ról — stosowanie przypisywania ról może potrwać kilka minut. Zasady dostępu do magazynu są przypisywane natychmiastowo.
- Ograniczona liczba przypisań ról — 2000 ról przydziałów na subskrypcję a 1024 zasad dostępu na Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Zasady dostępu do mapowania ról platformy Azure

Usługa Azure RBAC ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów usługi i zarządzanych tożsamości. Jeśli wbudowane role nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md).

Key Vault wbudowane role dla kluczy, certyfikatów i zarządzania dostępem do wpisów tajnych:
- Key Vault administrator
- Key Vault czytelnik
- Key Vault oficera certyfikatów
- Key Vault oficer kryptograficzny
- Key Vault użytkownika kryptograficznego
- Key Vault użytkownika szyfrowania usługi kryptograficznej
- Key Vault oficerów tajnych
- Użytkownik Key Vault Secret

Aby uzyskać więcej informacji na temat istniejących ról wbudowanych, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md) .

Zasady dostępu do magazynu można przypisywać przy użyciu indywidualnie wybranych uprawnień lub ze wstępnie zdefiniowanymi szablonami uprawnień.

Zasady dostępu wstępnie zdefiniowane szablony uprawnień:
- Klucz, wpis tajny, zarządzanie certyfikatami
- Zarządzanie kluczami tajnymi Key &
- Zarządzanie certyfikatami tajnymi &
- Zarządzanie kluczami
- Zarządzanie kluczami tajnymi
- Zarządzanie certyfikatami
- SQL Server Connector
- Azure Data Lake Storage lub Azure Storage
- Azure Backup
- Klucz klienta usługi Exchange Online
- Klucz klienta usługi SharePoint Online
- Usługa Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Mapowanie zasad dostępu do usługi Azure Roles
| Szablon zasad dostępu | Operacje | Rola na platformie Azure |
| --- | --- | --- |
| Klucz, wpis tajny, zarządzanie certyfikatami | Klucze: wszystkie operacje <br>Certyfikaty: wszystkie operacje<br>Wpisy tajne: wszystkie operacje | Key Vault administrator |
| Zarządzanie kluczami tajnymi Key & | Klucze: wszystkie operacje <br>Wpisy tajne: wszystkie operacje| Key Vault oficer kryptograficzny <br> Key Vault oficerów tajnych |
| Zarządzanie certyfikatami tajnymi & | Certyfikaty: wszystkie operacje <br>Wpisy tajne: wszystkie operacje| Oficer certyfikatów Key Vault <br> Key Vault oficerów tajnych|
| Zarządzanie kluczami | Klucze: wszystkie operacje| Key Vault oficer kryptograficzny|
| Zarządzanie kluczami tajnymi | Wpisy tajne: wszystkie operacje| Key Vault oficerów tajnych|
| Zarządzanie certyfikatami | Certyfikaty: wszystkie operacje | Oficer certyfikatów Key Vault|
| SQL Server Connector | Klucze: pobieranie, wyświetlanie, zawijanie klucza, odpakowywanie klucza | Key Vault użytkownika szyfrowania usługi kryptograficznej|
| Azure Data Lake Storage lub Azure Storage | Klucze: pobieranie, wyświetlanie, odpakowywanie klucza | Nie dotyczy<br> Wymagana rola niestandardowa|
| Azure Backup | Klucze: pobieranie, wyświetlanie, tworzenie kopii zapasowej<br> Certyfikat: Get, list, Backup | Nie dotyczy<br> Wymagana rola niestandardowa|
| Klucz klienta usługi Exchange Online | Klucze: pobieranie, wyświetlanie, zawijanie klucza, odpakowywanie klucza | Key Vault użytkownika szyfrowania usługi kryptograficznej|
| Klucz klienta usługi Exchange Online | Klucze: pobieranie, wyświetlanie, zawijanie klucza, odpakowywanie klucza | Key Vault użytkownika szyfrowania usługi kryptograficznej|
| Usługa Azure Information BYOK | Klucze: pobieranie, odszyfrowywanie, podpisywanie | Nie dotyczy<br>Wymagana rola niestandardowa|


## <a name="assignment-scopes-mapping"></a>Mapowanie zakresów przypisania  

Usługa Azure RBAC dla Key Vault umożliwia przypisywanie ról w następujących zakresach:
- Grupa zarządzania
- Subskrypcja
- Grupa zasobów
- Zasób Key Vault
- Pojedynczy klucz, klucz tajny i certyfikat

Model uprawnień zasad dostępu do magazynu jest ograniczony do przypisywania zasad tylko na poziomie zasobów Key Vault. 

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest posiadanie jednego magazynu kluczy dla każdej aplikacji i zarządzanie dostępem na poziomie magazynu kluczy. Istnieją scenariusze, w których zarządzanie dostępem w innych zakresach może uprościć zarządzanie dostępem.

- * * Infrastruktura, administratorzy zabezpieczeń i operatorzy: Zarządzanie grupą magazynów kluczy na poziomie grupy zarządzania, subskrypcji lub grupy zasobów z zasadami dostępu do magazynu wymaga zachowania zasad dla każdego magazynu kluczy. Usługa Azure RBAC umożliwia tworzenie jednego przypisania roli w grupie zarządzania, subskrypcji lub grupie zasobów. To przypisanie będzie miało zastosowanie do wszystkich nowych magazynów kluczy utworzonych w tym samym zakresie. W tym scenariuszu zaleca się używanie Privileged Identity Management z dostępem just in Time do zapewniania stałego dostępu.
 
- * * Aplikacje: istnieją scenariusze, gdy aplikacja będzie musiała udostępniać wpis tajny innym aplikacjom. Przy użyciu zasad dostępu do magazynu należy utworzyć osobny Magazyn kluczy, aby uniknąć udzielenia dostępu do wszystkich wpisów tajnych. Usługa Azure RBAC umożliwia przypisywanie roli z zakresem dla poszczególnych wpisów tajnych zamiast jednego magazynu kluczy.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Zasady dostępu magazynu do kroków migracji usługi Azure RBAC
Istnieje wiele różnic między modelem uprawnień usługi Azure RBAC i zasadami dostępu do magazynu. W celu uniknięcia przestojów podczas migracji zaleca się wykonanie poniższych kroków.
 
1. **Zidentyfikuj i przypisz role**: Zidentyfikuj wbudowane role na podstawie powyższej tabeli mapowania i w razie konieczności Utwórz role niestandardowe. Przypisywanie ról w zakresach na podstawie wskazówek dotyczących mapowania zakresów. Aby uzyskać więcej informacji na temat sposobu przypisywania ról do magazynu kluczy, zobacz [zapewnianie dostępu do Key Vault przy użyciu kontroli dostępu opartej na rolach na platformie Azure](rbac-guide.md)
1. **Weryfikowanie przypisania ról**: propagowanie przypisań ról w usłudze Azure RBAC może potrwać kilka minut. Aby uzyskać informacje na temat sposobu sprawdzania przypisań ról, zobacz [Lista przypisań ról w zakresie](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Konfigurowanie monitorowania i alertów w magazynie kluczy**: ważne jest włączenie rejestrowania i konfiguracji alertów dotyczących wyjątków odmowy dostępu. Aby uzyskać więcej informacji, zobacz [monitorowanie i zgłaszanie alertów dla Azure Key Vault](./alert.md)
1. **Ustaw model uprawnień kontroli dostępu opartej na rolach na platformie Azure na Key Vault**: włączenie modelu uprawnień RBAC platformy Azure spowoduje unieważnienie wszystkich istniejących zasad dostępu. Jeśli wystąpi błąd, model uprawnień może zostać przełączony z powrotem, a wszystkie istniejące zasady dostępu pozostają nienaruszone.

> [!NOTE]
> Zmiana modelu uprawnień wymaga uprawnienia "Microsoft. Authorization/roleAssignments/Write", które jest częścią ról [administratorów dostępu](../../role-based-access-control/built-in-roles.md#user-access-administrator) [właściciela](../../role-based-access-control/built-in-roles.md#owner) i użytkownika. Role administratora klasycznej subskrypcji, takie jak "administrator usługi" i "współadministrator", nie są obsługiwane.

> [!NOTE]
> Gdy model uprawnień RBAC platformy Azure jest włączony, wszystkie skrypty, które próbują zaktualizować zasady dostępu, zakończą się niepowodzeniem. Ważne jest, aby zaktualizować te skrypty do korzystania z usługi Azure RBAC.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
-  Przypisanie roli nie działa po kilku minutach — istnieją sytuacje, w których może upłynąć więcej czasu. Ważne jest zapisanie logiki ponawiania w kodzie, aby uwzględnić te przypadki.
- Przypisania ról zniknęły po usunięciu Key Vault (usuwanie nietrwałe) i odzyskanych — obecnie ograniczenie funkcji usuwania nietrwałego w ramach wszystkich usług platformy Azure. Wymagane jest ponowne utworzenie wszystkich przypisań ról po odzyskaniu.    

## <a name="learn-more"></a>Więcej tutaj

- [Przegląd RBAC platformy Azure](../../role-based-access-control/overview.md)
- [Samouczek ról niestandardowych](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)