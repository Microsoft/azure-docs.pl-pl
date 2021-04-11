---
title: Interfejsy API i narzędzia dla deweloperów
description: Informacje na temat narzędzi i interfejsów API przeznaczonych do tworzenia rozwiązań korzystających z usługi Azure Batch.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: b7c68ab16834bbd746cf52708db0cdb3f31c8d4d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219930"
---
# <a name="overview-of-batch-apis-and-tools"></a>Omówienie narzędzi i interfejsów API usługi Batch

Przetwarzanie obciążeń równoległych przy użyciu usługi Azure Batch jest zazwyczaj wykonywane programowo przy użyciu jednego z interfejsów API usługi Batch. Aplikacja lub usługa kliencka, której autorem jest użytkownik, może używać interfejsów API usługi Batch do komunikacji z usługą Batch. Za pomocą interfejsów API usługi Batch można tworzyć pule węzłów obliczeniowych (maszyny wirtualne lub usługi w chmurze) i zarządzać nimi. Następnie można zaplanować wykonywanie zadań i podzadań w ramach tych węzłów.

Można skutecznie przetwarzać duże obciążenia na potrzeby swojej organizacji lub zapewniać swoim klientom fronton usług, aby umożliwić im uruchamianie zadań i podzadań — na żądanie lub zgodnie z harmonogramem — w jednym węźle albo setkach lub nawet tysiącach węzłów. Usługę Azure Batch można także używać w ramach większego przepływu pracy zarządzanego za pomocą takich narzędzi, jak usługa [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Aby dowiedzieć się więcej o funkcjach i przepływie pracy używanych w Azure Batch, zobacz [przepływ pracy usługi Batch i zasoby](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Konta platformy Azure na potrzeby programowania w usłudze Batch

Podczas opracowywania rozwiązań usługi Batch potrzebne są następujące konta w ramach subskrypcji platformy Azure:

- **Konto usługi Batch** — zasoby Azure Batch, w tym pule, węzły obliczeniowe, zadania i zadania, są skojarzone z [kontem Azure Batch](accounts.md). Gdy aplikacja wykonuje żądanie względem usługi Batch, usługa ta uwierzytelnia żądanie przy użyciu nazwy konta usługi Batch, adresu URL konta oraz klucza dostępu albo tokenu usługi Azure Active Directory. Konto w usłudze [Batch można utworzyć](batch-account-create-portal.md) w Azure Portal lub programowo.
- **Konto usługi Storage** — Usługa Batch obejmuje wbudowaną obsługę pracy z plikami w usłudze [Azure Storage](../storage/index.yml). Usługa Azure Blob Storage jest używana prawie w każdym scenariuszu usługi Batch — do przemieszczania programów uruchamianych przez podzadania oraz danych, które one przetwarzają, oraz do przechowywania danych wyjściowych, które generują. Każde konto wsadowe jest zwykle skojarzone z odpowiednim kontem magazynu.

## <a name="service-level-and-management-level-apis"></a>Interfejsy API poziomu usług i zarządzania

Azure Batch ma dwa zestawy interfejsów API, jeden dla poziomu usługi i jeden dla poziomu zarządzania. Nazewnictwo jest często podobne, ale zwracają różne wyniki.

W dzienniku aktywności są śledzone tylko akcje z interfejsów API zarządzania. Interfejsy API poziomu usługi pomijają warstwę zarządzania zasobami platformy Azure (management.azure.com) i nie są rejestrowane.

Na przykład [interfejs API usługi Batch służący do usuwania puli](/rest/api/batchservice/pool/delete) jest przeznaczony bezpośrednio na koncie usługi Batch: `DELETE {batchUrl}/pools/{poolId}`

Natomiast [interfejs API zarządzania usługą Batch do usuwania puli](/rest/api/batchmanagement/pool/delete)  jest przeznaczony dla warstwy Management.Azure.com: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Interfejsy API usługi Batch

Aplikacje i usługi mogą wykonywać bezpośrednie wywołania interfejsu API REST lub użyć co najmniej jednej z następujących bibliotek klienckich do uruchamiania obciążeń usługi Azure Batch i zarządzania nimi.

| Interfejs API | Dokumentacja interfejsu API | Pobierz | Samouczek | Przykłady kodu | Więcej informacji |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Interfejs API REST platformy Azure — dokumentacja](/rest/api/batchservice/) |Nie dotyczy |- |- | [Obsługiwane wersje](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Zestaw Azure SDK dla platformy .NET — dokumentacja](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Samouczek](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Informacje o wersji](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Zestaw Azure SDK dla języka Python — dokumentacja](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Samouczek](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Plik Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch JavaScript** |[Zestaw Azure SDK dla języka JavaScript — dokumentacja](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Samouczek](batch-js-get-started.md) |- | [Plik Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Zestaw Azure SDK dla języka Java — dokumentacja](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Plik Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Interfejsy API usługi Batch Management

Interfejsy API usługi Azure Resource Manager dla usługi Batch zapewniają dostęp programowy do kont usługi Batch. Za pomocą tych interfejsów API możesz programowo zarządzać kontami usługi Batch, limitami przydziału, pakietami aplikacji i innymi zasobami za pośrednictwem dostawcy Microsoft.Batch.  

| Interfejs API | Dokumentacja interfejsu API | Pobierz | Samouczek | Przykłady kodu |
| --- | --- | --- | --- | --- |
| **Batch Management REST (Interfejs REST zarządzania usługą Batch)** |[Interfejs API REST platformy Azure — dokumentacja](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[Zestaw Azure SDK dla platformy .NET — dokumentacja](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Samouczek](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Zarządzanie usługą Batch za pomocą języka Python** |[Zestaw Azure SDK dla języka Python — dokumentacja](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Zarządzanie partiami JavaScript** |[Zestaw Azure SDK dla języka JavaScript — dokumentacja](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Zarządzanie usługą Batch za pomocą języka Java** |[Zestaw Azure SDK dla języka Java — dokumentacja](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Narzędzia wiersza polecenia usługi Batch

Te narzędzia wiersza polecenia zawierają te same funkcje co interfejsy API usług Batch i Batch Management: 

- [Polecenia cmdlet programu PowerShell w usłudze Batch](/powershell/module/az.batch/): polecenia cmdlet usługi Azure Batch w module programu [Azure PowerShell](/powershell/azure/) umożliwiają zarządzanie zasobami usługi Batch za pomocą programu PowerShell.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure): interfejs wiersza polecenia platformy Azure jest działającym na wielu platformach zestawem narzędzi, który udostępnia polecenia powłoki do interakcji z wieloma usługami platformy Azure, w tym usługi Batch i usługi Batch Management. Zobacz [Zarządzanie zasobami usługi Batch przy użyciu interfejsu wiersza polecenia platformy Azure](batch-cli-get-started.md), aby uzyskać więcej informacji o korzystaniu z interfejsu wiersza polecenia platformy Azure z usługą Batch.

## <a name="other-tools-for-application-development"></a>Inne narzędzia do opracowywania aplikacji

Te dodatkowe narzędzia mogą być przydatne do kompilowania i debugowania aplikacji i usług wsadowych.

- [Witryna Azure Portal](https://portal.azure.com/): możesz tworzyć, monitorować i usuwać pule oraz zadania usługi Batch w witrynie Azure Portal. Można wyświetlić informacje o stanie dla tych i innych zasobów podczas uruchamiania zadań, a nawet pobierać pliki z węzłów obliczeniowych w pulach. Na przykład podczas rozwiązywania problemów można pobrać plik `stderr.txt` zadania zakończonego niepowodzeniem. Można również pobrać pliki Remote Desktop (RDP) umożliwiające logowanie się do węzłów obliczeniowych.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer (dawniej BatchLabs) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows.
- [Azure Batch stocznie](https://github.com/Azure/batch-shipyard): stoczniowe narzędzie do obsługi inicjowania, wykonywania i monitorowania przetwarzania wsadowego opartego na kontenerach i obciążeń HPC na Azure Batch.
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/): Chociaż nie jest to dokładnie narzędzie Azure Batch, Eksplorator usługi Storage jest innym przydatnym narzędziem, które trzeba uzyskać podczas tworzenia i debugowania rozwiązań wsadowych.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby dowiedzieć się więcej o zdarzeniach rejestrowania z aplikacji usługi Batch, zobacz [metryki usługi Batch, alerty i dzienniki na potrzeby oceny i monitorowania diagnostyki](batch-diagnostics.md).
- Informacje referencyjne dotyczące zdarzeń zgłaszanych przez usługę Batch można znaleźć w temacie [Batch Analytics](batch-analytics.md).
- Aby uzyskać informacje o zmiennych środowiskowych dla węzłów obliczeniowych, zobacz [zmienne środowiskowe środowiska uruchomieniowego Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Przeczytaj artykuł [Get started with the Azure Batch library for .NET](tutorial-parallel-dotnet.md) (Wprowadzenie do biblioteki usługi Azure Batch dla platformy .NET), aby dowiedzieć się, jak używać języka C# i biblioteki usługi Batch dla środowiska .NET w celu wykonania prostego obciążenia przy użyciu popularnego przepływu pracy usługi Batch. Dostępna jest również [wersja języka Python](tutorial-parallel-python.md) i [samouczek JavaScript](batch-js-get-started.md) .
- Pobierz [przykłady kodu w serwisie GitHub](https://github.com/Azure-Samples/azure-batch-samples), aby zobaczyć, jak kod C# i Python może współpracować z usługą Batch w celu planowania i przetwarzania przykładowych obciążeń.
