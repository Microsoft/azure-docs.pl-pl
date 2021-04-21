---
title: Wprowadzenie do interfejsu wiersza polecenia platformy Azure dla usługi Batch
description: Skorzystaj z szybkiego wprowadzenia do poleceń usługi Batch w interfejsie wiersza polecenia platformy Azure, aby zarządzać zasobami usługi Azure Batch
ms.topic: how-to
ms.date: 07/24/2018
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: befe2f6fe67ce7c2c4bb61153344103ee2aa70cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768437"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Zarządzanie zasobami usługi Batch przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można go używać w systemach macOS, Linux i Windows. Interfejs wiersza polecenia platformy Azure jest zoptymalizowany do zarządzania i administrowania zasobami platformy Azure z wiersza polecenia. Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie kontami usługi Azure Batch i zarządzanie zasobami, na przykład pulami, zadaniami i zadaniami podrzędnymi. Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć skrypty dla wielu tych samych zadań, które wykonuje się za pomocą interfejsów API usługi Batch, witryny Azure Portal oraz poleceń cmdlet programu PowerShell dla usługi Batch.

Ten artykuł zawiera omówienie korzystania z [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) z usługą Batch. Aby zapoznać się z omówieniem korzystania z interfejsu wiersza polecenia na platformie Azure, zobacz [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

## <a name="set-up-the-azure-cli"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure

Możesz uruchomić najnowszy interfejs wiersza polecenia platformy Azure w usłudze [Azure Cloud Shell](../cloud-shell/overview.md). Aby zainstalować interfejs wiersza polecenia platformy Azure lokalnie, wykonaj czynności opisane w artykule [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

> [!TIP]
> Zaleca się częstą aktualizację interfejsu wiersza polecenia platformy Azure, aby mieć możliwość korzystania z aktualizacji i rozszerzeń usługi.
> 
> 

## <a name="command-help"></a>Pomoc związana z poleceniami

Możesz wyświetlić tekst pomocy dla każdego polecenia w interfejsie wiersza polecenia platformy Azure, dodając do polecenia opcję `-h`. Pomiń wszelkie inne opcje. Na przykład:

* Aby uzyskać pomoc dotyczącą polecenia `az`, wprowadź: `az -h`
* Aby uzyskać listę wszystkich poleceń usługi Batch w interfejsie wiersza polecenia, użyj: `az batch -h`
* Aby uzyskać pomoc związaną z tworzeniem konta usługi Batch, wprowadź: `az batch account create -h`

W razie wątpliwości użyj opcji wiersza polecenia `-h`, aby uzyskać pomoc dotyczącą jakiegokolwiek polecenia interfejsu wiersza polecenia platformy Azure.



Ponadto w dokumentacji wiersza polecenia platformy Azure można znaleźć szczegółowe informacje o [poleceniach wiersza polecenia platformy Azure dla usługi Batch](/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Logowanie i uwierzytelnianie

Aby używać interfejsu wiersza polecenia platformy Azure z usługą Batch, należy się zalogować i uwierzytelnić. Należy w tym celu wykonać dwa proste kroki:

1. **Zaloguj się na platformie Azure.** Zalogowanie się na platformie Azure zapewnia dostęp do poleceń usługi Azure Resource Manager, w tym do poleceń [usługi Batch Management](batch-management-dotnet.md).  
2. **Zaloguj się na koncie usługi Batch.** Zalogowanie się na koncie usługi Batch zapewnia dostęp do poleceń usługi Batch.   

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Istnieje kilka różnych sposobów logowania się na platformie Azure. Opisano je szczegółowo w artykule [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli):

1. [Logowanie interakcyjne](/cli/azure/authenticate-azure-cli). Zaloguj się interakcyjnie, gdy samodzielnie uruchamiasz polecenia interfejsu wiersza polecenia platformy Azure w wierszu polecenia.
2. [Zaloguj się przy użyciu jednostki usługi](/cli/azure/authenticate-azure-cli). Zaloguj się za pomocą jednostki usługi, gdy uruchamiasz polecenia interfejsu wiersza polecenia platformy Azure za pomocą skryptu lub aplikacji.

Na potrzeby tego artykułu wyjaśnimy, jak zalogować się interakcyjnie. W wierszu polecenia wpisz [az login](/cli/azure/reference-index#az_login):

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Polecenie `az login` zwraca token używany do uwierzytelniania, jak to tutaj pokazano. Postępuj zgodnie z instrukcjami, aby otworzyć stronę internetową i przesłać token do platformy Azure:

![Zaloguj się do platformy Azure.](./media/batch-cli-get-started/az-login.png)

W przykładach wymienionych w sekcji „Przykładowe skrypty powłoki” również pokazano, jak uruchomić sesję interfejsu wiersza polecenia platformy Azure przez zalogowanie się interakcyjne na platformie Azure. Po zalogowaniu się można wywoływać polecenia do pracy z zasobami usługi Batch Management, w tym z kluczami, pakietami aplikacji, przydziałami i kontami usługi Batch.  

### <a name="log-in-to-your-batch-account"></a>Logowanie się na koncie usługi Batch

Aby za pomocą interfejsu wiersza polecenia platformy Azure zarządzać zasobami usługi Batch, na przykład pulami, zadaniami i zadaniami podrzędnymi, należy zalogować się na koncie usługi Batch i dokonać uwierzytelnienia. Aby zalogować się do usługi Batch, użyj polecenia [az batch account login](/cli/azure/batch/account#az_batch_account_login). 

Dostępne są dwie opcje uwierzytelnienia na koncie usługi Batch:

- **Przy użyciu uwierzytelniania usługi Azure Active Directory (Azure AD)** 

    Uwierzytelnianie przy użyciu usługi Azure AD jest ustawieniem domyślnym w przypadku używania interfejsu wiersza polecenia platformy Azure z usługą Batch i jest zalecane w większości przypadków. 
    
    W razie logowania się interakcyjnego na platformie Azure, zgodnie z opisem w poprzedniej sekcji, poświadczenia są buforowane, dzięki czemu interfejs wiersza polecenia platformy Azure może zalogować użytkownika na koncie usługi Batch za ich pomocą. W razie logowania się na platformie Azure za pomocą jednostki usługi te poświadczenia także są używane do zalogowania się na koncie usługi Batch.

    Zaletą usługi Azure AD jest to, że oferuje ona kontrolę dostępu opartą na rolach (RBAC) platformy Azure. Dzięki kontroli dostępu na podstawie ról platformy Azure dostęp użytkownika zależy od przypisanej mu roli, a nie od tego, czy ma on klucze kont. Zamiast zarządzać kluczami kont, można zarządzać rolami platformy Azure i pozwolić usłudze Azure AD na obsługę dostępu i uwierzytelniania.  

     Aby zalogować się na koncie usługi Batch za pomocą usługi Azure AD, wywołaj polecenie [az batch account login](/cli/azure/batch/account#az_batch_account_login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Przy użyciu uwierzytelniania za pomocą klucza wspólnego**

    [Uwierzytelnianie klucza wspólnego](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) polega na uwierzytelnianiu poleceń interfejsu wiersza polecenia platformy Azure dla usługi Batch za pomocą kluczy dostępu konta.

    W przypadku tworzenia skryptów interfejsu wiersza polecenia platformy Azure w celu zautomatyzowania wywoływania poleceń usługi Batch można użyć uwierzytelniania klucza wspólnego lub jednostki usługi w usłudze Azure AD. W niektórych przypadkach użycie uwierzytelniania klucza wspólnego może być łatwiejsze niż tworzenie jednostki usługi.  

    Aby zalogować się przy użyciu uwierzytelniania klucza wspólnego, dodaj w wierszu polecenia opcję `--shared-key-auth`:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

W przykładach wymienionych w sekcji „Przykładowe skrypty powłoki” pokazano, jak zalogować się na koncie usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu zarówno usługi Azure AD, jak i klucza wspólnego.

## <a name="use-azure-batch-cli-extension-commands"></a>Używanie poleceń rozszerzenia interfejsu wiersza polecenia usługi Azure Batch

Po zainstalowaniu rozszerzenia interfejsu wiersza polecenia usługi Azure Batch możesz używać interfejsu wiersza polecenia platformy Azure do kompleksowej obsługi zadań usługi Batch bez konieczności pisania kodu. Polecenia usługi Batch obsługiwane przez to rozszerzenie umożliwiają korzystanie z szablonów JSON do tworzenia pul i zadań za pomocą wiersza polecenia platformy Azure. Polecenia rozszerzenia interfejsu wiersza polecenia umożliwiają także przekazywanie plików wejściowych zadania do konta usługi Azure Storage skojarzonego z kontem usługi Batch oraz pobieranie z niego plików wyjściowych zadania. Więcej informacji można znaleźć w temacie [Use Azure Batch CLI Templates and File Transfer (Korzystanie z szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików)](batch-cli-templates.md).

## <a name="script-examples"></a>Przykłady skryptów

Zobacz [przykłady skryptów interfejsów wiersza polecenia](./scripts/batch-cli-sample-create-account.md) dla usługi Batch, aby wykonać typowe zadania. Te przykłady obejmują wiele poleceń dostępnych w interfejsie wiersza polecenia platformy Azure dla usługi Batch do tworzenia kont, pul, zadań i zadań podrzędnych oraz zarządzania nimi.

## <a name="json-files-for-resource-creation"></a>Pliki JSON do tworzenia zasobów

Podczas tworzenia zasobów usługi Batch, np. puli i zadań, możesz określić plik JSON zawierający konfiguracje nowego zasobu, zamiast przekazywać parametry zasobu w opcjach wiersza polecenia. Na przykład:

```azurecli
az batch pool create my_batch_pool.json
```

Możesz wykonać większość operacji tworzenia zasobów usługi Batch, korzystając wyłącznie z opcji wiersza polecenia, niemniej niektóre funkcje wymagają określenia pliku w formacie JSON zawierającego szczegółowe informacje o zasobie. Przykładowo musisz użyć pliku JSON, jeśli chcesz określić pliki zasobu dla zadania rozpoczęcia.

Aby zapoznać się ze składnią pliku JSON wymaganego do utworzenia zasobu, skorzystaj z dokumentacji [interfejsu API REST usługi Batch][rest_api]. Każdy temat „Dodawanie *typu zasobu*” w dokumentacji interfejsu API REST zawiera przykładowe skrypty JSON do tworzenia zasobu. Tych przykładowych skryptów JSON można używać jako szablonów dla plików JSON do użytku z interfejsem wiersza polecenia platformy Azure. Aby na przykład zapoznać się ze składnią pliku JSON do tworzenia puli, zobacz [Dodawanie puli do konta][rest_add_pool].

Aby uzyskać przykładowy skrypt określający plik JSON, zobacz [Uruchamianie zadań i zadań podrzędnych za pomocą usługi Batch](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Jeśli określisz plik JSON podczas tworzenia zasobu, wszystkie inne parametry określone dla tego zasobu w wierszu polecenia zostaną zignorowane.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Wydajne zapytania dotyczące zasobów usługi Batch

Każdy typ zasobu usługi Batch obsługuje polecenie `list`, które wysyła zapytania do konta usługi Batch i wyświetla listę zasobów tego typu. Przykładowo możesz wyświetlić listę puli na koncie i zadań w ramach zadania:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Podczas wysyłania zapytania do usługi Batch z operacją `list` można określić klauzulę OData, aby ograniczyć ilość zwracanych danych. Ponieważ całe filtrowanie odbywa się po stronie serwera, tylko dane, których zażądasz, zostają przekazane podczas transmisji. Użyj tych klauzul, aby oszczędzić przepustowość (i czas) podczas wykonywania operacji związanych z wyświetlaniem listy.

W poniższej tabeli opisano klauzule OData obsługiwane przez usługę Batch:

| Klauzula | Opis |
|---|---|
| `--select-clause [select-clause]` | Zwracanie podzbioru właściwości dla każdej jednostki. |
| `--filter-clause [filter-clause]` | Zwracanie tylko jednostek, które pasują do określonego wyrażenia OData. |
| `--expand-clause [expand-clause]` | Uzyskiwanie informacji dotyczących jednostki w pojedynczym, podstawowym wywołaniu REST. Klauzula expand obsługuje obecnie tylko właściwość `stats`. |

Aby uzyskać przykładowy skrypt ilustrujący używanie klauzuli OData, zobacz [Uruchamianie zadań i zadań podrzędnych za pomocą usługi Batch](./scripts/batch-cli-sample-run-job.md).

Aby uzyskać więcej informacji o wykonywaniu wydajnych zapytań listy przy użyciu klauzul OData, zobacz [Efektywne wysyłanie zapytań do usługi Azure Batch](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższe porady mogą być pomocne w przypadku rozwiązywania problemów związanych z interfejsem wiersza polecenia platformy Azure:

* Użyj polecenia `-h`, aby uzyskać **tekst pomocy** dla dowolnego polecenia interfejsu wiersza polecenia
* Użyj opcji `-v` i `-vv`, aby wyświetlić **pełne** dane wyjściowe polecenia. Gdy jest dołączona flaga `-vv`, w interfejsie wiersza polecenia platformy Azure wyświetlane są faktyczne żądania i odpowiedzi REST. Te przełączniki są przydatne do wyświetlania pełnych danych wyjściowych błędu.
* Możesz wyświetlić **dane wyjściowe polecenia w formie pliku JSON** przy użyciu opcji `--json`. Przykładowo polecenie `az batch pool show pool001 --json` wyświetla właściwości puli 001 w formacie JSON. Następnie możesz skopiować i zmodyfikować te dane wyjściowe, aby użyć ich w pliku `--json-file` (zobacz sekcję „Pliki JSON” wcześniej w tym artykule).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Następne kroki

* Zobacz dokumentację [interfejsu wiersza polecenia platformy Azure.](/cli/azure)
* Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych,](batch-service-workflow-features.md) takich jak pule, węzły, zadania i zadania podrzędne.
* Dowiedz się więcej o używaniu szablonów usługi Batch do tworzenia pul, zadań i zadań bez pisania kodu, Azure Batch szablonów interfejsu wiersza polecenia [i transferu plików.](batch-cli-templates.md)

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: /rest/api/batchservice/
[rest_add_pool]: /rest/api/batchservice/pool/add
