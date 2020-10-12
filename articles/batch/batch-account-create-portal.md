---
title: Utwórz konto w Azure Portal
description: Dowiedz się, jak utworzyć konto usługi Azure Batch w portalu Azure w celu równoległego uruchamiania dużych obciążeń w chmurze
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711344"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Tworzenie konta usługi Batch w witrynie Azure Portal

W tym temacie pokazano, jak utworzyć konto Azure Batch w [Azure Portal](https://portal.azure.com), wybierając właściwości konta zgodne z Twoim scenariuszem obliczeniowym. Dowiesz się również, gdzie znaleźć ważne właściwości konta, takie jak klucze dostępu i adresy URL konta.

Aby uzyskać ogólne informacje o kontach i scenariuszach usługi Batch, zobacz [przepływ pracy i zasoby usług Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**, a następnie wybierz pozycję usługi **obliczeniowe** i **Usługa Batch**.

1. Wprowadź ustawienia **Nowe konto usługi Batch**. Zobacz następujące szczegóły.

    ![Tworzenie konta usługi Batch][account_portal]

    a. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.

    b. **Grupa zasobów**: wybierz istniejącą grupę zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.

    c. **Nazwa konta**: wybrana nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym konto zostanie utworzone (zobacz **Lokalizacja** poniżej). Nazwa konta może zawierać tylko małe litery lub cyfry i musi mieć od 3 do 24 znaków.

    d. **Lokalizacja**: region świadczenia usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.

    e. **Konto magazynu**: opcjonalne konto magazynu platformy Azure skojarzone z kontem usługi Batch. Aby uzyskać najlepszą wydajność, zaleca się użycie konta magazynu ogólnego przeznaczenia w wersji 2. Opis wszystkich opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](accounts.md#azure-storage-accounts). W portalu wybierz istniejące konto magazynu lub utwórz nowe.

      ![Tworzenie konta magazynu][storage_account]

    f. **Tryb alokacji puli**: na karcie Ustawienia **Zaawansowane** można określić tryb alokacji puli jako **usługę Batch** lub **subskrypcję użytkownika**. w przypadku większości scenariuszy zaakceptuj wartość domyślną **Usługa Batch**.

      ![Tryb alokacji puli usługi Batch][pool_allocation]

1. Wybierz przycisk **Utwórz**, aby utworzyć konto.

## <a name="view-batch-account-properties"></a>Wyświetlanie właściwości konta usługi Batch

Po utworzeniu konta wybierz je, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie zapewnia dostęp do wszystkich ustawień i właściwości konta.

> [!NOTE]
> Nazwa konta wsadowego jest jego IDENTYFIKATORem i nie można jej zmienić. Jeśli musisz zmienić nazwę konta w usłudze Batch, musisz usunąć konto i utworzyć nowe z zamierzoną nazwą.

![Strona konta usługi Batch w witrynie Azure Portal][account_blade]

* **Nazwa, adres URL i klucze konta usługi Batch**: aby po utworzeniu aplikacji za pomocą [interfejsów API usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development) uzyskać dostęp do zasobów usługi Batch, potrzebujesz adresu URL i klucza konta. (Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory).

    Aby wyświetlić informacje dostępu do konta usługi Batch, wybierz pozycję **Klucze**.

    ![Klucze konta usługi Batch w witrynie Azure Portal][account_keys]

* Aby wyświetlić nazwę i klucze konta magazynu skojarzone z kontem usługi Batch, wybierz pozycję **Konto magazynu**.

* Aby wyświetlić limity przydziału zasobów dotyczące konta usługi Batch, wybierz pozycję **Limity przydziału**. Aby uzyskać szczegółowe informacje, zobacz [Limity przydziału i limity usługi Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Dodatkowa konfiguracja dla trybu subskrypcji użytkownika

Jeśli wybrano tworzenie konta usługi Batch w trybie subskrypcji użytkownika, należy wykonać następujące dodatkowe kroki przed utworzeniem konta.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Umożliwia usłudze Azure Batch dostęp do subskrypcji (jednorazowa operacja)

Podczas tworzenia pierwszego konta usługi Batch w trybie subskrypcji użytkownika należy zarejestrować subskrypcję w usłudze Batch. (Jeśli zostało to już zrobione, przejdź do następnej sekcji).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **subskrypcje**usług, a następnie wybierz subskrypcję, której chcesz użyć dla konta usługi Batch.

1. Na stronie **Subskrypcja** wybierz pozycję **Dostawcy zasobów** i wyszukaj dostawcę **Microsoft.Batch**. Sprawdź, czy dostawca zasobów **Microsoft.Batch** został zarejestrowany w subskrypcji. Jeśli nie, wybierz link **Zarejestruj**.

    ![Rejestrowanie dostawcy Microsoft.Batch][register_provider]

1. Na stronie **Subskrypcja** wybierz pozycję **Kontrola dostępu (IAM)** > **Przypisania ról** > **Dodaj przypisanie roli**.

    ![Kontrola dostępu do subskrypcji][subscription_access]

1. Na stronie **Dodawanie przypisania roli** wybierz rolę **współautor** lub **właściciela** , a następnie wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. W przypadku nowszych dzierżaw usługi Azure AD może być używana ta nazwa.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch.

1. Po odnalezieniu interfejsu API usługi Batch zaznacz jego pozycję i wybierz przycisk **Zapisz**.

    ![Dodawanie uprawnień usługi Batch][add_permission]

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

W trybie subskrypcji użytkownika jest wymagane [Azure Key Vault](../key-vault/general/overview.md) . Key Vault musi znajdować się w tej samej subskrypcji i regionie co konto usługi Batch, które ma zostać utworzone. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Nowy** > **Bezpieczeństwo** > **Key Vault**.

1. Na stronie **tworzenie Key Vault** wprowadź nazwę Key Vault i Utwórz grupę zasobów w wybranym regionie konta usługi Batch. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **Utwórz**.

Podczas tworzenia konta usługi Batch w trybie subskrypcji użytkownika należy określić **subskrypcję użytkownika** jako tryb alokacji puli, zaznaczyć Key Vault i zaznaczyć pole wyboru, aby udzielić Azure Batch dostępu do Key Vault.

Jeśli wolisz udzielić dostępu do Key Vault ręcznie, przejdź do sekcji **zasady dostępu** w obszarze Key Vault, wybierz pozycję **Dodaj zasady dostępu** i Wyszukaj **Microsoft Azure Batch**. Po wybraniu tej opcji należy skonfigurować **uprawnienia klucza tajnego** za pomocą menu rozwijanego. Azure Batch musi mieć co najmniej uprawnienia **Get**, **list**, **Set**i **delete** .

![Uprawnienia klucza tajnego dla Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Upewnij się, że pola wyboru **Virtual Machines platformy Azure do wdrożenia** i **Azure Resource Manager dla wdrożenia szablonu** są zaznaczone w obszarze **zasady dostępu** dla połączonego zasobu **Key Vault** .
>
> ![Obowiązkowe zasady dostępu Key Vault](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Konfigurowanie limitów przydziału subskrypcji

W przypadku kont wsadowych subskrypcji użytkownika należy ręcznie ustawić przydziały podstawowe. Przydziały podstawowe Zestaw w wersji Standard nie mają zastosowania do kont w trybie subskrypcji użytkownika.

1. W [witrynie Azure Portal](https://portal.azure.com) wybierz konto usługi Batch trybu subskrypcji użytkownika, aby wyświetlić jego ustawienia i właściwości.
1. Z menu po lewej stronie wybierz pozycję **Limity przydziału**, aby wyświetlić i skonfigurować limity przydziału rdzeni skojarzone z kontem usługi Batch.

Aby uzyskać więcej informacji na temat przydziałów podstawowych trybu subskrypcji użytkownika, zobacz [limity przydziału i ograniczenia usługi Batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Inne opcje zarządzania kontem usługi Batch

Poza korzystaniem z witryny Azure Portal można tworzyć konta usługi Batch i zarządzać nimi za pomocą narzędzi, takich jak:

* [Polecenia cmdlet programu PowerShell w usłudze Batch](batch-powershell-cmdlets-get-started.md)
* [Interfejs wiersza polecenia platformy Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te przewodniki Szybki start zawierają omówienie przykładowej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
