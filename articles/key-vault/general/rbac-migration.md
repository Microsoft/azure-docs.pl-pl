---
title: Migracja do kontroli dostępu opartej na rolach na platformie Azure | Microsoft Docs
description: Dowiedz się, jak przeprowadzić migrację z zasad dostępu magazynu do ról platformy Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749082"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrowanie z zasad dostępu magazynu do modelu uprawnień kontroli dostępu opartej na rolach platformy Azure

Model zasad dostępu do magazynu to istniejący system autoryzacji wbudowany w Key Vault w celu zapewnienia dostępu do kluczy, wpisów tajnych i certyfikatów. Dostęp można kontrolować, przypisując poszczególne uprawnienia do podmiotu zabezpieczeń (użytkownika, grupy, jednostki usługi, tożsamości zarządzanej) w Key Vault zabezpieczeń. 

Kontrola dostępu oparta na rolach (RBAC) platformy Azure [to](../../azure-resource-manager/management/overview.md) system autoryzacji oparty na Azure Resource Manager, który zapewnia szczegółowe zarządzanie dostępem do zasobów platformy Azure. Dzięki kontroli RBAC platformy Azure możesz kontrolować dostęp do zasobów, tworząc przypisania ról, które składają się z trzech elementów: podmiotu zabezpieczeń, definicji roli (wstępnie zdefiniowanego zestawu uprawnień) i zakresu (grupa zasobów lub pojedynczy zasób). Aby uzyskać więcej informacji, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/overview.md)dostępu oparta na rolach na platformie Azure).

Przed migracją do kontroli RBAC platformy Azure ważne jest, aby zrozumieć jej zalety i ograniczenia.

Kluczowe korzyści związane z dostępem RBAC na platformie Azure do zasad dostępu do magazynu:
- Zapewnia ujednolicony model kontroli dostępu dla zasobów platformy Azure — ten sam interfejs API w usługach platformy Azure
- Scentralizowane zarządzanie dostępem dla administratorów — zarządzanie wszystkimi zasobami platformy Azure w jednym widoku
- Zintegrowana z [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) kontroli dostępu na podstawie czasu
- Odrzucanie przypisań — możliwość wykluczania podmiotu zabezpieczeń w określonym zakresie. Aby uzyskać więcej informacji, zobacz [Understand Azure Deny Assignments (Opis przypisań odmowy platformy Azure)](../../role-based-access-control/deny-assignments.md)

Wady kontroli RBAC platformy Azure:
- Opóźnienie przypisań ról — zastosowanie przypisania roli może potrwać kilka minut. Zasady dostępu do magazynu są przypisywane natychmiast.
- Ograniczona liczba przypisań ról — 2000 przypisań ról na subskrypcję w porównaniu z 1024 zasadami dostępu na Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Mapowanie zasad dostępu do ról platformy Azure

Rola RBAC platformy Azure ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów zabezpieczeń usługi i tożsamości zarządzanych. Jeśli role wbudowane nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role [niestandardowe platformy Azure.](../../role-based-access-control/custom-roles.md)

Key Vault wbudowane role do zarządzania kluczami, certyfikatami i wpisami tajnymi:
- Key Vault administratora
- Key Vault czytelnika
- Key Vault certyfikatów
- Key Vault Crypto Officer
- Key Vault użytkownik kryptograficzny
- Key Vault szyfrowania usługi kryptograficznych
- Key Vault Secrets Officer
- Key Vault użytkownika wpisów tajnych

Aby uzyskać więcej informacji na temat istniejących wbudowanych ról, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md)

Zasady dostępu do magazynu można przypisywać z indywidualnie wybranymi uprawnieniami lub wstępnie zdefiniowanymi szablonami uprawnień.

Wstępnie zdefiniowane szablony uprawnień zasad dostępu:
- Klucz, klucz tajny, zarządzanie certyfikatami
- Zarządzanie kluczami & tajnymi
- Zarządzanie & kluczem tajnym
- Zarządzanie kluczami
- Zarządzanie kluczami tajnymi
- Zarządzanie certyfikatami
- SQL Server Connector
- Azure Data Lake Storage lub Azure Storage
- Azure Backup
- Klucz klienta usługi Exchange Online
- Klucz klienta usługi SharePoint Online
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Mapowanie ról zasad dostępu do ról platformy Azure
| Szablon zasad dostępu | Operacje | Rola na platformie Azure |
| --- | --- | --- |
| Klucz, klucz tajny, zarządzanie certyfikatami | Klucze: wszystkie operacje <br>Certyfikaty: wszystkie operacje<br>Wpisy tajne: wszystkie operacje | Key Vault administratora |
| Zarządzanie kluczami & kluczami tajnymi | Klucze: wszystkie operacje <br>Wpisy tajne: wszystkie operacje| Key Vault Crypto Officer <br> Key Vault Secrets Officer |
| Zarządzanie & kluczem tajnym | Certyfikaty: wszystkie operacje <br>Wpisy tajne: wszystkie operacje| Key Vault certyfikatów <br> Key Vault Secrets Officer|
| Zarządzanie kluczami | Klucze: wszystkie operacje| Key Vault Crypto Officer|
| Zarządzanie kluczami tajnymi | Wpisy tajne: wszystkie operacje| Key Vault Secrets Officer|
| Zarządzanie certyfikatami | Certyfikaty: wszystkie operacje | Key Vault certyfikatów|
| SQL Server Connector | Klucze: get, list, wrap key, unwrap key | Key Vault szyfrowania usługi kryptograficznych|
| Azure Data Lake Storage lub Azure Storage | Klucze: get, list, unwrap key | Nie dotyczy<br> Wymagana rola niestandardowa|
| Azure Backup | Klucze: pobierz, lista, kopia zapasowa<br> Certyfikat: pobierz, lista, kopia zapasowa | Nie dotyczy<br> Wymagana rola niestandardowa|
| Klucz klienta usługi Exchange Online | Klucze: get, list, wrap key, unwrap key | Key Vault szyfrowania usługi kryptograficznych|
| Klucz klienta usługi Exchange Online | Klucze: get, list, wrap key, unwrap key | Key Vault szyfrowania usługi kryptograficznych|
| Azure Information BYOK | Klucze: uzyskiwanie, odszyfrowywanie, podpisywanie | Nie dotyczy<br>Wymagana rola niestandardowa|


## <a name="assignment-scopes-mapping"></a>Mapowanie zakresów przypisania  

Funkcja RBAC platformy Azure Key Vault umożliwia przypisywanie ról w następujących zakresach:
- Grupa zarządzania
- Subskrypcja
- Grupa zasobów
- Key Vault zasobów
- Pojedynczy klucz, klucz tajny i certyfikat

Model uprawnień zasad dostępu do magazynu jest ograniczony do przypisywania zasad Key Vault poziomie zasobu, który 

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest zastosowanie jednego magazynu kluczy na aplikację i zarządzanie dostępem na poziomie magazynu kluczy. Istnieją scenariusze, w których zarządzanie dostępem w innych zakresach może uprościć zarządzanie dostępem.

- **Infrastruktura, administratorzy zabezpieczeń i operatorzy: zarządzanie grupą magazynów kluczy na poziomie grupy zarządzania, subskrypcji lub grupy zasobów przy użyciu zasad dostępu do magazynu wymaga utrzymania zasad dla każdego magazynu kluczy. Funkcja RBAC platformy Azure umożliwia utworzenie jednego przypisania roli w grupie zarządzania, subskrypcji lub grupie zasobów. To przypisanie będzie stosowane do wszystkich nowych magazynów kluczy utworzonych w tym samym zakresie. W tym scenariuszu zaleca się używanie usługi Privileged Identity Management z dostępem just in time w celu zapewnienia trwałego dostępu.
 
- **Aplikacje: istnieją scenariusze, w których aplikacja musi udostępniać klucz tajny innej aplikacji. Aby uniknąć nadawania dostępu do wszystkich wpisów tajnych, trzeba było utworzyć oddzielny magazyn kluczy przy użyciu tych danych. Funkcja RBAC platformy Azure umożliwia przypisywanie roli z zakresem dla poszczególnych kluczy tajnych przy użyciu pojedynczego magazynu kluczy.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Kroki migracji zasad dostępu magazynu do kontroli RBAC platformy Azure
Istnieje wiele różnic między modelem uprawnień RBAC platformy Azure a modelem uprawnień zasad dostępu do magazynu. Aby uniknąć outage podczas migracji, zalecane są poniższe kroki.
 
1. **Identyfikowanie i przypisywanie ról:** identyfikowanie wbudowanych ról na podstawie powyższej tabeli mapowania i tworzenie ról niestandardowych w razie potrzeby. Przypisywanie ról w zakresach na podstawie wskazówek dotyczących mapowania zakresów. Aby uzyskać więcej informacji na temat przypisywania ról do magazynu kluczy, zobacz Zapewnianie dostępu do usługi Key Vault przy użyciu kontroli dostępu [opartej na rolach platformy Azure](rbac-guide.md)
1. **Zweryfikuj** przypisanie ról: propagacja przypisań ról w kontroli RBAC platformy Azure może potrwać kilka minut. Aby uzyskać wskazówki dotyczące sprawdzania przypisań ról, zobacz [Wyświetlanie listy przypisań ról w zakresie](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Konfigurowanie monitorowania i alertów w magazynie kluczy:** ważne jest włączenie rejestrowania i konfigurowania alertów dla wyjątków odmowy dostępu. Aby uzyskać więcej informacji, zobacz [Monitorowanie i alerty dotyczące Azure Key Vault](./alert.md)
1. Ustaw model uprawnień kontroli dostępu opartej na rolach na platformie Azure na **platformie Key Vault:** włączenie modelu uprawnień RBAC platformy Azure spowoduje unieważnienie wszystkich istniejących zasad dostępu. Jeśli wystąpi błąd, model uprawnień można przełączyć z powrotem, aby wszystkie istniejące zasady dostępu pozostały niezmienione.

> [!NOTE]
> Zmiana modelu uprawnień wymaga uprawnienia "Microsoft.Authorization/roleAssignments/write", które jest częścią [ról Właściciel](../../role-based-access-control/built-in-roles.md#owner) i [Administrator dostępu](../../role-based-access-control/built-in-roles.md#user-access-administrator) użytkowników. Role klasycznego administratora subskrypcji, takie jak "Administrator usługi" i "Współ-administrator", nie są obsługiwane.

> [!NOTE]
> Gdy model uprawnień RBAC platformy Azure jest włączony, wszystkie skrypty, które próbują zaktualizować zasady dostępu, nie powiodą się. Ważne jest, aby zaktualizować te skrypty w celu korzystania z kontroli RBAC platformy Azure.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
-  Przypisanie roli nie działa po kilku minutach — istnieją sytuacje, w których przypisania ról mogą trwać dłużej. Ważne jest, aby napisać logikę ponawiania prób w kodzie, aby opisać te przypadki.
- Przypisania ról zniknęły, Key Vault zostały usunięte (usuwanie nieuporządkowane) i odzyskane — obecnie jest to ograniczenie funkcji usuwania nie soft-delete we wszystkich usługach platformy Azure. Po odzyskaniu wymagane jest ponowne utworzenie wszystkich przypisań ról.    

## <a name="learn-more"></a>Więcej tutaj

- [Omówienie kontroli RBAC platformy Azure](../../role-based-access-control/overview.md)
- [Samouczek dotyczący ról niestandardowych](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)