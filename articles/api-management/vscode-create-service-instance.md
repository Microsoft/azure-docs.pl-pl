---
title: Tworzenie wystąpienia usługi Azure API Management przy użyciu Visual Studio Code | Microsoft Docs
description: Visual Studio Code utworzyć wystąpienia usługi Azure API Management.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 19080679291b88b693c95bd71f8ddc0e59286356
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90057425"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu Visual Studio Code

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. Usługa APIM pozwala tworzyć nowoczesne bramy interfejsów API dla istniejących usług zaplecza hostowanych w dowolnym miejscu oraz zarządzać tymi bramami. Aby uzyskać więcej informacji, zobacz temat [Omówienie](api-management-key-concepts.md).

W tym przewodniku szybki start opisano procedurę tworzenia nowego wystąpienia API Management przy użyciu *usługi Azure API Management Extension w wersji Zapoznawczej* Visual Studio Code. Można również użyć rozszerzenia do wykonywania typowych operacji zarządzania w wystąpieniu API Management.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ponadto upewnij się, że zainstalowano następujące elementy:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Rozszerzenie API Management platformy Azure dla Visual Studio Code (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Uruchom Visual Studio Code i Otwórz rozszerzenie platformy Azure. (Jeśli na pasku działania nie widzisz ikony platformy Azure, upewnij się, że rozszerzenie *API Management platformy Azure* jest włączone.)

Wybierz pozycję **Zaloguj się do platformy Azure...** , aby uruchomić okno przeglądarki i zalogować się do konto Microsoft.

![Zaloguj się do platformy Azure przy użyciu rozszerzenia API Management dla VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Tworzenie usługi API Management

Po zalogowaniu się do konto Microsoft w okienku Eksploratora *API Management* na platformie Azure zostaną wystawione subskrypcje platformy Azure.

Kliknij prawym przyciskiem myszy subskrypcję, której chcesz użyć, a następnie wybierz pozycję **utwórz API Management na platformie Azure**.

![Kreator tworzenia API Management w VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

W otwartym okienku Podaj nazwę nowego wystąpienia API Management. Musi ona być globalnie unikatowa na platformie Azure i zawierać 1-50 znaków alfanumerycznych i/lub łączników, a także rozpoczynać się od litery i kończyć znakiem alfanumerycznym.

Zostanie utworzone nowe wystąpienie API Management (i nadrzędna grupa zasobów) o określonej nazwie. Domyślnie wystąpienie jest tworzone w regionie *zachodnie stany USA* z użyciem jednostki SKU *zużycia* .

> [!TIP]
> W przypadku włączenia **zaawansowanego tworzenia** w *ustawieniach rozszerzenia API Management platformy Azure*można także określić [jednostkę SKU API Management](https://azure.microsoft.com/pricing/details/api-management/), [region platformy Azure](https://status.azure.com/en-us/status)i [grupę zasobów](../azure-resource-manager/management/overview.md) , aby wdrożyć wystąpienie API Management.
>
> Gdy jednostka SKU *zużycia* trwa krócej niż minutę, inne jednostki SKU zwykle zajmują 30-40 minut.

W tym momencie wszystko jest gotowe do zaimportowania i opublikowania pierwszego interfejsu API. Można to zrobić, a także wykonywać typowe operacje API Management w ramach rozszerzenia dla Visual Studio Code. Aby uzyskać więcej informacji, zobacz [Visual Studio Code rozszerzenie API Management](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) .

![Nowo utworzone wystąpienie API Management w okienku VS Code API Management rozszerzenie](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń wystąpienie API Management, klikając prawym przyciskiem myszy i wybierając pozycję **Otwórz w portalu** , aby [usunąć usługę API Management](get-started-create-service-instance.md#clean-up-resources) i jej grupę zasobów.

Alternatywnie można wybrać polecenie **usuń API Management** , aby usunąć tylko wystąpienie API Management (Ta operacja nie powoduje usunięcia grupy zasobów).

![Usuń wystąpienie API Management z VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)
