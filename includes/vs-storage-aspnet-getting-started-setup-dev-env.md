---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183132"
---
## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

Ta sekcja zawiera instrukcje dotyczące konfigurowania środowiska deweloperskiego. Obejmuje to tworzenie aplikacji ASP.NET MVC, Dodawanie połączenia usługi połączonej, dodawanie kontrolera i określanie wymaganych dyrektyw przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Tworzenie projektu aplikacji ASP.NET MVC

1. Otwórz program Visual Studio.

1. Z menu głównego wybierz pozycję **plik** > **Nowy** > **projekt**.

1. W oknie dialogowym **Nowy projekt** wybierz pozycję **Web** > **ASP.NET Web Application (.NET Framework)**. W polu **Nazwa** Określ **StorageAspNet**. Wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe nowego projektu](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET** wybierz pozycję **MVC**, a następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa aplikacja sieci Web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Łączenie się z kontem usługi Azure Storage przy użyciu usług połączonych

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.

1. Z menu kontekstowego wybierz pozycję **Dodaj** > **podłączoną usługę**.

1. W oknie dialogowym **połączone usługi** wybierz pozycję **Magazyn w chmurze z usługą Azure Storage**.

    ![Zrzut ekranu przedstawiający okno dialogowe usługi połączone](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. W oknie dialogowym **Azure Storage** wybierz konto usługi Azure Storage, które ma być używane w tym samouczku. Aby utworzyć nowe konto usługi Azure Storage, wybierz pozycję **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejącego konta magazynu lub utworzeniu nowego, wybierz pozycję **Dodaj**. Program Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i parametry połączenia magazynu z **Web. config**.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **zależności**, wybierz polecenie **Zarządzaj pakietami NuGet**i Dodaj odwołanie do pakietu NuGet do najnowszej wersji Microsoft. Azure. ConfigurationManager.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu przy użyciu [Azure Portal](https://portal.azure.com), zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Konto magazynu można również utworzyć przy użyciu [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [interfejsu wiersza polecenia platformy Azure](../articles/storage/common/storage-azure-cli.md)lub [Azure Cloud Shell](../articles/cloud-shell/overview.md).
