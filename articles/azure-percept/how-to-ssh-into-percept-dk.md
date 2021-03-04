---
title: Nawiązywanie połączenia z platformą Azure Percept DK za pośrednictwem protokołu SSH
description: Dowiedz się, jak za pomocą protokołu SSH do usługi Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096619"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Nawiązywanie połączenia z platformą Azure Percept DK za pośrednictwem protokołu SSH

Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować połączenie SSH z platformą Azure Percept [DK przy użyciu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)instrukcji wypełniania.

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer-host z systemem Windows, Linux lub OS X z możliwością Wi-Fi
- Klient SSH
    - Jeśli na komputerze hosta jest uruchomiony system [Windows, w](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) tym przewodniku zostanie użyty skuteczny klient SSH.
    - Jeśli na komputerze hosta jest uruchomiony system Linux lub OS X, usługi SSH są zawarte w tych systemach operacyjnych i można je uruchomić bez oddzielnej aplikacji klienckiej. Więcej informacji na temat sposobu uruchamiania usług SSH można znaleźć w dokumentacji produktu systemu operacyjnego.
- Azure Percept DK
- Skonfiguruj konto logowania SSH podczas [korzystania z platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>Inicjowanie połączenia SSH

1. Moc na platformie Azure Percept DK (dev Kit)

1. Jeśli Twój zestaw deweloperski jest już połączony z siecią za pośrednictwem sieci Ethernet lub Wi-Fi, przejdź do następnego kroku. W przeciwnym razie Podłącz komputer hosta bezpośrednio do punktu dostępu Wi-Fiego zestawu deweloperów, podobnie jak w przypadku łączenia się z innymi sieciami Wi-Fi:
    - **Nazwa sieci**: SCZ-xxxx (gdzie "XXXX" to ostatnie cztery cyfry adresu sieciowego Mac zestawu deweloperów)
    - **hasło**: można znaleźć na karcie powitalnej, która została dostarczona z zestawem deweloperskim

    > [!WARNING]
    > Po nawiązaniu połączenia z punktem dostępu do usługi Azure Percept Wi-Fi, komputer-host tymczasowo utraci połączenie z Internetem. Aktywne wywołania konferencji wideo, przesyłania strumieniowego sieci Web lub inne środowiska oparte na sieci będą przerywane do momentu zakończenia kroku 3 środowiska korzystania z platformy Azure Percept.

1. Otwórz program PuTTY. Wprowadź następujące polecenie i kliknij przycisk **Otwórz** , aby SSH w Devkit:

    1. Nazwa hosta: 10.1.1.1
    1. Port: 22
    1. Typ połączenia: SSH

    > [!NOTE]
    > **Nazwa hosta** jest adresem IP Twojego urządzenia. Jeśli Twój zestaw deweloperski jest połączony z punktem dostępu Wi-Fi zestawu deweloperskiego, adres IP będzie 10.1.1.1. Jeśli Twój zestaw deweloperski jest połączony za pośrednictwem sieci Ethernet, Użyj lokalnego adresu IP urządzenia, które można pobrać z routera lub koncentratora sieci Ethernet. Jeśli urządzenie jest połączone za pośrednictwem sieci Wi-Fi, należy użyć adresu IP, który został dostarczony podczas [korzystania z platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Image.":::

1. Zaloguj się do terminalu potoku przy użyciu nazwy użytkownika i hasła SSH utworzonego podczas korzystania z platformy.

## <a name="next-steps"></a>Następne kroki

Po pomyślnym nawiązaniu połączenia z usługą Azure Percept DK za pośrednictwem protokołu SSH można wykonać różne zadania, w tym Rozwiązywanie problemów, aktualizacje USB i uruchamianie narzędzia DiagTool lub SoftAP.