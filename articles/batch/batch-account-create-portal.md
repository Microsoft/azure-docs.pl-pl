---
title: Utwórz konto w Azure Portal
description: Dowiedz się, jak utworzyć konto usługi Azure Batch w portalu Azure w celu równoległego uruchamiania dużych obciążeń w chmurze
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703668"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Tworzenie konta usługi Batch w witrynie Azure Portal

W tym temacie pokazano, jak utworzyć [konto Azure Batch](accounts.md) w [Azure Portal](https://portal.azure.com), wybierając właściwości konta zgodne z Twoim scenariuszem obliczeniowym. Dowiesz się również, gdzie znaleźć ważne właściwości konta, takie jak klucze dostępu i adresy URL konta.

Aby uzyskać ogólne informacje o kontach i scenariuszach usługi Batch, zobacz [przepływ pracy i zasoby usług Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na stronie głównej wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wprowadź ciąg **Usługa Batch**. Wybierz pozycję **Usługa Batch** z wyników, a następnie wybierz pozycję **Utwórz**.

1. Wprowadź następujące szczegóły.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Zrzut ekranu przedstawiający ekran nowe konto w usłudze Batch.":::

    a. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.

    b. **Grupa zasobów**: wybierz istniejącą grupę zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.

    c. **Nazwa konta**: wybrana nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym konto zostanie utworzone (zobacz **Lokalizacja** poniżej). Nazwa konta może zawierać tylko małe litery lub cyfry i musi mieć od 3 do 24 znaków.

    d. **Lokalizacja**: region świadczenia usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.

    e. **Konto magazynu**: opcjonalne [konto magazynu platformy Azure](accounts.md#azure-storage-accounts) skojarzone z kontem usługi Batch. Można wybrać istniejące konto magazynu lub utworzyć nowe. Aby uzyskać najlepszą wydajność, zaleca się użycie konta magazynu ogólnego przeznaczenia w wersji 2.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Zrzut ekranu przedstawiający opcje podczas tworzenia konta magazynu.":::

1. W razie potrzeby wybierz pozycję **Zaawansowane** , aby określić **Typ tożsamości**, **dostęp do sieci publicznej** lub **tryb alokacji puli**. W przypadku większości scenariuszy opcje domyślne są odpowiednie.

1. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz** , aby utworzyć konto.

## <a name="view-batch-account-properties"></a>Wyświetlanie właściwości konta usługi Batch

Po utworzeniu konta wybierz je, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie zapewnia dostęp do wszystkich ustawień i właściwości konta.

> [!NOTE]
> Nazwa konta wsadowego jest jego IDENTYFIKATORem i nie można jej zmienić. Jeśli musisz zmienić nazwę konta w usłudze Batch, musisz usunąć konto i utworzyć nowe z zamierzoną nazwą.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Zrzut ekranu strony konto wsadowe w Azure Portal.":::

aby po utworzeniu aplikacji za pomocą [interfejsów API usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development) uzyskać dostęp do zasobów usługi Batch, potrzebujesz klucza i adresu URL konta. (Zadanie wsadowe obsługuje również uwierzytelnianie Azure Active Directory). Aby wyświetlić informacje o dostępie do konta partii, wybierz pozycję **klucze**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Zrzut ekranu kluczy konta programu Batch w Azure Portal.":::

Aby wyświetlić nazwę i klucze konta magazynu skojarzone z kontem usługi Batch, wybierz pozycję **Konto magazynu**.

Aby wyświetlić [przydziały zasobów](batch-quota-limit.md) , które są stosowane do konta wsadowego, wybierz pozycję **przydziały**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Dodatkowa konfiguracja dla trybu subskrypcji użytkownika

Jeśli wybrano tworzenie konta usługi Batch w trybie subskrypcji użytkownika, należy wykonać następujące dodatkowe kroki przed utworzeniem konta.

> [!IMPORTANT]
> Użytkownik tworzący konto usługi Batch w trybie subskrypcji użytkownika musi mieć przypisaną rolę współautora lub właściciela do subskrypcji, w której zostanie utworzone konto usługi Batch.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Umożliwia usłudze Azure Batch dostęp do subskrypcji (jednorazowa operacja)

Podczas tworzenia pierwszego konta usługi Batch w trybie subskrypcji użytkownika należy zarejestrować subskrypcję w usłudze Batch. (Jeśli zostało to już zrobione, przejdź do następnej sekcji).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie**  >  **subskrypcje** usług, a następnie wybierz subskrypcję, której chcesz użyć dla konta usługi Batch.

1. Na stronie **Subskrypcja** wybierz pozycję **Dostawcy zasobów** i wyszukaj dostawcę **Microsoft.Batch**. Sprawdź, czy dostawca zasobów **Microsoft.Batch** został zarejestrowany w subskrypcji. Jeśli tak nie jest, wybierz link **zarejestruj** w górnej części ekranu.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Zrzut ekranu przedstawiający dostawcę zasobów Microsoft.Batch.":::

1. Wróć do strony **subskrypcja** , a następnie wybierz pozycje **Kontrola dostępu (IAM)**  >  **przydziały ról**  >  **Dodaj**  >  **Dodaj przypisanie roli**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Zrzut ekranu strony przypisań ról dla subskrypcji.":::

1. Na stronie **Dodawanie przypisania roli** wybierz rolę **współautor** lub **właściciela** , a następnie wyszukaj interfejs API usługi Batch. Wyszukaj **Microsoft Azure Batch** lub **MicrosoftAzureBatch** , aby znaleźć interfejs API. (**ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator aplikacji dla interfejsu API usługi Batch).

1. Po odnalezieniu interfejsu API usługi Batch zaznacz jego pozycję i wybierz przycisk **Zapisz**.

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

W trybie subskrypcji użytkownika jest wymagane [Azure Key Vault](../key-vault/general/overview.md) . Key Vault musi znajdować się w tej samej subskrypcji i regionie co konto usługi Batch, które ma zostać utworzone.

1. Na stronie głównej [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.
1. W polu wyszukiwania wprowadź **Key Vault**. Wybierz **Key Vault** z wyników, a następnie wybierz pozycję **Utwórz**.
1. Na stronie **Tworzenie magazynu kluczy** wprowadź nazwę Key Vault i Utwórz nową grupę zasobów w tym samym regionie, w którym ma zostać utworzone konto usługi Batch. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **Utwórz**.

Podczas tworzenia konta usługi Batch w trybie subskrypcji użytkownika Określ **subskrypcję użytkownika** jako tryb alokacji puli, wybierz utworzoną Key Vault i zaznacz pole wyboru, aby udzielić Azure Batch dostępu do Key Vault.

Jeśli wolisz udzielić dostępu do Key Vault ręcznie, przejdź do sekcji **zasady dostępu** w Key Vault i wybierz pozycję **Dodaj zasady dostępu**. Wybierz link obok pozycji **Wybierz podmiot zabezpieczeń** i Wyszukaj **Microsoft Azure Batch** (Identyfikator aplikacji **ddbf3205-c6bd-46ae-8127-60eb93363864**). Wybierz tego podmiotu zabezpieczeń, a następnie skonfiguruj **uprawnienia klucza tajnego** za pomocą menu rozwijanego. Azure Batch musi mieć co najmniej uprawnienia **Get**, **list**, **Set** i **delete** . W przypadku [magazynów kluczy z włączonym](../key-vault/general/soft-delete-overview.md)nietrwałym usuwaniem Azure Batch musi również mieć uprawnienie **odzyskania** .

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Zrzut ekranu opcji uprawnień do wpisów tajnych dla Azure Batch":::

Wybierz pozycję **Dodaj**, a następnie upewnij się, że pola wyboru **Virtual Machines platformy Azure do wdrożenia** i **Azure Resource Manager dla wdrożenia szablonu** są zaznaczone dla połączonego zasobu **Key Vault** . Wybierz pozycję **Zapisz** , aby zatwierdzić zmiany.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Zrzut ekranu przedstawiający ekran zasad dostępu.":::

### <a name="configure-subscription-quotas"></a>Konfigurowanie limitów przydziału subskrypcji

W przypadku kont wsadowych subskrypcji użytkownika należy ręcznie ustawić [przydziały podstawowe](batch-quota-limit.md) . Przydziały podstawowe Zestaw w wersji Standard nie mają zastosowania do kont w trybie subskrypcji użytkownika oraz [przydziałów w ramach subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md) dla regionalnych rdzeni obliczeniowych, rdzeni obliczeniowych na serie i innych zasobów, które są używane i wymuszane.

1. W [witrynie Azure Portal](https://portal.azure.com) wybierz konto usługi Batch trybu subskrypcji użytkownika, aby wyświetlić jego ustawienia i właściwości.
1. Z menu po lewej stronie wybierz pozycję **Limity przydziału**, aby wyświetlić i skonfigurować limity przydziału rdzeni skojarzone z kontem usługi Batch.

## <a name="other-batch-account-management-options"></a>Inne opcje zarządzania kontem usługi Batch

Poza korzystaniem z witryny Azure Portal można tworzyć konta usługi Batch i zarządzać nimi za pomocą narzędzi, takich jak:

- [Polecenia cmdlet programu PowerShell w usłudze Batch](batch-powershell-cmdlets-get-started.md)
- [Interfejs wiersza polecenia platformy Azure](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te Przewodniki Szybki Start przeprowadzą Cię przez przykładową aplikację, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych przy użyciu magazynu platformy Azure na potrzeby przemieszczania i pobierania pliku obciążenia.