---
title: Tworzenie konta usługi Azure Remote Rendering
description: Zawiera opis czynności, które należy wykonać, aby utworzyć konto do zdalnego renderowania na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 8169b277dfae918e86ac493259325ff84d0b6a4e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998536"
---
# <a name="create-an-azure-remote-rendering-account"></a>Tworzenie konta usługi Azure Remote Rendering

Ten rozdział przeprowadzi Cię przez kroki tworzenia konta dla usługi **zdalnego renderowania na platformie Azure** . Prawidłowe konto jest wymagane do ukończenia dowolnego przewodnika Szybki start lub samouczków.

## <a name="create-an-account"></a>Tworzenie konta

Następujące kroki są wymagane do utworzenia konta dla usługi zdalnego renderowania na platformie Azure:

1. Przejdź do [strony Podgląd rzeczywistości mieszanej](https://aka.ms/MixedRealityPrivatePreview)
1. Kliknij przycisk Utwórz zasób.
1. W polu wyszukiwania ("Wyszukaj witrynę Marketplace") wpisz ciąg "renderowanie zdalne" i naciśnij klawisz ENTER.
1. Na liście wynik kliknij kafelek "zdalne renderowanie"
1. Na następnym ekranie kliknij przycisk "Utwórz". Zostanie otwarty formularz umożliwiający utworzenie nowego konta renderowania zdalnego:
    1. Ustaw wartość "nazwa zasobu" na nazwę konta
    1. W razie konieczności zaktualizuj element "Subscription"
    1. Ustaw grupę zasobów na wybraną grupę zasobów
    1. Wybierz region z listy rozwijanej "lokalizacja", w której ma zostać utworzony ten zasób. Zobacz uwagi dotyczące [regionów kont](create-an-account.md#account-regions) poniżej.
1. Po utworzeniu konta przejdź do niego i:
    1. Na karcie *Przegląd* Zanotuj wartość "Identyfikator konta"
    1. Na karcie *ustawienia > klucze dostępu* Zanotuj klucz podstawowy. to jest klucz konta tajnego konta.

### <a name="account-regions"></a>Regiony konta
Lokalizacja określona podczas tworzenia konta konta określa region, do którego jest przypisany zasób konta. Nie można go zmienić po utworzeniu. Konto może być jednak używane do nawiązywania połączenia z sesją renderowania zdalnego w dowolnym [obsługiwanym regionie](./../reference/regions.md), niezależnie od lokalizacji konta.

### <a name="retrieve-the-account-information"></a>Pobierz informacje o koncie

Przykłady i samouczki wymagają podania identyfikatora konta i klucza. Na przykład w **arrconfig.js** pliku używanym dla przykładowych skryptów programu PowerShell:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Zapoznaj się z [listą dostępnych regionów](../reference/regions.md) w celu wypełniania opcji *region* .

Wartości dla **`arrAccountId`** i **`arrAccountKey`** można znaleźć w portalu, zgodnie z opisem w następujących krokach:

* Przejdź do witryny [Azure Portal](https://www.portal.azure.com).
* Znajdź **"zdalne konto renderowania"** — powinno ono znajdować się na liście **"ostatnie zasoby"** . Możesz również wyszukać ją na pasku wyszukiwania u góry. W takim przypadku upewnij się, że subskrypcja, której chcesz użyć, została wybrana w domyślnym filtrze subskrypcji (ikona filtru obok paska wyszukiwania):

![Filtr subskrypcji](./media/azure-subscription-filter.png)

Kliknięcie konta spowoduje przejście do tego ekranu, który będzie od razu widoczny dla  **identyfikatora konta** :

![Identyfikator konta platformy Azure](./media/azure-account-id.png)

W polu klucz wybierz pozycję **klucze dostępu** w panelu po lewej stronie. Na następnej stronie jest wyświetlany klucz podstawowy i pomocniczy:

![Klucze dostępu platformy Azure](./media/azure-account-primary-key.png)

Wartość dla **`arrAccountKey`** może być kluczem podstawowym lub pomocniczym.

## <a name="link-storage-accounts"></a>Łączenie kont magazynu

W tym akapicie wyjaśniono, jak połączyć konta magazynu z kontem renderowania zdalnego. Po połączeniu konta magazynu nie jest konieczne wygenerowanie identyfikatora URI SAS za każdym razem, gdy chcesz pracować z danymi na koncie, na przykład podczas ładowania modelu. Zamiast tego można użyć nazw kont magazynu bezpośrednio zgodnie z opisem w [sekcji Ładowanie modelu](../concepts/models.md#loading-models).

Kroki opisane w tym ustępie należy wykonać dla każdego konta magazynu, które ma używać tej alternatywnej metody dostępu. Jeśli jeszcze nie utworzono kont magazynu, możesz zapoznać się z odpowiednim krokiem w sekcji [konwertowanie modelu na potrzeby renderowania przewodnika Szybki Start](../quickstarts/convert-model.md#storage-account-creation).

Teraz zakładasz, że masz konto magazynu. Przejdź do konta magazynu w portalu i przejdź do karty **Access Control (IAM)** dla tego konta magazynu:

![Konto magazynu w usłudze IAM](./media/azure-storage-account.png)

 Upewnij się, że masz uprawnienia właściciela na tym koncie magazynu, aby upewnić się, że można dodać przypisania ról. Jeśli nie masz dostępu, opcja **Dodaj przypisanie roli** zostanie wyłączona.

 Należy dodać trzy odrębne role, zgodnie z opisem w następnym kroku. Jeśli nie podasz wszystkich trzech poziomów dostępu, podczas próby uzyskania dostępu do konta magazynu będą występować problemy z uprawnieniami.

 Kliknij przycisk **Dodaj** na kafelku "Dodawanie przypisania roli", aby dodać pierwszą rolę:

![Konto magazynu IAM Dodawanie przypisania roli](./media/azure-add-role-assignment.png)

* Pierwszą rolą do przypisania jest **właściciel** , jak pokazano na poniższym zrzucie ekranu.
* Wybierz opcję **konto renderowania zdalnego** z listy rozwijanej **Przypisz dostęp do** .
* Wybierz swoje subskrypcję i konto renderowania zdalnego z ostatnich list rozwijanych.

> [!WARNING]
> Jeśli konto renderowania zdalnego nie znajduje się na liście, zapoznaj się z [sekcją rozwiązywania problemów](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

Powtórz Dodaj nowe role dwa razy dla odpowiednich opcji z listy rozwijanej **rola** :

* **Współautor konta magazynu**
* **Współautor danych obiektu blob usługi Storage**

Inne listy rozwijane są wybierane jako pierwszy krok.

Jeśli dodano wszystkie trzy role, konto renderowania zdalnego platformy Azure ma dostęp do konta magazynu przy użyciu tożsamości usługi zarządzanej przypisanej przez system.
> [!IMPORTANT]
> Przypisania ról platformy Azure są przechowywane w pamięci podręcznej przez usługę Azure Storage, co może wiązać się z opóźnieniem do 30 minut między przypisaniem zdalnego konta renderowania i użyciem go w celu uzyskania dostępu do konta magazynu. Szczegółowe informacje znajdują się w [dokumentacji kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) .

## <a name="next-steps"></a>Następne kroki

* [Authentication](authentication.md)
* [Używanie interfejsów API frontonu platformy Azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)